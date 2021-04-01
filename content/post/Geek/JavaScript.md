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

### 4.export default function() {}：你无法导出一个匿名函数表达式

顺便说下JavaScript的模块技术，以下是导出的方式：

```js

// 导出“（声明的）名字”
export <let/const/var> x ...;
export function x() ...
export class x ...
export {x, y, z, ...};


// 导出“（重命名的）名字”
export { x as y, ...};
export { x as default, ... };


// 导出“（其它模块的）名字”
export ... from ...;


// 导出“值”
export default <expression
```

但是对于最后这种形式，也就是“（导出）值”的形式，事实上是非常特殊的。

因为如要导出一个模块的全部内容就必须导出“（全部的）名字和值”，然而纯粹的值没有名字，于是也就没法访问了，所以这就与“导出点什么东西”的概念矛盾了。

所以 ECMAScript 6 模块约定了一个称为"default"的名字，用于来导出当前模块中的一个“值”。

```js

var varName = 100;
export default {
  varName,  // 直接导出名字
  propName: 123,  // 导出值
  funcName: function() { }, // 导出函数
  foo() { // 或导出与主对象相关联的方法
     // method
  }
}
```

export做了下面两件事：导出一个名字，为上述名字绑定一个值

和用let声明一个变量的过程是一致的。

import则做如下工作：按照语法在当前模块声明名字，添加一个当前模块对目标模块的依赖项。

有了上述的第二步操作，JavaScript 就可以依据所有它能在静态文本中发现的import语句来形成模块依赖树，最后就可以找到这个模块依赖树最顶端的根模块，并尝试加载之。

**在处理 export/import 语句的全程，没有表达式被执行**

按照 JavaScript 的约定，匿名函数表达式可以理解为一个函数的“字面量（值）;

```js
let a = function b () {

}

a();
b();//此时会报错 b is not defined
```

export ...语句通常是按它的词法声明来创建的标识符的，例如export var x = ...就意味着在当前模块环境中创建的是一个变量，并可以修改等等。但是当它被导入时，在import语句所在的模块中却是一个常量，因此总是不可写的。

由于export default ...没有显式地约定名字“default（或default）”应该按let/const/var的哪一种来创建，因此 JavaScript 缺省将它创建成一个普通的变量（var），但即使是在当前模块环境中，它事实上也是不可写的，因为你无法访问一个命名为“default”的变量——它不是一个合法的标识符。

