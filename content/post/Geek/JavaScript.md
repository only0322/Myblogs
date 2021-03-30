---
title: "JavaScript核心原理解析"
date: 2021-03-29T17:57:06+08:00
draft: true
---

## 一、JavaScript语言是如何构建起来的

### 1.delete 0：JavaScript到底有什么是可以销毁的

在 JavaScript 中表达式是一个很独特的东西，所有一切表达式运算的终极目的都是为了得到一个值，例如字符串。然后再用另外一些操作将这个值输出出来，例如变成网页中的一个元素（element）。

**这个结果，才是delete这个操作要删除的东西。**

在JS中，语句和表达式可以被执行并存在执行结果。

#### delete的运算结果

1. 如果x根本不存在，delete x什么也不做，返回true

2. 删除字面量 返回true

3. 如果x是对象，delete x即删除对象本身，返回false

4. delete undefined 返回false

### 2.var x = y = 100：声明语句与语法改变了JavaScript语言核心性质

let x所声明的那个x其实也已经存在 f() 函数的上下文环境中。访问它之所以会抛出异常（Exception），不是因为它不存在，而是因为这个标识符被拒绝访问了。

JavaScript 是允许访问还没有绑定值的var所声明的标识符的。这种标识符后来统一约定称为“变量声明（varDelcs）”，而“let/const”则称为“词法声明（lexicalDecls）”。JavaScript 环境在创建一个“变量名（varName in varDecls）”后，会为它初始化绑定一个 undefined 值，而”词法名字（lexicalNames）”在创建之后就没有这项待遇，所以它们在缺省情况下就是“还没有绑定值”的标识符。

```js
a;
var a;
//undefined

b;
let b;
//Uncaught ReferenceError: Cannot access 'b' before initialization
```

JavaScript的赋值其实是将右操作数的值赋值给左操作数的引用。

也就是说，在 JavaScript 中，一个赋值表达式的左边和右边其实“都是”表达式。

变量泄漏：变量可以随用随声明，也不用像后来的let语句一样，还要考虑在声明语句之前能不能访问的问题了。但是代码多了就容易出现很多问题。

**var x = y = 100，在这行代码中，等号的右边是一个表达式y = 100，它发生了一次“向不存在的变量赋值”，所以它隐式地声明了一个全局变量y，并赋值为 100。**

```js
function main() {
    let a = b = 0;
}
main();
console.log(b);
console.log(a);
//0
//ReferenceError: a is not defined
```

赋值表达式也是有结果的，它的结果是右操作数的值。

### 3.a.x = a = {n:2}：一道被无数人无数次地解释过的经典面试题

（赋值表达式左侧的）操作数可以是另一个表达式——这在专栏的第一讲里就讲过了，而“var 声明”语句中的等号左边，绝不可能是一个表达式

**JavaScript 总是严格按照从左至右的顺序来计算表达式。**

例如，在表达式w = x + y * z中，将首先计算子表达式 w，然后计算 x、y 和 z；然后，y 的值和 z 的值相乘，再加上 x 的值；最后将其赋值给表达式 w 所指代的变量或属性。

但是带有var let const的变量就不是表达式而是一个标识符了。

