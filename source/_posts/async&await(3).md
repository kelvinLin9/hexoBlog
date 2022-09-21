---
title: Promise的語法糖：async/await (3)
date: 2022-09-21 10:04:09
tags:
- javascript
categories:
- javascript
- 非同步
---

## 前言
接續上一篇，這篇來改寫一些範例，多熟悉 async/await 寫法。

## promise.all

```javascript
Promise.all([ promiseCook('來一客' , 3),
              promiseCook('雙響砲' , 4),
              promiseCook('滿漢大餐' , 6)
            ])
.then((res) => {
  console.log(res)
})
.catch((err) => {
  console.log(err)
})
```

用 async/await 改寫

```javascript
async function asyncFnAll() {
  try {
    const res = await Promise.all([ 
      promiseCook('來一客' , 3),
      promiseCook('雙響砲' , 2),
      promiseCook('滿漢大餐' , 5)
    ])
    console.log(res)
  } catch(err) {
    console.log(err)
  }
}
asyncFnAll();
```

## 撰寫高可用性高的錯誤捕捉

上面的寫法其實沒那麼漂亮，因為變成每一次使用 `async` 、 `await` 時都必須寫很多 `try...catch...`，因此 `try...catch...` 結構是可以省略的，但這邊要注意 `catch` 的部分，因為我們會使用 `try...catch...` 的主要原因在於捕捉錯誤並提示出來，因此 `catch` 的部分必須是具有一制性的（意指統一行為，如 `window.alert(error);` 等）。

```javascript
async function asyncFn() {
  const res1 = await promiseCook('來一客' , 3);
  console.log(res1);
  const res2 = await promiseCook('來兩客' , 5);
  console.log(res2);
  const res3 = await promiseCook('來三客' , 2);
  console.log(res3);
  }
// catch 部分拉出來寫
const catchError = (asFn) => {
  return asFn.catch((err) => {
    console.log(err);
  })
}
  
catchError(asyncFn())
```










---助教例子還看不太懂 明天再看一次 先留著---

但這個寫法並不是那麼好，因為假設今天如果要傳入參數的話，然後剛好又是一個迴圈是要戳 API 的
```javascript
[1, 2].forEach(catchError(async (n) => {
  await axios.get(`https://jsonplaceholder.typicode.com/todos/${n}`)
}))
```
你會發現根本沒有辦法傳入迴圈的值，因此上面是第一種常見寫法，如果要解決這種狀況的話，就要改一下 catchError，我們必須在外層再包一個函式
```javascript
const catchError = (asFn) => {
  return (n) => {
    return asFn(n).catch((error) => {
      console.log('error', error);
    })
  }
}
```
-------

## 作品後台使用到的例子

不過這邊因為碰到需要驗證的問題，還需要處理一下

```javascript
  async function getAllOrders () {
      let allOrders = []
      let revenue = 0
      let ordersNum = 0
      for (let i = 0; i <= 6; i++) {
        const url = `https://vue3-course-api.hexschool.io/api/kelvinlin9/admin/orders?page=${i}`
        await axios
          .get(url)
          .then((res) => {
            console.log(res)
            // res.data.orders.forEach((order) => {
            //   this.allOrders.push(order)
            //   this.ordersNum += 1
            //   this.revenue += order.total
            // })
          })
          .catch((err) => console.error(err))
      }
    }
    getAllOrders ()
```
![結果](https://i.imgur.com/Kg5kxiB.png)

如果catch分開寫的話
```javascript
  async function getAllOrders () {
    let allOrders = []
    let revenue = 0
    let ordersNum = 0
    for (let i = 0; i <= 6; i++) {
      const url = `https://vue3-course-api.hexschool.io/api/kelvinlin9/admin/orders?page=${i}`
      const res = await axios.get(url)
      console.log(res)
      // res.data.orders.forEach((order) => {
      //   this.allOrders.push(order)
      //   this.ordersNum += 1
      //   this.revenue += order.total
      // })
    }
  }

  const catchError = (asFn) => {
    return asFn.catch((err) => {
      console.log(err);
    })
  }
  
  catchError(getAllOrders ())
```

感覺上也是差不多，之後再多想想用than接跟用res直接拿的差別在哪裡



## 參考資料
- [JavaScript 核心篇](https://www.hexschool.com/courses/js-core.html)
- [是 Ray 不是 Array](https://israynotarray.com/javascript/20220515/2262757023/)