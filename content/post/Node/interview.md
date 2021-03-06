---
title: "Nodejs面试题汇总"
date: 2020-09-10T10:55:25+08:00
draft: true
---


### 一、Nodejs模块引入方式

Node中，每个文件模块都是一个对象，它的定义如下：

```js
function Module(id, parent) {
 this.id = id;
 this.exports = {};
 this.parent = parent;
 this.filename = null;
 this.loaded = false;
 this.children = [];
}

module.exports = Module;

var module = new Module(filename, parent);
```

### 二、node导出模块有两种方式，一种是exports.xxx=xxx和Module.exports={}有什么区别？

个人理解：一般函数用 `exports.xxx = xxx;` 类用`module.exports = xxx;`

### 三、什么是错误优先的回调函数？

错误优先的回调函数用于传递错误和数据。第一个参数始终应该是一个错误对象， 用于检查程序是否发生了错误。其余的参数用于传递数据。例如：

```js
fs.readFile(filePath, function(err, data) {  
    if (err) {
        //handle the error
    }
    // use the data object
});

```

### 四、RestFul是什么

Representational State Transfer（表象层状态转变），它由Roy Fielding于2000年论文中提出，它代表着分布式服务的架构风格。

1.每一个URL代表一种资源； 

2.客户端和服务器之间，传递这种资源的某种表现层； 

3.客户端通过四个HTTP动词（get、post、put、delete），对服务器端资源进行操作，实现”表现层状态转化”。


#### RESTful6大原则

REST之父Roy Fielding在论文中阐述REST架构的6大基本原则，它们分别是：

1. C-S架构

数据的存储在Server端，Client端只需使用就行。两端彻底分离的好处使client端代码的可移植性变强，Server端的拓展性变强。两端单独开发，互不干扰。

2. 无状态

http请求本身就是无状态的，基于C-S架构，客户端的每一次请求带有充分的信息能够让服务端识别。请求所需的一些信息都包含在URL的查询参数、header、div，服务端能够根据请求的各种参数，无需保存客户端的状态，将响应正确返回给客户端。无状态的特征大大提高的服务端的健壮性和可拓展性。

当然，这种无状态性的约束也是有缺点的，客户端的每一次请求都必须带上相同重复的信息确定自己的身份和状态，造成传输数据的冗余性，但这种确定对于性能和使用来说，几乎是忽略不计的。

3. 统一的接口

REST架构的核心内容，统一的接口对于RESTful服务非常重要。客户端只需要关注实现接口就可以，接口的可读性加强，使用人员方便调用。

REST接口约束定义为：资源识别; 请求动作; 响应信息; 它表示通过uri标出你要操作的资源，通过请求动作（http method）标识要执行的操作，通过返回的状态码来表示这次请求的执行结果。

4. 一致的数据格式

服务端返回的数据格式要么是XML，要么是Json（获取数据），或者直接返回状态码，一些知名网站的开放平台的操作数据的api，post、put、patch都是返回的一个状态码 。

如请求一条微博信息，服务端响应信息应该包含这条微博相关的其他URL，客户端可以进一步利用这些URL发起请求获取感兴趣的信息，再如分页可以从第一页的返回数据中获取下一页的URT也是基于这个原理。

5. 可缓存

在万维网上，客户端可以缓存页面的响应内容。因此响应都应隐式或显式的定义为可缓存的，若不可缓存则要避免客户端在多次请求后用旧数据或脏数据来响应。管理得当的缓存会部分地或完全地除去客户端和服务端之间的交互，进一步改善性能和延展性。

6. 按需编码、可定制代码

服务端可选择临时给客户端下发一些功能代码让客户端来执行，从而定制和扩展客户端的某些功能。比如服务端可以返回一些 Javascript 代码让客户端执行，去实现某些特定的功能。提示：REST架构中的设计准则中，只有按需编码为可选项。如果某个服务违反了其他任意一项准则，严格意思上不能称之为RESTful风格。

### 五、什么是WebSocket

WebSocket本质就是让服务器主动发起请求。

 (1）建立在 TCP 协议之上，服务器端的实现比较容易。

（2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

（3）数据格式比较轻量，性能开销小，通信高效。

（4）可以发送文本，也可以发送二进制数据。

（5）没有同源限制，客户端可以与任意服务器通信。

（6）协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。


### 六、CommonJS模块和require关键字

CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。

#### require命令

1.基本用法   require命令用于加载模块文件，相当于读入并执行一个js文件，然后返回该模块的exports对象，没有发现指定模块，则就会报错。

例如example.js ,exports.name = 'tom';exports.age = 50;

在同目录下的demo.js文件中 var example = require('./example.js');


