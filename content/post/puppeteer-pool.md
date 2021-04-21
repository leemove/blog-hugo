---
title: Puppetter连接池
date:  2021-04-20T14:15:39+08:00
draft: true
tags: ["puppeteer"]
categories: ["前端"]
---


# Puppetter连接池

## 场景说明

puppeteer目前已经被广泛应用于各种场景的业务中，假设我们有一个频繁截屏网页的需求，我们很容易想到以下伪代码。

```js
async function main() {
    const broswer = await puppeteer.launch()
    const page = await broswer.newPage()
    await page.goto('https://www.163.com')
    await page.screenshot({
        path: './screen.jpg'
    })
}
```

缺点也十分明显，首先就是每次截屏都要开启浏览器，而且每个浏览器对系统的开销都比较庞大，我们往往只是需要一个新的标签页。

我们的api设计可以是这样的。

```js
const pool = await createPool({
    maxPagesCount: 20 //浏览器最大页面数量
})
const page = await pool.getPage()
```

