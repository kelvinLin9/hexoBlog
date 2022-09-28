---
title: Promise的語法糖：async/await (2)
date: 2022-09-20 10:04:09
tags:
- JavaScript
categories:
- JavaScript
- 非同步
---

## 前言
接續上一篇，這篇來談談Async Function 與 Promise 之間的關聯性

## Async Function 與 Promise 之間的關聯性

上個章節的程式碼，這邊使用的 Async Function 改成表達式

```javascript
function promiseCook(foodName , time) {
  return new Promise(function(resolve, reject) { 
    setTimeout(() => {
      if (time >= 3 && time <= 5) {
        resolve(`${foodName}泡了${time}分鐘，好吃`)
      } else {
        reject(`${foodName}泡了${time}分鐘，難吃`)
      }
    },time*1000)
  })
}

const asyncFn = async () => {
  try {
    const res1 = await promiseCook('來一客' , 3);
    console.log(res1); 
    const res2 = await promiseCook('來兩客' , 2);
    console.log(res2); 
  } catch(err) {
    console.log(err)
  }
}

console.log(asyncFn())
```
因為接下來將會直接一探究竟 `async` 到底做了什麼事情，因此這邊我們使用一下 `console.log(asyncFn)` 來看一下輸出結果是什麼

![](https://i.imgur.com/HGpTgCq.png)

我們可以看到它回傳的會是一個 Promise，因此我們可以知道一件事情當 `async function` 被執行時，它就會被轉換成 Promise，因此我們就可以使用 `then` 來串接，這兩者是可以混合使用的。

## 參考資料
- [JavaScript 核心篇](https://www.hexschool.com/courses/js-core.html)
- [是 Ray 不是 Array](https://israynotarray.com/javascript/20220513/3060050230/)