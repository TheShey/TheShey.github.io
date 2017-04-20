---
layout: post
title: RxBinding
categories: [Android]
tags: [Android]
fullview: true
---

RxBinding是Jake Wharton的一个开源库,它提供了一套在Android 平台上的基于 RxJava的Binding API,所谓Binding,就是类似设置OnClickListener、设置TextWatcher 这样的注册绑定对象的API.


### git地址   
---
[https://github.com/JakeWharton/RxBinding](https://github.com/JakeWharton/RxBinding)

### 引用使用   
---
```gradle
dependencies {
    compile 'com.jakewharton.rxbinding:rxbinding:0.4.0'
    compile 'com.jakewharton.rxbinding:rxbinding-support-v4:0.4.0'
    compile 'com.jakewharton.rxbinding:rxbinding-appcompat-v7:0.4.0'
    compile 'com.jakewharton.rxbinding:rxbinding-design:0.4.0'
}
```

### 示例   
---

#### Button防抖处理
```gradle
button = (Button) findViewById(R.id.bt);
     RxView.clicks(button)
             .throttleFirst(2,TimeUnit.SECONDS)//两秒钟之内只取一个点击事件防抖操作
             .subscribe(new Action1<Void>() {
                 @Override
                 public void call(Void aVoid) {
                     Toast.makeText(MainActivity.this, "点击了", Toast.LENGTH_SHORT).show();
                 }
             }) ;
```

#### 按钮的长按事件监听
```gradle
button = (Button) findViewById( R.id.bt );
 //监听长按事件
 RxView.longClicks( button)
      .subscribe(new Action1<Void>() {
          @Override
         public void call(Void aVoid) {
         Toast.makeText(MainActivity.this, "long click  ！！", Toast.LENGTH_SHORT).show();
         }
     }) ;
```

#### ListView的点击事件、长按事件处理
```gradle
listView = (ListView) findViewById( R.id.listview );
 List<String> list = new ArrayList<>() ;
     for ( int i = 0 ; i < 40 ; i++ ){
         list.add( "sss" + i ) ;
     }
 final ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,android.R.layout.simple_expandable_list_item_1);
 adapter.addAll( list );
 listView.setAdapter( adapter );
 //item click event
 RxAdapterView.itemClicks( listView )
     .subscribe(new Action1<Integer>() {
         @Override
         public void call(Integer integer) {
         Toast.makeText(ListActivity.this, "item click " + integer , Toast.LENGTH_SHORT).show();
             }
         }) ;
 //item long click
 RxAdapterView.itemLongClicks( listView)
     .subscribe(new Action1<Integer>() {
         @Override
         public void call(Integer integer) {
             Toast.makeText(ListActivity.this, "item long click " + integer , Toast.LENGTH_SHORT).show();
             }
         }) ;
```

#### 用户登录界面,勾选同意隐私协议,登录按钮就变高亮
```gradle
button = (Button) findViewById( R.id.login_bt );
checkBox = (CheckBox) findViewById( R.id.checkbox );
RxCompoundButton.checkedChanges( checkBox )
    .subscribe(new Action1<Boolean>() {
        @Override
        public void call(Boolean aBoolean) {
            button.setEnabled( aBoolean );
            button.setBackgroundResource( aBoolean ? R.color.button_yes : R.color.button_no );
            }
        }) ;
```

#### 搜索的时候,关键词联想功能,debounce()在一定的时间内没有操作就会发送事件
```gradle
 editText = (EditText) findViewById( R.id.editText );
 listView = (ListView) findViewById( R.id.listview );
 final ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,android.R.layout.simple_expandable_list_item_1);
 listView.setAdapter( adapter );
 RxTextView.textChanges( editText )
             .debounce( 600 , TimeUnit.MILLISECONDS )
             .map(new Func1<CharSequence, String>() {
                 @Override
                 public String call(CharSequence charSequence) {
                     //get the keyword
                     String key = charSequence.toString() ;
                     return key ;
                 }
             })
             .observeOn( Schedulers.io() )
             .map(new Func1<String, List<String>>() {
                 @Override
                 public List<String> call(String keyWord ) {
                     //get list
                     List<String> dataList = new ArrayList<String>() ;
                     if ( ! TextUtils.isEmpty( keyWord )){
                         for ( String s : getData()  ) {
                             if (s != null) {
                                 if (s.contains(keyWord)) {
                                     dataList.add(s);
                                 }
                             }
                         }
                     }
                     return dataList ;
                 }
             })
             .observeOn( AndroidSchedulers.mainThread() )
             .subscribe(new Action1<List<String>>() {
                 @Override
                 public void call(List<String> strings) {
                     adapter.clear();
                     adapter.addAll( strings );
                     adapter.notifyDataSetChanged();
                 }
             }) ;
```

