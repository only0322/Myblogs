---
title: "Vue+Vue-cli+ElementUI实战"
date: 2020-05-14T21:25:25+08:00
draft: true
---


用Vue和Element-ui做的点菜系统，一些基本的代码记录

##### 图标库使用

```vue
<template>
  <div id="app">
    <router-view/>
    <i class = "icon iconfont iconchild"></i>
    <br>
    <i class = "icon iconfont iconbrightness"></i>
  </div>
</template>

```



##### 侧边栏及样式 

```vue

<template>
<div class="left-nav">
    <ul>
        <li>
            <i class="icon iconfont iconaddto" ></i>
            <div>收银</div>
        </li>
        <li>
            <i class="icon iconfont icondelete" ></i>
            <div>店铺</div>
        </li>
        <li>
            <i class="icon iconfont iconbrightness" ></i>
            <div>商品</div>
        </li>
        <li>
            <i class="icon iconfont iconbluray" ></i>
            <div>会员</div>
        </li>
        <li>
            <i class="icon iconfont iconbluetooth_link" ></i>
            <div>统计</div>
        </li>
         <li>
            <i class="icon iconfont iconbluetooth_link" ></i>
            <div>设置</div>
        </li>

    </ul>
</div>
</template>
<script>
export default {

}
</script>

<style >
 .left-nav{
     color: #fff;
     font-size :10px;
     height: 100%;
     background-color: #1D8CE0;
     float:left;
     width:5%;
}
.iconfont{
     font-size:24px;
}
.left-nav ul{
    padding: 0px;
    margin: 0px;
}
.left-nav li{
    list-style: none;
    text-align: center;
    border-bottom: 1px solid #20a0ff;
    padding: 10px;
}
</style>
```

引入的代码

```html
<template>
  <div id="app">
    <leftNav></leftNav>
    <router-view></router-view>
  </div>
</template>

<script>
import leftNav from './components/common/leftNav'
export default {
  name: 'App',
  components: {
    leftNav
  }
}
</script>
```

##### 菜单栏 按钮样式

```html
<template>
  <div class="hello">
    <el-row>
      <el-col :span='7' class="pos-order" id='order-list'>
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border stype="width:100%">
            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
            <el-table-column prop="count" label="数量" width="50"></el-table-column>
            <el-table-column prop="price" label="金额" width="70"></el-table-column>
            <el-table-column label="操作" width="100" fixed="right"></el-table-column>
            <template >
              <el-button type="text" size="small">删除</el-button>
              <el-button type="text" size="small">增加</el-button>
            </template>
            </el-table>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger">删除</el-button>
              <el-button type="success">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">

          </el-tab-pane>
          <el-tab-pane label="外卖">
            
          </el-tab-pane>
        </el-tabs>

          

      </el-col>
      <el-col>
        产品栏
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'pos',
  data:function(){
    return {
      tableData:[
        {
          goodsName:"香辣鸡腿堡",
          price:15,
          count:10
        },
        {
          goodsName:"吮指原味鸡",
          price:10,
          count:20
        },
        {
          goodsName:"香辣脆皮鸡",
          price:15,
          count:15
        }
      ]
    }
  },
  
  mounted:function () {
    var orderHeight = document.body.clientHeight;
    console.log(orderHeight)
    document.getElementById('order-list').style.height=orderHeight+'px'
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order{
  background-color: #F9FAFC;
  border-right :1px solid #C0CCDA;
}
.div-btn{
  margin-top: 10px;
}
</style>

```

##### 加上菜单，信息

```html
<template>
  <div class="hello">
    <el-row>
      <el-col :span='7' class="pos-order" id='order-list'>
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border stype="width:100%">
            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
            <el-table-column prop="count" label="数量" width="50"></el-table-column>
            <el-table-column prop="price" label="金额" width="70"></el-table-column>
            <el-table-column label="操作" width="100" fixed="right">
              <template slot-scope="scope">
              <el-button type="button" size="small">删除</el-button>
              <el-button type="button" size="small">增加</el-button>
            </template>
            </el-table-column>
            
            </el-table>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger">删除</el-button>
              <el-button type="success">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">

          </el-tab-pane>
          <el-tab-pane label="外卖">
            
          </el-tab-pane>
        </el-tabs>

          

      </el-col>
      <el-col :span="17">
        <div class="often-goods">
        <div class="title">常用商品</div>
        <div class="often-goods-list">
        <ul>
          <li>
            <span>香辣鸡腿堡</span>
            <span class="o-price">￥15元</span>
          </li>
        </ul>
        </div>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'pos',
  data:function(){
    return {
      tableData:[
        {
          goodsName:"香辣鸡腿堡",
          price:15,
          count:10
        },
        {
          goodsName:"吮指原味鸡",
          price:10,
          count:20
        },
        {
          goodsName:"香辣脆皮鸡",
          price:15,
          count:15
        }
      ]
    }
  },
  
  mounted:function () {
    var orderHeight = document.body.clientHeight;
    console.log(orderHeight)
    document.getElementById('order-list').style.height=orderHeight+'px'
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order{
  background-color: #F9FAFC;
  border-right :1px solid #C0CCDA;
}
.div-btn{
  margin-top: 10px;
}
.title{
  height:20px;
  border-bottom: 1px solid #D3dcd6;
  background-color: #F9FAFC;
  padding:10px;
  text-align: left;
}
.often-goods-list ul li{
  list-style:none;
  float:left;
  border:1px solid #E5E9F2;
  padding:10px;
  margin:10px;
  background-color: #FFF;
}
</style>

```


截图：
![截图](/images/Front/pos.png)
