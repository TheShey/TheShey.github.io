---
layout: post
title: okio简化处理Android I/O操作
categories: [Android]
tags: [Java]
fullview: false
---

Okio库是一个由square公司开发的，它补充了Java.io和java.nio的不足，以便能够更加方便，快速的访问、存储和处理你的数据。而OkHttp的底层也使用该库作为支持。而在开发中，使用该库可以大大给你带来方便。

目前，Okio的最新版本是1.6.0，gradle的引用如下

```
compile 'com.squareup.okio:okio:1.6.0'
```

Okio中有两个关键的接口，Sink和Source，这两个接口都继承了Closeable接口；而Sink可以简单的看做OutputStream，Source可以简单的看做InputStream。而这两个接口都是支持读写超时设置的。结构图如下:   

![2367](http://img.blog.csdn.net/20160115144102367)

它们各自有一个支持缓冲区的子类接口，BufferedSink和BufferedSource，而BufferedSink有一个实现类RealBufferedSink,BufferedSource有一个实现类RealBufferedSource；此外，Sink和Source它门还各自有一个支持gzip压缩的实现类GzipSink和GzipSource；一个具有委托功能的抽象类ForwardingSink和ForwardingSource；还有一个实现类便是InflaterSource和DeflaterSink，这两个类主要用于压缩，为GzipSink和GzipSource服务；整体的结构图如下:   
![2367](http://img.blog.csdn.net/20160115144113403)   

BufferedSink中定义了一系列写入缓存区的方法，比如write方法写byte数组，writeUtf8写字符串，还有一些列的writeByte，writeString，writeShort，writeInt，writeLong，writeDecimalLong等等方法；

BufferedSource定义的方法和BufferedSink极为相似，只不过一个是写一个是读，基本上都是一一对应的，如readUtf8，readByte，readString，readShort，readInt等等等等。这两个接口中的方法有兴趣的点源码进去看就可以了。

而这两个支持缓冲区的接口的实现类RealBufferedSink和RealBufferedSource都是通过包装一个Sink+Buffer或者Source+Buffer来进行实现的。如下图所示:   
![2367](http://img.blog.csdn.net/20160115144210193)   

拿RealBufferedSink来举例，实际调用的write的一系列方法，都是直接的对成员变量buffer进行的操作，当写入buffer成功后，最后会调用一个方法将buffer中的内容写入到sink中去，我们随便拿一个方法看一下:   

```
public BufferedSink writeLong(long v) throws IOException {
       if(this.closed) {
           throw new IllegalStateException("closed");
       } else {
           this.buffer.writeLong(v);
           return this.emitCompleteSegments();
       }
   }
```


可以看到，首先会判断closed成员变量是否是标记着关闭，如果已经关闭了则扔出一个异常，否则将内容写入到buffer，写入完成后调用了一个emitCompleteSegments的方法，该方法中做了什么呢,没错，就是将buffer中的内容写入到sink成员变量中去，然后将自身返回。

```
public BufferedSink emitCompleteSegments() throws IOException {

       if(this.closed) {
           throw new IllegalStateException("closed");
       } else {
           long byteCount = this.buffer.completeSegmentByteCount();
           if(byteCount > 0L) {
               this.sink.write(this.buffer, byteCount);
           }
           return this;
       }

   }
```
这两个实现类的内部的所有方法都是类似的，这里不一一展开。

而这一切的背后都是一个叫做Buffer的类在支持着缓冲区,Buffer是BufferedSink和BufferedSource的实现类，因此它既可以用来读数据，也可以用来写数据，其内部使用了一个Segment和SegmentPool，维持着一个链表，其循环利用的机制和Android中Message的利用机制是一模一样的。   

```
final class SegmentPool {

    static final long MAX_SIZE = 65536L;
    static Segment next;
    static long byteCount;

    private SegmentPool() {
    }

    static Segment take() {
        Class var0 = SegmentPool.class;
        synchronized(SegmentPool.class) {
            if(next != null) {
                Segment result = next;
                next = result.next;
                result.next = null;
                byteCount -= 2048L;
                return result;
            }
        }
        return new Segment();
    }

    static void recycle(Segment segment) {
        if(segment.next == null && segment.prev == null) {
            if(!segment.shared) {
                Class var1 = SegmentPool.class;
                synchronized(SegmentPool.class) {
                    if(byteCount + 2048L <= 65536L) {
                        byteCount += 2048L;
                        segment.next = next;
                        segment.pos = segment.limit = 0;
                        next = segment;
                    }
                }
            }
        } else {
            throw new IllegalArgumentException();
        }
    }
}
```   

内部一个成员变量next指向链表下一个元素，take方法首先判断池中是否存在可用的，存在则返回，不存在则new一个，而recycle则是将不再使用的Segment重新扔到池中去。从而达到一个Segment池的作用。

而Okio暴露给外部使用的类便是Okio这个类，其内部有大量的静态方法,包括通过一个Source获得BufferedSource，通过一个Sink获得一个BufferedSink。这个过程很简单，我们调用Okio的buffer方法即可返回我们需要的，如下:  

```
Okio.buffer(sink)
Okio.buffer(source)
```   
但是上面两个方法需要传递一个Sink或者Source，那么这个Sink和Source又是如何获得的呢。其实方法也在Okio这个类中。我们可以调用sink方法获得一个Sink，调用source方法获得一个Source，而数据的来源或者目的可以是一个File，一个输入或者输出流，一个Socket链接等等。如下:    

```
Okio.sink(new File("***"));
Okio.sink(new FileOutputStream(new File("***")));
Okio.sink(new Socket("***",8888));
Okio.source(new File("***"));
Okio.source(new FileInputStream(new File("***")));
Okio.source(new Socket("****",8888));
```      

这样你可能还不过瘾，那么让我们连起来应用一下，现在我们从本地读一个文件，读完后再往另一个文件中写入内容:    


```
public static void main(String[] args) {
    Source source = null;
    BufferedSource bufferedSource = null;
    try {
        File file = new File("resources/test.txt");
        source = Okio.source(file);
        bufferedSource = Okio.buffer(source);
        String content = bufferedSource.readUtf8();
        System.out.println(content);
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        closeQuietly(bufferedSource);
    }
    Sink sink = null;
    BufferedSink bufferedSink = null;
    try {
        File dest = new File("resources/dest.txt");
        sink = Okio.sink(dest);
        bufferedSink = Okio.buffer(sink);
        bufferedSink.writeUtf8("11111111111");
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        closeQuietly(bufferedSink);
    }
}


public static void closeQuietly(Closeable closeable) {
    if (closeable != null) {
        try {
            closeable.close();
        } catch (RuntimeException rethrown) {
            throw rethrown;
        } catch (Exception ignored) {
        }
    }

}
```    
或许有时候网络请求中，我们需要使用到Gzip的功能，那么，我们可以简单的使用一下gzip的功能:    

```
public static void main(String[] args) {

    Sink sink = null;
    BufferedSink bufferedSink = null;
    GzipSink gzipSink=null;
    try {
        File dest = new File("resources/gzip.txt");
        sink = Okio.sink(dest);
        gzipSink=new GzipSink(sink);
        bufferedSink = Okio.buffer(gzipSink);
        bufferedSink.writeUtf8("android vs ios");
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        closeQuietly(bufferedSink);
    }

    Source source = null;
    BufferedSource bufferedSource = null;
    GzipSource gzipSource=null;

    try {
        File file = new File("resources/gzip.txt");
        source = Okio.source(file);
        gzipSource=new GzipSource(source);
        bufferedSource = Okio.buffer(gzipSource);
        String content = bufferedSource.readUtf8();
        System.out.println(content);
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        closeQuietly(bufferedSource);
    }

}

public static void closeQuietly(Closeable closeable) {
    if (closeable != null) {
        try {
            closeable.close();
        } catch (RuntimeException rethrown) {
            throw rethrown;
        } catch (Exception ignored) {
        }
    }
}

```   

验证是否正确的方法便是查看该写入的文件是否是乱码，以及读出来是否是原来的字符串。

对比一下原来的gzip压缩与解压缩的方式，你就会发现还是简单了不少的:


```
public class GzipUtil {
    /**
     * GZIP压缩
     *
     * @param data
     * @return
     */
    public static byte[] gzip(byte[] data) throws Exception {

        if (data == null || data.length == 0) {
            return null;
        }

        ByteArrayOutputStream out = new ByteArrayOutputStream();
        GZIPOutputStream zos;
        BufferedInputStream bis = new BufferedInputStream(new ByteArrayInputStream(data));
        byte[] buf = new byte[512];
        int len;

        try {
            zos = new GZIPOutputStream(out);
            while ((len = bis.read(buf)) != -1) {
                zos.write(buf, 0, len);
                zos.flush();
            }
            bis.close();
            zos.close();
            return out.toByteArray();
        } finally {
            if (out != null) {
                try {
                    out.close();
                } catch (Exception e2) {
                }
            }
        }
    }


    /**
     * Gzip解压缩
     * @param b
     * @return
     */
    public static byte[] unGzip(byte[] b) {

        if (b == null || b.length == 0) {
            return null;
        }

        ByteArrayOutputStream out = new ByteArrayOutputStream();
        ByteArrayInputStream in = new ByteArrayInputStream(b);

        try {
            GZIPInputStream gunzip = new GZIPInputStream(in);
            byte[] buffer = new byte[256];
            int n;
            while ((n = gunzip.read(buffer)) >= 0) {
                out.write(buffer, 0, n);
            }
            return out.toByteArray();
        } catch (IOException e) {
            Log.e(WDCore.getInstance().getConfiguration().getLogTag(), "uncompress error", e);
        } finally {
            try {
                if (out != null) {
                    out.close();
                }
                if (in != null) {
                    in.close();
                }
            } catch (Exception e2) {
            }
        }
        return null;
    }
}
```   

此外还有一个ByteString类，这个类可以用来做各种变化，它将byte转会为String，而这个String可以是utf8的值，也可以是base64后的值，也可以是md5的值，也可以是sha256的值，总之就是各种变化，最后取得你想要的值。
