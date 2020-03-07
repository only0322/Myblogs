---
title: "Nodejs数据库和文件操作总结"
date: 2020-03-05T19:48:53+08:00
draft: true
---

# 文件操作

需要引入nodejs自带的fs模块
```
var fs=require("fs");
var path=require("path");
```

异步读取文件`fs.readFileSync()`

对buffer进行切割：`slice()`

buffer转为方便操作的字符串`toString()`


还需要进制转换操作

1. buffer转16进制

`parseInt(filehead.toString('hex'),"16")`

2. 16进制转2进制

`parseInt(weekdatebin,"16").toString(2);`

# 数据库操作

先引入MySQL模块`var mysql=require('mysql')`


```
var connection=mysql.createConnection({
    host:'172.161.25.xx',
    user:'hmsnc',
    password:'xx',
    database:'xx'
});
```
但最好还是每次操作都重新创建连接，以防发生重连接的错误

?磺`var connection=mysql.createConnection(dbconfig);`

向数据库插入数据

```
var addSql='insert into tb_paraData values(0,?,?,?,?,?,?,?)';
var addparams=['JSON',fileversion,moment().format("YYYYMMDD"),record,JSON.stringify(myjson),filenameraw,"??Ч??"];

//addSql=mysql.format(addSql,addparams);
console.log(addSql);
connection.query(addSql,addparams,function(err,result){
    if (err){
        console.log("insert err-",err.message);
        connection.end();
        return;
    }
    else{
        console.log("insert result",result);
        connection.end();
    }
})
```


# 总结

总的来说nodejs上手还是很快的，暂时先写这么多吧，后续有内容再进一步更新。

