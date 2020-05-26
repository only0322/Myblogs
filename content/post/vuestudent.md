---
title: "Vue制作的学生管理系统"
date: 2020-05-26T06:21:13+08:00
draft: true
---

首先给出项目地址：[码云连接](https://gitee.com/onlyyyy_admin/MyFrontEnd)

前端存放在manager目录下，后端则是BackEnd目录。

### 一、用到的技术

#### 1.前端:Vue+iview

#### 2.后端:Node.js+Koa

#### 3.数据库:MySQL

### 二、各模块功能实现

#### 1.登录界面

登录界面通过前端渲染两个对话框，供用户输入账号密码，发送post请求至后端。

后端去数据库查询预先配置好的公私钥，通过RSA解密密文，与前端发送的密码明文进行匹配。

```Js
<!-- 登录界面 -->
<Modal v-model="notLogin" name="" title="登录" 
:on-text="登录" :closable="false"  :mask-closable="false">
<div slot="footer">
    <Button type="primary" size="large" @click="loginCheck">确定</Button>
</div>

    <Form :model="logInfo" label-position="left" :label-width="100">
        <FormItem label="用户名">
            <Input v-model="logInfo.name"></Input>
        </FormItem>
        <FormItem label="密码">
            <Input v-model="logInfo.password"></Input>
        </FormItem>
        
    </Form>
</Modal>
```

后端处理：

```Js
router.post('/student/password',async(ctx)=>{
    let Body = ctx.request.body;
    console.log("Body = ",Body);
    let sql_key = `select public,private from test.RSA where type = 'login'`;
    let result = await appMysql.dbop(sql_key);
    console.log("result = ",result);
    let key = new NodeRSA(result[0].public); 
    let sql_get_password = `select password from test.password where name = '${Body.name}'`;
    let passwd = await appMysql.dbop(sql_get_password);
    if(passwd.length == 0)
    {
        ctx.body = 1;
        return;
    }
    let rawText = key.decryptPublic(passwd[0].password,'utf8');
    console.log(rawText);
    if(Body.password == rawText)
    {
        ctx.body = 0;
        return;
    }
    else
    {
        ctx.body = 1;

    }
    ctx.body = 1;
    
})
```



干脆上全部代码吧

前端：


```js
<style scoped>
    .layout{
        border: 1px solid #d7dde4;
        background: #f5f7f9;
        position: relative;
    }
    .layout-breadcrumb{
        padding: 10px 15px 0;
    }
    .layout-content{
        min-height: 200px;
        margin: 15px;
        overflow: hidden;
        background: #fff;
        border-radius: 4px;
    }
    .layout-content-main{
        padding: 10px;
    }
    .layout-copy{
        text-align: center;
        padding: 10px 0 20px;
        color: #9ea7b4;
    }
    .layout-menu-left{
        background: #464c5b;
    }
    .layout-header{
        height: 60px;
        background: #fff;
        box-shadow: 0 1px 1px rgba(0,0,0,.1);
    }
    .layout-logo-left{
        width: 90%;
        height: 30px;
        background: #5b6270;
        border-radius: 3px;
        margin: 15px auto;
    }
</style>
<template>
    <div class="layout">

        <Row type="flex">
            <i-col span="5" class="layout-menu-left">
                <Menu active-key="1-2" theme="dark" width="auto" :open-keys="['1']">
                    <div class="layout-logo-left"></div>
                    <Submenu key="1">
                        <template slot="title">
                            <Icon type="ios-select"></Icon>
                            增删改查
                        </template>
                        <Menu-item key="1-1" @click.native="select1(1)">管理</Menu-item>
                        <Menu-item key="1-2" @click.native="select2(1)">图表</Menu-item>
                    </Submenu>
                </Menu>
            </i-col>
            <i-col span="19">
                <div class="layout-header"></div>
                <div class="layout-breadcrumb">
                    <Breadcrumb>
                        <Breadcrumb-item href="#">首页</Breadcrumb-item>
                        <Breadcrumb-item href="#">应用中心</Breadcrumb-item>
                        <Breadcrumb-item>某应用</Breadcrumb-item>
                    </Breadcrumb>
                </div>
                <!-- 登录界面 -->
                <Modal v-model="notLogin" name="" title="登录" 
                :on-text="登录" :closable="false"  :mask-closable="false">
               <div slot="footer">
                    <Button type="primary" size="large" @click="loginCheck">确定</Button>
                </div>

                    <Form :model="logInfo" label-position="left" :label-width="100">
                        <FormItem label="用户名">
                            <Input v-model="logInfo.name"></Input>
                        </FormItem>
                        <FormItem label="密码">
                            <Input v-model="logInfo.password"></Input>
                        </FormItem>
                        
                    </Form>
                </Modal>
                <div class="layout-content">
                    显示区域
                    <div id="test">
                        <div v-if="test1">
                            <i-button type="info" @click.native="select_student">查询</i-button>
                            <i-button type="success" @click="showadd">新增</i-button>
                            <br>
                            <i-table width="800" border :columns="columns2" :data="data_student" v-if="selected">
                                <i-tab></i-tab>
                            </i-table>
                            <Modal v-model="modal1" name="" title="新增" @on-ok="ok" @on-cancel="cancel">
                                <Form :model="formLeft" label-position="left" :label-width="100">
                                    <FormItem label="姓名">
                                        <Input v-model="formLeft.name"></Input>
                                    </FormItem>
                                    <FormItem label="年龄">
                                        <Input v-model="formLeft.age"></Input>
                                    </FormItem>
                                    <FormItem label="城市">
                                        <Input v-model="formLeft.city"></Input>
                                    </FormItem>
                                </Form>
                                
                                <Form :model="formTop" label-position="top">
                                    <FormItem label="地区">
                                        <Input v-model="formTop.area"></Input>
                                    </FormItem>
                                    <FormItem label="地址">
                                        <Input v-model="formTop.address"></Input>
                                    </FormItem>
                                    <FormItem label="邮箱">
                                        <Input v-model="formTop.mail"></Input>
                                    </FormItem>
                                </Form>
                            </Modal>
                            <Modal v-model="delete_modal" name="" title="删除"  @on-ok="check_delete" @on-cancel="deletecancel">
                                要删除的人为【{{student_name}}】
                                是否确定要删除？
                            </Modal>
                            <Modal v-model="update_modal" name="" title="修改"  @on-ok="check_update" @on-cancel="cancel">
                                <Form :model="formLeft" label-position="left" :label-width="100">
                                    <FormItem label="姓名">
                                        <Input v-model="formLeft.name"></Input>
                                    </FormItem>
                                    <FormItem label="年龄">
                                        <Input v-model="formLeft.age"></Input>
                                    </FormItem>
                                    <FormItem label="城市">
                                        <Input v-model="formLeft.city"></Input>
                                    </FormItem>
                                </Form>
                                
                                <Form :model="formTop" label-position="top">
                                    <FormItem label="地区">
                                        <Input v-model="formTop.area"></Input>
                                    </FormItem>
                                    <FormItem label="地址">
                                        <Input v-model="formTop.address"></Input>
                                    </FormItem>
                                    <FormItem label="邮箱">
                                        <Input v-model="formTop.mail"></Input>
                                    </FormItem>
                                </Form>
                            </Modal>
                            
                            <!-- <mymodal></mymodal> -->
                        </div>
                        
                        <div v-if="test2">
                            hello2
                            
                        </div>
                        {{test3}}
                    </div>
                </div>
                <div class="layout-copy">
                    2020 &copy; 吉法师
                </div>
            </i-col>
        </Row>
    </div>
</template>
<script>
import axios from 'axios'
import mymodal from './modal'
axios.defaults.baseURL="http://127.0.0.1:3000";
axios.defaults.withCredentials = false;
export default {
    data: function(){
        return{
            currentIndex:-1,
            notLogin:true,
            student_name:"",
            delete_modal:false,
            update_modal:false,
            test1: false,
            test2: false,
            test3: null,
            selected:null,
            columns2: [
                {
                    title: '姓名',
                    key: 'name',
                    width: 100,
                    fixed: 'left'
                },
                {
                    title: '年龄',
                    key: 'age',
                    width: 100
                },
                {
                    title: '城市',
                    key: 'province',
                    width: 100
                },
                {
                    title: '地区',
                    key: 'city',
                    width: 100
                },
                {
                    title: '地址',
                    key: 'address',
                    width: 200
                },
                {
                    title: '邮箱',
                    key: 'zip',
                    width: 100
                },
                {
                    title: '操作',
                    key: 'action',
                    fixed: 'right',
                    width: 120,
                    render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {
                                        type: 'warning',
                                        size: 'small'
                                    },
                                    on: {
                                        click :()=>{
                                            this.change(params.index);
                                        }
                                    }
                                }, '修改'),
                                h('Button', {
                                    props: {
                                        type: 'error',
                                        size: 'small'
                                    },
                                    on: {
                                        click :()=>{
                                            this.delete(params.index);
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                }
            ],
            data_student: [],
            modal1:false,
            formLeft: {
                    name: '',
                    age: '',
                    city: ''
                },
            formTop: {
                area: '',
                address: '',
                mail: ''
            },
            logInfo:{
                name :'',
                password:'',
            }
            
        }
        
    },
    methods:{
        loginCheck:function(){
            axios.post("/student/password",{
                name:this.logInfo.name,
                password:this.logInfo.password
            }).then((response) =>{
                    if(response.data == 0)
                    {
                        this.$Message.info("登陆成功!");
                        this.notLogin = false;

                    }
                    else
                    {
                        this.$Message.info("用户名或密码错误!");
                        this.notLogin = true;

                    }
                }).catch(function(response){
                    this.$Message.info("系统错误!");
                    this.notLogin = true;
                })
        },
        select1:function(){
            this.test1 = !this.test1
            if(this.test2 == true)
            {
                this.test2 = false;
            }
            console.log("test1=",test1)
        },
        select2:function(){
            this.test2 = !this.test2
            if(this.test1 == true)
            {
                this.test1 = false;
            }
            console.log("test2=",test2)
        },
        select_student:function(){
            console.log('/student/select');
            this.data_student = [];
            let that = this;
            axios.get('/student/select',{
            }).then( (response)=>{
                console.log('response = ',response.data);
                this.selected = true;
                for(let i=0;i<response.data.length;i++)
                {
                    let resValue = {};
                    resValue.name = response.data[i].Name;
                    resValue.age = response.data[i].age;
                    resValue.address = response.data[i].address;
                    resValue.province = response.data[i].city;
                    resValue.city = response.data[i].area;
                    resValue.zip = response.data[i].mail;
                    this.data_student.push(resValue);
                }
                console.log('data_student = ',this.data_student);
            }).catch(function (error){
                console.log(error);
            })
        },
        ok () {
            axios.post('/student/add',{
                
                name:this.formLeft.name,
                age:this.formLeft.age,
                city:this.formLeft.city,
                area:this.formTop.area,
                address:this.formTop.address,
                mail:this.formTop.mail
                }).then((response) =>{
                    console.log("返回值为",response);
                    this.select_student();
                    if(response.data ==0)
                    {
                        this.$Message.info('新建成功');
                        console.log("成功")
                    }
                    else
                    {
                        this.$Message.info('新建失败');
                    }
                    
                }).catch(function(response){
                    this.$Message.info("创建失败，系统错误！");
                })
            },
        cancel () {
            this.$Message.info('取消提交');
        },
        showadd:function(){
            this.select_student();
            console.log("点击了新增");
            this.modal1 = true;
            console.log('modal1 = ',this.modal1);
        },
        change:function(index){
            console.log("点击了修改按钮 index = ",index);
            this.currentIndex = index;
            this.update_modal = true;
            this.formLeft.name  = this.data_student[this.currentIndex].name;
            this.formLeft.age = this.data_student[this.currentIndex].age;
            this.formLeft.city  =  this.data_student[this.currentIndex].city;
            this.formTop.area = this.data_student[this.currentIndex].province;
            this.formTop.address = this.data_student[this.currentIndex].address;
            this.formTop.mail = this.data_student[this.currentIndex].zip;
        },
        delete:function(index){
            console.log("点击了删除按钮 index = ",index);
            this.delete_modal = true;
            this.currentIndex = index;
            let name = this.data_student[index].name;
            this.student_name = name;
        },
        check_delete:function(){
            let name = this.data_student[this.currentIndex].name;
            console.log("要删除的名字为",name);
            axios.post('/student/delete',{
                studentName:name 
            }).then((response)=>{
                if(response.data == 0)
                {
                    this.$Message.info("删除成功");
                    this.select_student();
                }
                else {
                    this.$Message.info("删除出错");
                }
            }).catch(function (error){
                console.log(error);
            })
            
        },
        check_update:function(){
            let name = this.data_student[this.currentIndex].name;
            let formData = {};
            console.log("this.formLeft.name",this.formLeft.name);
            console.log("this.formTop.province",this.formTop.mail);
            formData.name =this.formLeft.name;
            formData.age =  this.formLeft.age;
            formData.city =  this.formLeft.city;
            formData.area= this.formTop.area;
            formData.address= this.formTop.address;
            formData.mail = this.formTop.mail;
            console.log("要发给后端的数据为",formData);
            axios.post("/student/update",{
                Data:formData
            }).then((response)=>{
                if(response.data == 1)
                {
                    this.$Message.info("更新成功");
                    this.select_student();
                }
                else if(response.data == 0)
                {
                    this.$Message.info("输入了新的人名，新建成功!");
                    this.select_student();
                }
                else {
                    this.$Message.info("更新出错");
                }
            }).catch(function (error){
                console.log(error);
            })
        },
        deletecancel (){
            this.$Message.info("取消删除");
        }


        
    },
    components: {
            mymodal,
    }
}
</script>


```

后端

```js
//https://gitee.com/onlyyyy_admin/MyFrontEnd
const moment = require('moment');
const NodeRSA = require('node-rsa')
let Mysql_student = require('./tools/applyMysql');

const Koa = require('koa');
const static = require('koa-static');
const bodyParser = require('koa-bodyparser');
const Router = require('koa-router');
const koaBody = require('koa-body');
const cors = require('koa2-cors');
// 创建koa的实例app
const app = new Koa();
let appMysql = new Mysql_student();


var router = new Router();

router.post('/student/password',async(ctx)=>{
    let Body = ctx.request.body;
    console.log("Body = ",Body);
    let sql_key = `select public,private from test.RSA where type = 'login'`;
    let result = await appMysql.dbop(sql_key);
    console.log("result = ",result);
    let key = new NodeRSA(result[0].public); 
    let sql_get_password = `select password from test.password where name = '${Body.name}'`;
    let passwd = await appMysql.dbop(sql_get_password);
    if(passwd.length == 0)
    {
        ctx.body = 1;
        return;
    }
    let rawText = key.decryptPublic(passwd[0].password,'utf8');
    console.log(rawText);
    if(Body.password == rawText)
    {
        ctx.body = 0;
        return;
    }
    else
    {
        ctx.body = 1;

    }
    ctx.body = 1;
    
})

router.get('/student/select',async (ctx) =>{
    console.log(ctx.request.body);
    let sql = 'select * from test.student'; //
    let sqlParam = [];
    let a = await appMysql.dbop(sql,sqlParam);

    //console.log('result = ',a);
    ctx.body = a;

})

router.post('/student/add',async(ctx)=>{
    console.log(ctx.request.body);
    let body = ctx.request.body;
    let sql = `insert into test.student(name,age,city,area,address,mail)
    values('${body.name}','${body.age}','${body.city}','${body.area}','${body.address}','${body.mail}')`;

    try{
        await appMysql.dbop(sql,null);
        
        ctx.body = 0;
    }catch (error){
        ctx.body = 1;
    }
    
    
})


router.post('/student/delete',async(ctx)=>{
    let Body = ctx.request.body;
    console.log("Body = ",Body);
    let name = Body.studentName;
    let sql = `delete from test.student where name = '${name}'`;
    try{
        await appMysql.dbop(sql,null);
        ctx.body =0;
    }catch (error) {
        ctx.body =1;
    }
})

router.post('/student/update',async(ctx)=>{
    let Body = ctx.request.body;
    console.log("Body = ",Body);
    let name = Body.Data.name;
    let age = Body.Data.age;
    let city = Body.Data.city;
    let area = Body.Data.area;
    let address = Body.Data.address;
    let mail = Body.Data.mail;
    let getsql = `select count(*) as count from test.student where name = '${name}'`;
    let result = await appMysql.dbop(getsql,null);
    
    let count = result[0].count;
    console.log("count = ",count);
    let sql = '';
    if(count == 0)
    {
        sql = `insert into test.student(name,age,city,area,address,mail)
        values('${name}','${age}','${city}','${area}','${address}','${mail}')`;
    }
    else
    {
        sql = `update test.student set age = '${age}',city ='${city}',area='${area}',address='${address}',
        mail = '${mail}'
        where name = '${name}'`;
    }
    console.log("sql =  ",sql);
    try{
        await appMysql.dbop(sql,null);
        if(count == 0)
        {
            ctx.body = 0;
        }
        else
        {
            ctx.body = 1;
        }
    }catch(error) {
        ctx.body = -1;
    }
    console.log("返回值为",ctx.body);
})


app.use(bodyParser());
app.use(cors());  //跨域
app.use(router.routes());
app.use(router.allowedMethods());

// 监听端口
app.listen(3000, () => {
    console.log("服务器已启动，http://localhost:3000");
})

```