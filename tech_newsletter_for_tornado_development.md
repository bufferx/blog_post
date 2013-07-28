# Tornado开发

应[荣哥](http://github.com/jacky098123/)相邀写些关于Tornado的东西.

本文使用[Markdown](http://en.wikipedia.org/wiki/Markdown)语法,
文档源码[在此](https://github.com/bufferx/blog_post/blob/master/tech_newsletter_for_tornado_development.md).

## Tips
- 本文不是Tornado入门
- 一篇开发实践Slides
- 两个实际CASE
- 正在用Tornado做啥

Talk is cheap, Show me the code.
故本文不做长篇的文字分析, 更多的是提供一些源码展现与解读.

## 实践

- 去年分享[Slides](https://docs.google.com/a/kuxun.com/presentation/d/1rf_wUpNQaxVide5s2wmo3lNP-6V3SZiUBLksxw1JtdE/edit#slide=id.p),
  有更新

## 两个CASE

### PipeLine方式访问后端多个服务

传统阻塞的方式需要依次向后端服务请求N个请求, 最大响应时间是N个请求之和.
Tornado中统一了generator与Epoll事件处理, 可以依次发送多个REQUEST,
而无须等待前置REQUEST完成, 使最大响应时间为N个请求中最大响应时间(当然,
事件循环原因, 会略有损耗).

[CODE](https://gist.github.com/bufferx/5494125)
[gevent实现与Tornado的区别](http://idupx.blogspot.com/2013/05/async-batch-httprequest-base-on-tornado.html)

### Bugfix: asyncmongo blocking connection

#### 故障
mongodb所在机器宕机, 阻塞了整个线上服务

#### 现象
mongodb service服务宕机时, 阻塞IO连接会很快(毫秒级)返回
mongodb 所部署服务器宕机时, 阻塞IO会等待3秒返回

#### 原因
asyncmongo使用阻塞方式与mongodb service建立连接,机器宕机时(Error: no route to
host),时间可能消耗在路由查找中

#### 解决
使用tornado.IOStream建立非阻塞连接, 并加入超时机制
已提交[github pull request](https://github.com/bitly/asyncmongo/pull/59)

## 正在做的项目

### KPNS
- 全称: Kuxun Push Notification Service
- 简介: 向移动终端推送消息, 并保持与终端的持续连接
- 并发: 500K
- 协议: KMPP, 基于TCP的应用层自定义二进制协议

## 我眼中的Tornado
- Async Network Library
- HTTP SERVER
- Web Framework

## 一些数据
- 与php-cgi相比,进程数由上千个降低到十余个,减少进程切换开销,有效利用CPU,降低系统负载
- 减少网络连接(与后端服务采用长连接方式)
- 线上服务QPS峰值可达8k,可cover目前业务量(3k),延迟小于20ms,响应时间小于1s占比达到4个9
- 轻松支撑千万级服务
- 使用[PyPy](http://pypy.org/)可有效提升性能, 短连接: +30%, 长连接: 300%
- 目前流行后端服务(存储/HTTPSERVER/消息队列)等均有相应的库或框架支持

## 一些感想
- 轻量,简单,非阻塞事件触发框架的良好典范
- 深入源码的过程也是深入学习Python的过程
- 易于上手,开发效率高,健壮性好,性价比高
- 方便DIY,在其上实现自己私有协议服务
- 有良好的开源社区支持,也可以随时贡献开源社区
- 周边有良好的生态系统, 常用后端服务均有相应的库或框架
- 可以支撑目前公司业务量, 值得推广

## 推而广之
- gevent, 未测, 据说比tornado性能好
- nginx + lua, 基本上可以cover住当前业务, 性能是tornado的3倍(裸测)

## Quick Links
- [Documentation](http://www.tornadoweb.org/en/stable/)
- [Mailing
  List](https://groups.google.com/forum/?fromgroups#!forum/python-tornado)
- [Issue Tracker](https://github.com/facebook/tornado/issues)
- [Additional Resource](https://github.com/facebook/tornado/wiki/Links)

# About ME

## Social

- Sector: 研发 
- Blog: [http://idupx.blogspot.com/](http://idupx.blogspot.com/)
- Github: [@bufferx](https://github.com/bufferx)
- Twitter: [@bf4x](https://twitter.com/bf4x)

## Tech
- 工作经历中使用过多种编程语言及开发环境
- 近两年工作语言使用Python, 使用Python最多的是写Tornado相关的东西
- 其实我喜欢C
- 对于网络服务开发, 垂直搜索后端架构方面有点儿心得

# 后记
- 这是篇内部技术Newsletter
- Tech News
- 当时邮件组讨论激烈,进一步扩大了Python及Tornado的普及和使用

欢迎交流 :)
