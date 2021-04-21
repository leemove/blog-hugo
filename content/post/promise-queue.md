---
title: "Promise Queue"
date: 2021-04-20T17:58:07+08:00
draft: false
tags: []
categories: ["前端"]
---

# 实现一个Promise.queue

可能会有如下的业务场景，我们需要一次请求多个后台接口，第二个请求在第一个请求结束后进行操作，可以抽象成以下代码。

```js
Promise.queue([
  () => promise1,
  () => promise2,
  () => promise3
]).then();

Promise.queue([1, 2, 3].map((index) => {
  return () => new Promise((resolve) => {
    setTimeout(() => {
      resolve(index);
    }, index * 1000);
  });
})).then((data) => {
  // 6s 后输出
  console.log(data); // [1, 2, 3];
});
```





```js
Promise.queue = function (fns) {
    if (fns.length === 0) {
        Promise.resolve([])
    }
    const res = []
    function go (index) {
        const fn = fns[index]
        if (fn) {
            return fn().then((data) => {
                res.push(data)
                return go(index+1)
            })
        } else {
            return Promise.resolve(res)
        }
    }
    return go(0)
}
```

