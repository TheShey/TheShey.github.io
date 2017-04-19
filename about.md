---
layout: page
title: 加入天猫
permalink: /about/
---

天猫前端团队负责天猫所有的Web前端开发，这意味着只要通过Web访问天猫，那都是我们的作品，请以挑剔的眼光去看待它们，欢迎提出任何问题。这次实习意味着你的代码会运行在天猫上，供亿万人使用，你还能告诉父母、恋人、同学和朋友，TA正在享受你编写的代码来进行购物。如果实习时间足够，你还会经历独一无二的双11，体会近亿人同一时刻涌上天猫带来的挑战和刺激，这会对代码在健壮性、性能和模块化及可维护性等上提出非常苛刻的要求。

随着移动互联网的发展，移动已经成为用户的首要状态，移动开发能力已是前端的必备要求，这是继ajax提出之后对于前端而言最激动人心最大的改变，我们幸运赶上了转变。所以除了HTML、CSS和JavaScript这些传统的前端技能必须扎实之外，Native、动画（虚拟现实）和Nodejs的开发能力也成为前端的必备，所以天猫前端团队非常早就提出并实践推动了跨终端的前端工程师（http://www.infoq.com/cn/news/2013/01/qcon-beijing-web-interview ），这里有一份我4月份在成都大学的分享来介绍关于跨终端的前端工程师我们为什么做，做什么（http://tmallfe.github.io/slide/cross-end-fe.pdf ），分享给你。

至于天猫前端怎样，欢迎去知乎去讨论（http://www.zhihu.com/question/22933704 ），这里我要和你谈谈天猫前端团队的理念和设计思想，这些思考和想法至少会悄悄地贯穿你在天猫的整个实习期：

### 我们追求的设计思想：
0. **弱中心**：SVN是强中心，Git就是弱中心，弱中心是强调每个人独立自主，不依赖的他人，有自己的思考和行动力，之所以还存在中心的原因就是强调交换、协同和共建，如果还没有玩过Git，那就赶快去https://github.com/ 玩玩吧
0. **细粒度**：虽然天猫业务是一个整体，但我们会把天猫业务和技术拆分成若干个独立发布单元，每人负责1个或多个单元，减少彼此之间的耦合，降低冲突和提高效率，Git也正是这个思想。另外一层含义是提倡化整为零，从小事做起，比如你想写一本技术书，可以先从写一篇技术blog开始，如果尚未开始写技术blog，那么先从在github上写个demo开始。
0. **扁平化**：当今所有的OS系统都在向扁平化演变，这样做的目的是让用户在不同环境下快速地获得想要的，同样我们的技术架构、团队组织和实现的产品，同样在向扁平化方向发展，比如天猫前端团队组织架构和技术架构都是两层结构，足够扁平。
0. **跨平台**：
     *  Web本来就是一种跨平台的解决方案，前端职位的出现得益于Web的大规模发展，所以前端的骨子里就流着跨平台的血液，[渐进增强和优雅退化](http://ued.taobao.org/blog/2008/10/understanding-progressiveen-hancement-chs-translation/)是基础思想。面对复杂环境，在PC上，一个前端要熟悉HTML、CSS和JavaScript及Flash，才能做到好体验真正地跨浏览器，而今天消费者的终端碎片化，分布到花样百出的各种设备上，此时渐进增强和优雅退化依旧是基础思想，一个前端要熟悉传统的HTML、CSS和JavaScript技术外，还要熟悉Native、动画和Nodejs才能够做到真正意义好体验的跨终端。
     * 作为一个技术人员，管理能力不是职位，是基本素质。不了解交互的开发不是好前端。

### 我们思考技术架构的角度：
0. **简单**：
    * 抱歉，我这个欢迎邮件写的很啰嗦繁杂，反面典型，我的功力还不够，需要继续向这个方向努力，欢迎    提供建议，如何写的简单。
    * 简单的东西才能长久，HTML、CSS和JavaScript之所以能够活到现在，而其他类似的很牛的方案都死掉了，原因之一是简单，才有那么多人用它，所以我们需要把技术和产品方案朝着简单的思路发展，简单才是本质，复杂一定是临时的会过时的。天猫的前端技术架构为什么都基于Kissy，为什么是两层架构，就是朝着简单的方式去思考。看起来简单，用起来简单。
0. **高效**：简单是高效的前提，复杂的高效都是临时的会过时的，技术架构一定要提高团队的工作效率，否则一定会被抛弃，因此把简单的规则自动化，把精确的重复的事情让机器去做，前端这么多年为什么开发环境不够成熟，就是自动化的工具太少，前端又很少能力驾驭编写工具的语言，而Nodejs的出现是一个前所未有的机会。
0. **灵活**：高效往往和灵活是对立的，就像移动上Native和Web的关系，而我们就需要思考如何做到两者兼顾，既高效又灵活，所以要不断把事情做简单，思考本质、看到本质，基于本质去实现。比如Apple为什么敢于把鼠标和键盘去掉，是因为确信人直接和界面打交道比借助一个中间硬件更能够表达人机交互的本质。
0. **新鲜**：面向未来，前端需要不停地更新自己，无论是思想还是技术。比如整个天猫基于Kissy，那么就使用最新的Kissy版本，基础设施能够升级是一种能力，如果有一天基础设施升不了啦，那么这套技术架构就老去了。比如发现Gulp比Grunt更能够代表未来，那么我们毫不犹豫地整个团队开始进行升级。

### 我们希望团队的状态：
0. **健康**：至少要身体健康，比如是否有健身习惯，如果没有，开始培养一个吧
0. **乐观**：当遇到问题，是看到困难退缩，还是看到机会去挑战，后者就是乐观
0. **积极**：当心中有想法、疑问或建议时，是放到心里，还是用说或写等方式向对方表达出来
0. **主动**：当发现问题，也有解决方案，是否勇于行动，勇于承担责任

### 我们提供的前端开发服务：
0. **可用性高**：跨浏览器、跨终端且健壮地能够经受双11这样的大流量的冲击
0. **速度快**：要快速地做出快速的前端产品，所以我们在工作量大时间紧的情况还是把天猫首页做到同类型网站最快
0. **有惊喜**：做好自己工作的同时，主动为用户和团队创造惊喜，比如让你做的业务速度最快，设计了一个工具提高了大家的工作效率，做的动画让消费者惊艳，努力实现让残障人士也友好访问天猫......

是不是看起来有点晕？不用担心，你是我们精心挑选出来的，邀请来实习表示认可你过去的努力和所取得的成绩，也非常看好你的未来，所以我们会给你配备优秀的天猫前端作为师兄，全方位从技术、项目和生活来协助你，内部称之为“新人成长计划”，有3个目标：

0. **协助新人快速的融入团队、业务和公司**
0. **培养师弟，成长师兄**：每个新人都会交给师兄全权负责，这对师兄无论从技术上还是管理上都是巨大挑战，这是培养优秀的复合型人才的开始，有些师兄就是去年的优秀应届生，未来你们也可以
0. **这个新人的痛苦不要成为下个新人痛苦**：每个人到一个新环境都会有些不适应，我们提倡乐观地面对这些挑战，看成是成长的机会，积极地表达出来，并思考造成这些痛苦的原因，把某些可以改变的地方主动地改造它，比如发现某个工具不好用，用更好的想法和动手去改造它，这是非常赞的事情。

### 加入天猫

对加入天猫前端团队有意向的，可以发简历到join-tmallfe@list.alibaba-inc.com，招聘要求见：https://job.alibaba.com/zhaopin/position_detail.htm?positionId=3504

### 工作环境

![img_8050](https://cloud.githubusercontent.com/assets/7637440/3065114/cafd9216-e25d-11e3-83ab-ce4b8484a82e.jpg)
![img_8053](https://cloud.githubusercontent.com/assets/7637440/3065113/cafd8848-e25d-11e3-9fdb-4f6392193428.jpg)
![img_8796](https://cloud.githubusercontent.com/assets/7637440/3065116/cafebb5a-e25d-11e3-823f-36f17c184db2.jpg)
![img_8920](https://cloud.githubusercontent.com/assets/7637440/3065112/cafd707e-e25d-11e3-9c3f-9f2a8ce57a82.jpg)
![img_8937](https://cloud.githubusercontent.com/assets/7637440/3065117/caff5286-e25d-11e3-9d7b-6a9a90081e76.jpg)
![img_9622](https://cloud.githubusercontent.com/assets/7637440/3065118/cb2caeca-e25d-11e3-9e15-0d0178ccc036.jpg)
![img_9629](https://cloud.githubusercontent.com/assets/7637440/3065119/cb2cb03c-e25d-11e3-87e0-7ae8f90f81de.jpg)
![img_9571](https://cloud.githubusercontent.com/assets/7637440/3065120/cb2ebefe-e25d-11e3-858a-10482803238f.jpg)
![img_8538](https://cloud.githubusercontent.com/assets/7637440/3065115/cafdd79e-e25d-11e3-8f81-9c53e80ac4f7.jpg)
![img_9722](https://cloud.githubusercontent.com/assets/7637440/3065121/cb2f22e0-e25d-11e3-903e-311cce422f24.jpg)