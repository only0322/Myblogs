---
title: "Koa和Koa-router的具体使用"
date: 2020-05-20T06:17:31+08:00
draft: true
---

##### 通过Koa来创建Koa实例

```js
const Koa = require('koa');
const app = new Koa();
```

##### 路由配置

预先设置好路由和响应，以此来进行操作。

全部代码

```js
const moment = require('moment');

let Mysql_student = require('./tools/applyMysql');

const router = require('koa-router')();

let appMysql = new Mysql_student();

// let sql = 'select ID,Name from test.student';
// let sqlParam = [];

// async function result(){
//     await appMysql.dbop(sql,sqlParam);
// }

// let a = await result();

// console.log('result = ',a);

const Koa = require('koa');
// 创建koa的实例app
const app = new Koa();

router.get('/student/select',ctx =>{
    ctx.body="操你妈";
})



app.use(router.routes());
app.use(router.allowedMethods);

// 监听端口
app.listen(3000, () => {
    console.log("服务器已启动，http://localhost:3000");
})
```

接口已经能在postman调通