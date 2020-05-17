---
title: "Vue基础知识"
date: 2020-05-10T07:30:48+08:00
draft: true
---

##### Hello World 

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue.js学习</title>
</head>

<body>
    <div id="app">
        {{message}}
    </div>
    <script src = "https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                message:"hello world"
            }
        })
    </script>
</body>
</html>
```


- id是app ，然后用#app绑定这个id。

- {{message}}是变量的意思

稍微复杂点的调用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue.js学习</title>
</head>

<body>
    <div id="app">
        {{message}}
        <h2>{{school.name}}</h2>
        <div>
            {{campus[0]}}
        </div>
    </div>
    <script src = "https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                message:"hello 吉法师",
                school:{
                    name:"LC",
                    station:"1003"
                },
                campus:["1002","1003","1005"]
            }
        })
    </script>
</body>
</html>
```

取json，数组，和Nodejs的语法是一样的

##### v-text v-html

```html
<body>
    <div id="app">
        <p v-html="content"></p>
        <p v-text="content"></p>
    </div>
 
    <script src = "./assets/js/vue.js"></script>
    <script>
       var app = new Vue({
           el:"#app",
           data : {
               content:"<a href='http://Only0322.github.io'>博客</a>"
           }
       })

    </script>
</body>
```

html标签可以解释html语法，text就只是字符串

##### 绑定函数 弹框等

```html
<body>

    <div id="test">
        <input type="button" value="v-on" v-on:click="doIt">
        
    </div>
    <script src = "./assets/js/vue.js"></script>
    <script>


       var test = new Vue({
            el:"#test",
            methods:{
                doIt:function(){
                    alert("hello world");
                }
            },
       })

    </script>
</body>
```

##### 计数器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue学习</title>
</head>
<body>
    <div id="app">
        <div class = "input-num">
            <button @click="sub">
                -
            </button>
            <span>{{num}}</span>
            <button @click="add">
                +
            </button>
        </div>

    </div>

    <script src = "./assets/js/vue.js"></script>


    <script>
        var app = new Vue({
            el:"#app",
            data:{
                num:1
            },
            methods:{
                add:function(){
                    if(this.num<10)
                    {
                        this.num++;
                    }
                    else
                    {
                        alert("长度不能超过10");
                    }
                },
                sub:function(){
                    if(this.num>0)
                    {
                        this.num--;
                    }
                }
            }
        })
    </script>
</body>
</html>
```

为什么要用this.num呢？


##### v-show

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue</title>
</head>
<body>
    <div id="app">
        <input type="button" value="切换显示" @click="change">
        <h1 v-if="isShow" >
            {{this.num}}
        </h1>
    </div>
   


    <script src ='./assets/js/vue.js'></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                isShow:false,
                num:1
            },
            methods: {
                change:function(){
                    this.isShow=!this.isShow;
                    console.log("isShow = ",this.isShow);
                }
            },
        })

    </script>
</body>
</html>
```

 **要写到app这个div里面，才是vue的对象，不然没有反应的** 
 


##### v-for标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>测试</title>
</head>
<body>
    <div id="app">
        <input type="button" value="添加数据" @click="add">
        <input type="button" value="移除数据" @click="remove">
        <li v-for='item in vege' v-bind:title="item.name">
            {{item.name}}
        </li>

    </div>
    <script src="./assets/js/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                arr:["上海","南昌","北京"],
                vege:[
                    {name:"abc"},
                    {name:"def"}
                ]
            },
            methods:{
                add:function(){
                    this.vege.push({name:"meat"})
                },
                remove:function(){
                    this.vege.shift();
                }
            }
        });
    </script>
</body>
</html>
```

简单的添加删除

##### 回车提交

```html
<body>
    <div id="app">
        <input type="button" value="按钮" @click="doIt(666)">
        <input type="text" @keyup.enter="hello">
    </div>
    <script src="./assets/js/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            methods:{
                doIt:function(tx){
                    console.log(tx)
                },
                hello:function(){
                    alert("提交完成");
                },
            }
        });
    </script>
</body>
```


##### v-model双向绑定

```html
<body>
    <div id="app">

        <input type="text" v-model="message" @keyup.enter="meths">
        <input type="button" value="修改" @click="setV">
        <h2>{{message}}</h2>
    </div>
    <script src="./assets/js/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
                message:"吉法师"
            },
            methods:{
                meths:function(){
                    alert(this.message);
                },
                setV:function(){
                    this.message="hello world"
                }
            }
        });
    </script>
</body>
```

这样一下子就可以改所有的值了


##### 记事本

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>记事本</title>
</head>
<body>
    <section id="todoapp">
        <header class ="header">
            <h1>记事本</h1>
            <input v-model="inputValue" @keyup.enter="add" autofocus="autofocus" autocomplete="off" placeholder="请输入"
           class = "new-todo"/>
        </header>
        <!--列表区域-->
        <section class ="main">
        <ul class="todo-list">
            <li class="todo" v-for="(item,index) in list">
                <div class="view">
                    <span class="index">{{index+1}}.</span>
                    <label>{{item}}</label>
                    <button class ="destory" v-if="list.length!=0" @click="remove(index)">销毁</button >
                </div>
            </li>
        </ul>
        </section>
        <footer class="footer">
            <span class="todo-count" v-if="list.length!=0">
                <strong>{{list.length}}</strong> items left
            </span>
            
            <div>
            <button @click="clearall">清空</button>
            </div>
        </footer>
       
    </section>
    <footer class="info">
    </footer>
    <script src = "assets/js/vue.js"></script>

    <script>
        var app =new Vue({
            el:"#todoapp",
            data:{
                list:["a","b","c"],
                inputValue:"双向绑定",
            },
            methods:{
                add:function(){
                    this.list.push(this.inputValue);
                    this.inputValue=null;
                },
                remove:function(index){
                    console.log(index);
                    this.list.splice(index,1);

                },
                clearall:function(){
                    this.list = [];
                }
            },
        })

    </script>
</body>
</html>
```

v-if v-model等实战


##### axios使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>axios</title>
</head>
<body>
    <input type="button" value="get请求" class="get">
    <input type="button" value="post请求" class="post">
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script>
        
        document.querySelector(".get").onclick=function(){
            console.log("123");
            axios.get("https://autumnfish.cn/api/joke/list?num=9")
            .then(function(response){
                console.log(response);
                console.log("123")
            },function(error){
                console.log(error);
            })
        }

        document.querySelector(".post").onclick=function(){
            axios.post("https://autumnfish.cn/api/user/reg",{username:"吉法师"})
            .then(function(response){
                console.log(response);
            })
        }
    </script>
</body>
</html>
```


