# express-mongodb
  具体运行请移步博客http://www.speedcode.cn/ArticleDetail?id=127

  如果你想在后台同学敷衍你接口改起来麻烦，不想改的时候，光速打脸对方；如果你有自己想操作数据库，及时删库跑路也没人找你麻烦的冲动；如果你想前后端分离在你一个人身上就可以实现，本篇文章将会让你向全栈进阶，“挑衅同事”，“报复同事”，迈出一大步。

  技术栈：express + mongodb+mongoose 实现web接口。

  前端：jq + boostrap（洪斌还鄙视我👎）

  系统:macOs 10.14.1

  在正式进入到项目之前，先简单的介绍一下所用到的技术栈的内容。

  

  Express 框架

 Express 简介

  Express 是一个简洁而灵活的 node.js Web应用框架, 提供了一系列强大特性帮助你创建各种 Web 应用，和丰富的 HTTP 工具。使用 Express 可以快速地搭建一个完整功能的网站。Express 框架核心特性：

可以设置中间件来响应 HTTP 请求。

定义了路由表用于执行不同的 HTTP 请求动作。

可以通过向模板传递参数来动态渲染 HTML 页面。

 安装 Express


 npm install express --save

MongoDB 
MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。



Mongoose
Mongoose是在node.js异步环境下对mongodb进行便捷操作的对象模型工具

安装 Mongoose

npm install mongoose
 

我似乎发现了问题所在，为什么每个用schema创建的collection在可视化工具里都是复数命名

于是我去翻查api

Mongoose#model(name, [schema], [collection], [skipInit])

在官方的api文档里面有解释（我不听，我不听，我不听。。。。。。。）

When no collection argument is passed, Mongoose produces a collection name by passing the model name to the utils.toCollectionName method. This method pluralizes the name. If you don’t like this behavior, either pass a collection name or set your schemas collection name option.



 意思就是：

当没有传入collection参数时，Mongoose会通过model name（就是第一个参数），调用utils.toCollectionName方法产生一个collection name，而这个方法会使name变成复数形式。如果你不想要这个过程，只要传入collection name参数或设置Schema中的collection name选项。



什么坑爹的设定！！！！！！！！！



