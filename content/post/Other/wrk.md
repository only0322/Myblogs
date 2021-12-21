---
title: "Wrk使用浅谈"
date: 2021-12-21T11:32:51+08:00
draft: true
---

## 介绍

Wrk是一款用C语言编写的轻量化压测软件，采用多路复用的方式进行压测，对机器的性能要求比Jmeter更低。

## 安装

```shell
git clone https://github.com/wg/wrk.git;
cd /wrk;
make;
```

## 命令

`wrk -t5 -c100 -d 5s -s ./menu_detail.lua 'http://127.0.0.1:3000`

- t: 线程数 一般不宜太高

- c: 连接数
- d: 持续时间
- --timeout:超时时间 不设置就默认2s
- -s带脚本执行
- 最后是路径

## 脚本

### POST

```lua
wrk.method = "POST"
wrk.headers["Content-Type"] = "application/json"

wrk.headers['x-customer-id'] = "1-LIKNY3H"
wrk.path="/wxmem/mop/store/list?sign=skip"

wrk.body = "{\"pageNo\":1,\"pageSize\":20,\"currentLongitude\":\"113.562864\",\"currentLatitude\":\"22.1233\",\"longitude\":\"113.562864\",\"latitude\":\"22.1233\",\"diameter\":50000,\"nonce\":0.7852713490574055}"
```

简单使用就是这样，把json压缩在body里面 

### GET

```lua
wrk.method = "GET"
wrk.headers["Content-Type"] = "application/json"
wrk.headers['x-customer-id'] = "1-LIKNY3H"
wrk.path = "/wxmem/mop/menu/detail?sign=skip&storeId=99999&menuNo=2&channel=MOP"
```

参数写在path里即可

## 压测结果解读

```
Running 30s test @ http://www.bing.com （压测时间30s）

  8 threads and 200 connections （共8个测试线程，200个连接）

  Thread Stats   Avg      Stdev     Max   +/- Stdev
              （平均值） （标准差）（最大值）（正负一个标准差所占比例）
    Latency    46.67ms  215.38ms   1.67s    95.59%
    （延迟）
    Req/Sec     7.91k     1.15k   10.26k    70.77%
    （处理中的请求数）

  Latency Distribution （延迟分布）
     50%    2.93ms
     75%    3.78ms
     90%    4.73ms
     99%    1.35s （99分位的延迟：%99的请求在1.35s以内）
  1790465 requests in 30.01s, 684.08MB read （30.01秒内共处理完成了1790465个请求，读取了684.08MB数据）
Requests/sec:  59658.29 （平均每秒处理完成59658.29个请求）
Transfer/sec:     22.79MB （平均每秒读取数据22.79MB）
```

## 其他

wrk还可以用启动钩子去自定义脚本执行过程，一般用不到，这里也就不赘述了。