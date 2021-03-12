---
title: "兄弟选择器"
date: 2016-04-18T19:13:14+08:00
draft: false
tags: ["CSS"]
categories: ["前端"]
---

<!--more-->

 ## CSS3 兄弟选择器 E~F 
 ```html
 <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>$别看我$</title>
    <style>
    /*兄弟选择器*/
        .p~h2{
            color: red;
        }
    </style>
</head>
<body>
<p>asdfasf</p>
<p class="p">asdfasdf</p>
<h2>efasdf</h2>
<p>adsfasdf</p>
<!--<script src=jquery-3.1.1.js></script>-->
<script>
</script>
</body>
</html>
 ```
 代码中只有最后一个p标签被选中，兄弟选择器E~F只会选择 E 后面的兄弟元素F。