---
title: H5中新添加的表单元素
date:  2016-04-11T20:39:39+08:00
draft: false
tags: ["H5"]
categories: ["前端"]
---

## H5中添加的新表单有有以下几种
```html
 <form action="#">
    <input type="color">
    <input type="date">
    <label for="">请输入邮箱:</label>
    <input type="email" placeholder="请输入邮箱" autofocus>
    <input type="tel">
    <input type="number">
    <input type="search">
    <input type="range"  id='myRange'><span id='output'>0</span>
  </form>
```
## 新增的表单事件
	* onchange 在确定每次修改value之后触发 滑动滑块事件中会在鼠标松开后触发
	* oninput 会在用户输入时候触发 滑块事件中 每当用户滑动就会触发
	* oninvalid 会在用户输入的表单不合法时候触发事件
		**这个事件中一般使用setCustomvalidity("");来设置输入错误时显示的提示语
###滑块输入和滑块数值的显示例子
```javascript
 var huakuai=document.getElementById("myRange");
  huakuai.oninput=function () {
    var val=this.value;
    document.getElementById("output").innerText=val;
  }//配合上面代码块一起
```
##新增表单的属性
	>新增的表单相比于传统的表单只是修改了部分特殊的显示或者其他特效，将原由的输入标签划分的更加细致
	*required属性 这个表单必须要填 才能进行提交
	*placeholder=“提示内容” 占位符 当你没有进行输入时 他会显示出灰色字符串 输入时消失
	*pattern=“正则表达式” 他会按照你输入的正则表达式对表单value进行校验，校验失败则无法提交 不用写/^   $/。 可以配合invalid 事件 和setCustomvalidity()方法来修改浏览器默认样式进行提示。
##新增加的表单标签
	*效果类似于搜索框的单选框
```html
<input type="text" list="foodlist" >
  <datalist id='foodlist'>
    <option value="vegetable">西兰花</option>
    <option value="beef">牛肉</option>
    <option value="rice">泰国香米</option>
  </datalist>
```
* outpu标签 语义化标签 输出的意思
* Keygen标签 对表单进行加密
  * 以上两者都不推荐使用

```html
<output>123</output>
  <br>
  <keygen name="">
```
