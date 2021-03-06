---
title: 译见｜不可变基础设施与一次性组件
---

<!-- reviewed by fiona -->

![](http://7xi8kv.com5.z0.glb.qiniucdn.com/yijian-6-1.jpg)

> 译见系列｜DaoCloud 现推出「译见」系列，每周为开发者提供国外精品译文，主要关注云计算领域的技术和前沿趋势。由 Fiona 翻译。

#### 译者注

2013年6月，Chad 在自己的博客中撰写一篇 ***Trash Your Servers and Burn Your Code: Immutable Infrastructure and Disposable Components*** 的文章，提出了 **Immutable Infrastructure** 的概念。这一前瞻性的构想，伴随着 Docker 容器技术的兴起、微服务架构的流行，得到了事实上的检验。时隔两年，我们不妨再温故而知新。

#### 作者简介

![](http://7xi8kv.com5.z0.glb.qiniucdn.com/yijian-6-2.png)

Chad Fowler ，热情的 Ruby/Rails 信徒，活跃于各种技术大会上，知名 TODO 软件公司 6Wunderkinder 的 CTO。他是 *Rails Recipes*、*The Passionate Programmer: Creating a Remarkable Career in Software Development*、 *Programming Ruby*、 *The Ruby Way*、 *Professional Apache Tomcat* 等技术书籍的作者；同时也是 The International Ruby Conference 和 RailsConf 等技术大会的组织者。

---

作为一名开发者和兼职系统管理员，我遇到过的最可怕的事情就是一台运行多年的服务器历经多次系统和应用软件的升级。

![](http://7xi8kv.com5.z0.glb.qiniucdn.com/yijian-6-3.jpg)

为什么？因为老系统终将大而不当，当遇上中断时我们得古法修复。尽管对配置文件的一次快速修改能挽救你的一天，但往往是漫长的拉锯战后我们好不容易能去睡觉，却不得不警醒：

> 过一会我们还要把这文件放回 Chef 里。

此外，多年累积的问题会意想不到地爆发，系统运行日渐晦涩，关键功能只有一人了解。正常的部署是一个「直接基于代码控制系统」的流程，而他们的应用代码的部署是在这个流程之外完成的。

系统变得过分繁琐，只能使用某种手动部署。除非使用非常规的特殊方法，否则初始化脚本也不能工作。

当然，最好的情况是操作系统已经打了无数次补丁，符合标准的操作流程，但仍不可避免地出现问题。最糟糕的情况就是系统从未打过补丁，一旦有所动作，你也不知道后果如何。

系统变得岌岌可危。你害怕做任何改动；因为对其工作原理并不了解，你也不敢替换它。

为了避免种种困境，在过去的多年里，遵循自动化原则，我们多方设法。最后我们在工作中引入了「不可变部署」这一概念。

身处软件业的我们开始意识到软件架构中「不可变」带来的好处。我们可以看到，过去几年里，对函数编程技术的兴趣日渐增多，也促进了 Erlang、 Scala、 Haskell 和 Clojure 等语言的流行。函数式语言提供了不可变数据结构和单一任务变量。我们很多人仅根据已有经验就相信，「不可变」将会让程序更合理，也更难被搞砸。

那为什么不把这个方法应用到基础设施中呢？如果你非常肯定系统是自动化创建，并且从创建之时起就不会进行改动，那我上文提及的大多数问题就不复存在。

> 需要升级？
- 没问题！重新构建一个全新的升级版系统，将旧版本抛诸脑后。

> 修正应用？
- 也是如此。构建一个新版本的服务器或者镜像，把旧的丢弃。

在 6Wunderkinder ，我们在过去的四个月里（注：本文写于 2013 年 6 月）一直实践此方法。我们有信心进行后端基础设施的快速迭代，让我们的产品开发更快，对我们的用户来说更容易扩展和更可靠，也更加灵活、自由地迁移应用。

更有价值的是，作为一个新的编程范式，基础设施这一视角从根本上改变了我对系统的看法。新模式的涌现，不仅改变了我对部署的认识，也改变了我对应用代码和团队结构的看法。

这个方法来对我来说非常适用。显然我们不是想到这个方法的第一人，还有很多需要学习的地方。这里面也包含了某些「云」基础设施，我认为现代软件架构已经普遍采用了。