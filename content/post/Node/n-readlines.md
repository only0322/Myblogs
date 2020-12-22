---
title: "n-readlines具体使用"
date: 2020-11-25T15:44:45+08:00
draft: true
---

### 简介

n-readlines是我发现的一个按行读文件模块，使用起来也比readline简单。

### 示例

```js
const lineByLine = require('n-readlines');
const liner = new lineByLine('./test/fixtures/normalFile.txt');
 
let line;
let lineNumber = 0;
 
while (line = liner.next()) {
    console.log('Line ' + lineNumber + ': ' + line.toString('ascii'));
    lineNumber++;
}
 
console.log('end of line reached');
```



