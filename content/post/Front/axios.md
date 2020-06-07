---
title: "Axios发送请求的具体使用"
date: 2020-05-31T07:45:36+08:00
draft: true
---

#### axios具体请求类型

- axios.request(config)

- axios.get(url[, config])

- axios.delete(url[, config])

- axios.head(url[, config])

- axios.post(url[, data[, config]])

- axios.put(url[, data[, config]])

- axios.patch(url[, data[, config]])

##### 1.GET请求

```js
axios.get('demo/url', {
    params: {
        id: 123,
        name: 'Henry',
    },
   timeout: 1000,
  ...//其他相关配置
})
```

##### 2.POST请求

```js
axios.post('demo/url', {
    id: 123,
    name: 'Henry',
},{
   timeout: 1000,
    ...//其他相关配置
})
```

##### 3.DELETE请求

```js
//如果服务端将参数作为java对象来封装接受
axios.delete('demo/url', {
    data: {
        id: 123,
        name: 'Henry',
    },
     timeout: 1000,
    ...//其他相关配置
})
//如果服务端将参数作为url参数来接受，则请求的url为:www.demo/url?a=1&b=2形式
axios.delete('demo/url', {
    params: {
        id: 123,
        name: 'Henry',
    },
     timeout: 1000,
    ...//其他相关配置
})
```

##### 4.PUT请求

```js
axios.put('demo/url', {
    id: 123,
    name: 'Henry',
},{
   timeout: 1000,
    ...//其他相关配置
})
```

##### 5.PATCH请求

```js
axios.patch('demo/url', {
    id: 123,
    name: 'Henry',
},{
   timeout: 1000,
    ...//其他相关配置
})
```

#### 本文记录为主