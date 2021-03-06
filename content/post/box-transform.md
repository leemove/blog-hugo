---
title: "小盒子触摸特效"
date: 2016-07-12T15:36:33+08:00
draft: false
tags: []
categories: ["前端"]
---
# 小盒子触摸特效
在许多网页中都有的小盒子特效，鼠标进入小盒子后，小盒子内的图片和文字产生一种类似滑动的特效
<!--more-->
## 2D小特效
实现的效果如下: 
<br>
![2d1.gif](https://www.tuchuang001.com/images/2017/01/31/2d1.gif)
<br>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>$想不想知道后面写的是什么$</title>
    <style>
        div.wai{
            margin:50px auto;
            width: 180px;
            height: 180px;
            overflow: hidden;
        }
        div.nei{
            height: 100%;
            background-color: white;
            opacity: 0.6;
            position: absolute;
            width: 180px;
            transform: translateY(-100%);
            z-index: 3;
            text-align: center;
            transition: all 1s;
            height: 180px;
        }
        .wai:hover img{
            transition: all 1s;
            transform: scale(1.2,1.2);
        }
        .wai:hover .nei{
            transform: translateY(0);

        }
    </style>
</head>
<body>
<div class="wai">
    <div class="nei">
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ad, architecto unt, tempora? Accusantium eligendi id ipsa laborum quidem?
    </div>

    <img src="https://www.tuchuang001.com/images/2017/01/25/touxiang.jpg
" alt="">
</div>
</body>
</html>
```
## 盒子3d小特效
效果如下:
[![3d1.gif](https://www.tuchuang001.com/images/2017/01/31/3d1.gif)](https://www.tuchuang001.com/image/H2LH8)
<br>   
代码如下:
<br>                                
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>$想不想知道后面写的是什么$</title>
    <style>
        .wai{
            /*background-color: gray;*/
            overflow: hidden;
            perspective: 800px;
            width: 180px;
            height: 180px;
            margin:50px auto;
            border: 1px solid black;
        }
        .pic{
            position: absolute;
            width: 180px;
            height: 180px;
            background: url("https://www.tuchuang001.com/images/2017/01/25/touxiang.jpg");
            transition: all 1s;
            transform-origin: bottom center;
        }
        .news{
            width: 180px;
            position: absolute;
            background-color: white;
            opacity: 0.8;
            transform: translateY(-100%);
            transition: all 1s;
            /*width: 100%;*/
        }
        .wai:hover .news{
            transform: translateY(0);
        }
        .wai:hover .pic{
            transform: rotateX(70deg);
        }
    </style>
</head>
<body>
<div class="wai">
    <div class="news">
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. A cum, numquam quam saepe ut velit!
    </div>
    <div class="pic">

    </div>
</div>
</body>
</html>
```
##  小骰子特效
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>$想不想知道后面写的是什么$</title>
    <style>
        div{
            width: 200px;
            height: 200px;
            /*position: absolute;*/
            /*设置文字到DIV中央*/
            text-align: center;
            line-height: 200px;
            font-size: 100px;
        }
        .box{
            position: relative;
            margin:100px auto;
            /*开启3D渲染功能*/
            transform-style: preserve-3d;

        }
        .box div{
            position: absolute;
            opacity:0.7;

        }
        .box div:nth-child(1){
            background-color: hotpink;
            /*保证骰子的滚动围绕着中间 向前走宽度的一半*/
            transform: translateZ(100px);
        }
        .box div:nth-child(2){
            background-color: coral;
            transform: translateZ(-100px);
        }
        .box div:nth-child(3){
            background-color: aqua;
            /*先旋转再移动 旋转过程中z轴也进行了旋转*/
            transform: rotateY(90deg) translateZ(100px);
        }
        .box div:nth-child(4){
            background-color: brown;
            transform: rotateY(-90deg) translateZ(100px);
        }
        .box div:nth-child(5){
            background-color: darkblue;
            transform: rotateX(90deg) translateZ(100px);
        }
        .box div:nth-child(6){
            background-color: dimgray;
            transform: rotateX(-90deg) translateZ(100px);
        }
        .box:hover{
            transition: all 2s linear;
            transform:  rotateY(360deg) rotateZ(360deg);
        }
    </style>
</head>
<body>
<div class="box">
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <div>5</div>
    <div>6</div>
</div>
</body>
</html>
```
效果如下: <br>
![shaizi.gif](https://www.tuchuang001.com/images/2017/01/31/shaizi.gif)
<br>
注意点：
* transform-style: preserve-3d 这个属性是开启3d渲染，浏览器默认是不开启的，也就是无法进行任何的3D操作。渲染3d非常占用电脑的资源。
* 骰子拼接侧面和顶面底面时候先进行旋转，Z轴也就跟着转变方向。
