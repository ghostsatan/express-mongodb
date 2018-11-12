# express-mongodb
{,…} http_admin_speedcode_cn_index_php_admin_article_add_htmlcontainer-drafts-data: "

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
 

mongodb 免费可视化关系工具 Robo 3T
image.png

Schema
schema是mongoose里会用到的一种数据模式，可以理解为表结构的定义；每个schema会映射到mongodb中的一个collection，它不具备操作数据库的能力。

下面我们定义一个list的Schema

var ListSchema = new mongoose.Schema({
user_id: String, //定义一个属性user_id，类型为String
content: String, //定义一个属性content，类型为String
updated_at: Date //定义一个属性updated_at，类型为Date
});
Model
定义好了Schema，接下就是生成Model。

model是由schema生成的模型，可以对数据库的操作，我们对上面的定义的user的schema生成一个User的model并导出，修改后代码如下：

//定义Listschema数据模式
var ListSchema = new mongoose.Schema({
user_id: String, //定义一个属性user_id，类型为String
content: String, //定义一个属性content，类型为String
updated_at: Date //定义一个属性updated_at，类型为Date
});

mongoose.model('user', ListSchema); //将该Schema发布为Model,user就是集合名称


 对以上的知识点有了简单的了解之后，那么直接进入我们今天的项目。

 image.png

 这个就是我们今天实现todolist增删盖查的项目目录，我们先简单的看一下目录结构

db.js

var mongoose = require("mongoose"); //引入mongoose
mongoose.connect('mongodb://localhost/list'); //连接到mongoDB的todo数据库
//该地址格式：mongodb://[username:password@]host:port/database[?options]
//默认port为27017 

var db = mongoose.connection;
db.on('error', function callback() { //监听是否有异常
console.log("Connection error");
});
db.once('open', function callback() { //监听一次打开
//在这里创建你的模式和模型
console.log('connected!');
});
//定义Listschema数据模式
var ListSchema = new mongoose.Schema({
user_id: String, //定义一个属性user_id，类型为String
content: String, //定义一个属性content，类型为String
updated_at: Date //定义一个属性updated_at，类型为Date
});

mongoose.model('user', ListSchema); //将该Schema发布为Model,user就是集合名称
module.exports = mongoose;
这里面简单介绍一下，bd.js的主要作用就是封装了连接数据库的方法，同时封装了schema数据模型，并导出成为model



router/index.js

var express = require('express');
var router = express.Router();
var mongoose = require('mongoose'); //引入对象
var TodoModel = mongoose.model('user');//引入模型
var URL = require('url'); //引入URL中间件，获取req中的参数需要
/* 进入主页 */
router.get('/', function(req, res, next) {
res.render('index', { title: 'Express' });
});
router.post('/create', function(req, res) {
console.log('req.body', req.body);
new TodoModel({ //实例化对象，新建数据
content: req.body.content,
updated_at: Date.now()
}).save(function(err, todo, count) { //保存数据
console.log('内容', todo, '数量', count); //打印保存的数据
res.redirect('/'); //返回首页
});
});
router.get('/search', function(req, res, next) {
TodoModel.
find().
sort('updated_at').
exec(function(err, aa, count) {
res.send(aa);
});
});
router.get('/edit',function(req,res){
//获取参数
var params=URL.parse(req.url,true).query;
console.log(10086,req.url);
//res.send(params);
//查询数据库
TodoModel.findById(params.id,function(err,todo){
// res.redirect('edit'); //返回首页
res.send(todo);
})
})
router.post('/update',function(req,res){
//res.send(req);
console.log(req.body);
TodoModel.findById(req.body._id,function(err,todo){
todo.content=req.body.content;
todo.updated_at=Date.now();
todo.save();
})
res.redirect('/'); //返回首页
})
router.get('/destroy',function(req,res){
var params=URL.parse(req.url,true).query;
console.log(params);
//根据待办事项的id 来删除它
TodoModel.findById(params.id, function(err, todo) {
todo.remove(function(err, todo) {
res.redirect('/');
});
});
})
module.exports = router;

因为页面的逻辑并不复杂，所以我把核心的服务都写在了index.js一个文件里面，并且都写了注释。这个文件的主要作用就是引入了mongose,model,express根据前端进来的请求分别对数据库进行操作。其中包含/create创建 /search 搜索 /destroy删除 /edit 编辑

以及一些model.find等一些数据库操作等

前端界面index.html

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
<link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
crossorigin="anonymous">
<title>Document</title>
</head>
<body>
<div class="allCon" style="width: 70%;margin: 0 auto;margin-top: 60px;">
<h1 style="text-align: center;">Node实现数据增删改查</h1>
<!--新增-->
<form class="initform" method="post" accept-charset="utf-8">
<div class="from-group">
<label>输入内容</label>
<input class="form-control createContent" type="text" name="content" placeholder="Enter Todo" />
</div>
<div class="from-group" style="margin: 10px 0px;">
<button type="button" class="btn btn-info" onclick="ceateItem()">新增</button>
</div>
</form>
<!--编辑/保存表格-->
<form class="cerateform" method="post" accept-charset="utf-8">
<div class="from-group">
<label>更改内容</label>
<input class="form-control upContent" type="text" name="content" placeholder="Enter Todo" />
</div>
<input class="form-control upid" style="display:none" type="text" name="_id" placeholder="Enter Todo" />

<button type="button" class="btn btn-success" style="margin: 10px 0px;" onclick="updateItem()">Save</button>
</form>
<!--查询全部-->
<button class="btn btn-default" id="btn" onclick="searchItem()">查询全部</button>
<hr>
<!--显示数据库信息-->
<div class="content" onclick="editItem()" style="margin-top: 10px;"></div>
</div>
</body>
<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="http://cdn.bootcss.com/jquery/1.11.1/jquery.min.js"></script>
<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="http://cdn.bootcss.com/bootstrap/3.3.0/js/bootstrap.min.js"></script>
<script>
var global = {};
$(".cerateform").hide();
//查询全部数据
function searchItem() {
$(".content").empty();
$.get('http://localhost:3000/search', function (res) {
console.log(res);
for (var i = 0; i < res.length; i++) {
$(".content").append(`
                     <div id="lists" class="alert alert-success" click="edit(event)" style="padding: 0;height: 50px;line-height: 50px;padding-left: 10px;">
                     <p  style="width: 90%;display: inline-block;" id="${res[i]._id}" >${res[i].content}</p>
                    <p class="pdelete" title="删除该数据" style=" display: inline-block;">
                        <button type="button" class="close"><span aria-hidden="true" onclick="deleteItem()"  nodeValue="${res[i]._id}">&times;</span><span class="sr-only">Close</span></button>
                    </p>
                </div>
                     `)
}
})
}
//创建数据
function ceateItem() {
var obj = {};
obj.content = $('.createContent').val();
if (obj.content != '') {
$.post('http://localhost:3000/create', obj, function (res) {
console.log(10086,obj)
console.log('新增成功');
searchItem();
})
} else {
alert('不能为空');
}
}
//查询单个数据
function editItem() {
if (event.target.nodeName == 'P') {
var id = event.target.id;
$.get('http://localhost:3000/edit?id=' + id, function (res) {
$('.cerateform').show();
$('.initform').hide();
$('.upContent').val(res.content);
$('.upid').val(res._id);
})
}
}
//更新数据
function updateItem() {
var obj = {};
obj.content = $('.upContent').val();
obj._id = $('.upid').val();
if (obj.content != '') {
$.post('http://localhost:3000/update', obj, function (res) {
console.log('成功');
searchItem();
$('.cerateform').hide();
$('.initform').show();
})
} else {
alert('不能为空');
}
}
//删除数据
function deleteItem() {
var id = event.target.attributes[2].nodeValue;
$.get('http://localhost:3000/destroy?id=' + id, function (res) {
console.log(res);
searchItem();
})
}
</script>
</html>
前后端完全分离，前端用JQ+BOOSTRAP实现的简单的页面，后台提供接口即可。



运行实例
 源代码git clone到本地之后

 npm intall  安装依赖

 npm start

 访问本地 

 http://localhost:3000/
 image.png

 同时打开Robo 3T 每一步操作之后跟数据里的数据进行一下对比

 这样，我们就实现了一个简单的操作数据库的操作。



 ·······················我是分割线··············前方高能·······有史前巨坑·······注意避让·················

 在玩美的实现了增删盖查之后，我想对数据库里的另一个collection进行同样的操作，另个name为 chm的数据库，于是我就在db.js里面把数据库连接的部分更改了一下，其他的地方都没有改动，其中更改如下：

mongoose.connect('mongodb://localhost/chm');
然后npm start之后，数据库连接成功，但是返回的数据是空，但是数据库连接成功，控制台没有报错，没有异常。

注意:chm数据库是用原生的node创建的，用于本人第一调试增删改查时用。

有点惊悚

于是我又切换到了我另一个原生Node操作数据库的项目下，一顿cd之后，运行了项目，对数据库进行操作，发现数据库可以正常写入，返回数据。

经过以上的两部操作，我发现问题可能不是出在数据库的问题上，也不是出在schema数据模型上，应该在某个方法上出了问题。

于是我不死心，想找出问题的所在

于是我又用schema模型创建了一个test的数据库，用来报错学生基本信息，于是我打开了数据库进行查看

image.png

我似乎发现了问题所在，为什么每个用schema创建的collection在可视化工具里都是复数命名

于是我去翻查api

Mongoose#model(name, [schema], [collection], [skipInit])

在官方的api文档里面有解释（我不听，我不听，我不听。。。。。。。）

When no collection argument is passed, Mongoose produces a collection name by passing the model name to the utils.toCollectionName method. This method pluralizes the name. If you don’t like this behavior, either pass a collection name or set your schemas collection name option.



 意思就是：

当没有传入collection参数时，Mongoose会通过model name（就是第一个参数），调用utils.toCollectionName方法产生一个collection name，而这个方法会使name变成复数形式。如果你不想要这个过程，只要传入collection name参数或设置Schema中的collection name选项。



什么坑爹的设定！！！！！！！！！

于是db.js应该这样改

var mongoose = require("mongoose"); //引入mongoose
mongoose.connect('mongodb://localhost/chm'); //连接到mongoDB的todo数据库
//该地址格式：mongodb://[username:password@]host:port/database[?options]
//默认port为27017 

var db = mongoose.connection;
db.on('error', function callback() { //监听是否有异常
console.log("Connection error");
});
db.once('open', function callback() { //监听一次打开
//在这里创建你的模式和模型
console.log('connected!');
});
//定义Listschema数据模式
var ListSchema = new mongoose.Schema({
user_id: String, //定义一个属性user_id，类型为String
content: String, //定义一个属性content，类型为String
updated_at: Date //定义一个属性updated_at，类型为Date
});

mongoose.model('vip', ListSchema,'vip'); //将该Schema发布为Model,user就是集合名称
module.exports = mongoose;
index.js开头引入的部分也需要更改

var express = require('express');
var router = express.Router();
var mongoose = require('mongoose'); //引入对象
var TodoModel = mongoose.model('vip');//引入模型
var URL = require('url'); //引入URL中间件，获取req中的参数需要
最后 同样的代码可以完全操作chm数据库里的数据！



坑爹！！！！！！！！！！！！！！！！！！
