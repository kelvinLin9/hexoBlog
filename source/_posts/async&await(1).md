---
title: Promise的語法糖：async/await
date: 2022-09-19 11:24:12
tags:
- javascript
categories:
- javascript
- 非同步
---

## 前言
這個主題有滿多東西想寫的，所以就分成幾篇比較好閱讀

## Async 是什麼?
`async/await`語法也是用來處理非同步程式，它背後的操作原理與 Promise 是一樣的，所以也被稱為 Promise 的語法糖，它可以把Promise寫得更直覺和簡潔(但其實也是見人見智)。

async函式一定會回傳一個Promise物件，即使例子中的func不是一個Promise物件，它也會被包裝成Promise物件。
## 為什麼需要 Async
開始之前，先複習一下 Promise 寫法

```javascript
function promiseFn (boolean) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if(boolean) {
        resolve('resolve');
      } else {
        reject('reject')
      }
    }, 1000)
  })
}

promiseFn(true)
  .then((res) => {
    console.log('resolve', res);
  })
  .catch((err) => {
    console.log('reject', err);
  })

```

換成 Async 與 Await

首先 Async 是必須寫在宣告函式之前，順便複習一下各種函式寫法

```javascript
async function fn() {
  ...
}
const fn = async function() {
  ...
}
const fn = async () => {
  ...
}
```

呼叫 `promiseFn` 的方式改寫呢，其實就是使用 Async 來重寫

```javascript
async function asyncFn() {
  const res = await promiseFn(true);
  console.log(res); // resolve
}

asyncFn();

```

這邊要注意 `await` 僅會用於接收 Promise 表達式結果，而使用 `await` 之後程式碼會被迫堵塞住等待該行執行完畢，因此就會有點類似逐行執行的概念運作，也就不會發生事件佇列（非同步）的狀況發生。

```javascript
async function asyncFn() {
  console.log('1'); // 1
  const res = await promiseFn(true);
  console.log('2', res); // 2 resolve
}

asyncFn();

```

而這邊假設如果要如前面 Promise 一樣可以接下一個 then 的話該怎麼撰寫呢？其實非常簡單

```javascript
async function asyncFn() {
  console.log('1'); // 1
  const res1 = await promiseFn(true);
  console.log('2', res1); // 2 resolve
  const res2 = await promiseFn(true);
  console.log('3', res2); // 3 resolve
}

asyncFn();

```

因此整體來講 Async 與 Await 可以說是真正解決 Promise 的聖經語法，讓整體閱讀性更高。

但實務開發上來講必定會遇到錯誤，那錯誤發生時該怎麼做呢？那這時候就要使用錯誤捕捉的方式

```javascript
async function asyncFn() {
  try {
    console.log('1'); // 1
    const res1 = await promiseFn(true);
    console.log('2', res1); // 2 resolve
    const res2 = await promiseFn(false); // 製造失敗
  } catch(error) {
    console.log('error', error);
  }
}

asyncFn();
```

## 參考資料
- [JavaScript 核心篇](https://www.hexschool.com/courses/js-core.html)