# Web开发基础


Web开发基础
<!--more-->

# Web开发基础

## 前端

### JavaScript

- 在哪些地方可以运行JavaScript

1.HTML的`<script></script>`之间

2.HTML的事件属性中 如onclick

3.浏览器的JavaScript控制台中（Console）

```html
<html>
<head>
<title>test</title>
</head>
<body>
<p onclick="changetext(this)" id="intro">Hello World!</p>
<script>
function changetext(id)
{
    id.innerHTML="谢谢";
}
</script>
<h1 onclick="changetext(this)">请点击该文本</h1>

<p onclick="changetext(this)" id="intro2">Hello World!</p>
</body>
</html>
```

### JavaScript DOM

用JavaScript HTML DOM来操作HTML

- 如何获取一个HTML元素内容？

1.获取元素

getElementById(): 通过Id获取元素

2.获取元素内容

.innerHTML:获取元素内容

- 如何修改一个HTML元素内容？

1.获取元素

2.获取元素内容

直接等于号赋值

- 如何创建动态的HTML元素内容？

`document.write()`

- 如何增加互动？

 `onclick="alert(1)"`
 `onclick=clickfunction(this)`

### JavaSrcirpt BOM

 - 如何让浏览器来警告用户？

 1.警告弹窗`alert("")`

 2.确认弹窗`confirm("")`

 3.提示弹窗`prompt("","")`

 TIPS:
 常用于简单的调试和信息展示 如XSS漏洞的测试

 - 如何从浏览器获取用户Cookie？

 从F12的Application里查看Cookie信息或者Network里面

 Cookie：通常是服务器发给用户客户端的一小段文本信息
 这个凭证Cookie就相当于我们的钥匙

 1.获取Cookie

 document.cookie

 2.写入Cookie

 document.cookie="写入值"

- 其他对浏览器的获取和操作行为

1.获取浏览器屏幕信息

`window.screen`

2.获取/控制用户页面URL

window.location

window.location.href=""

3.获取访问者浏览器信息

window.navigator

4.操作浏览器窗口

window.open("")

window.close()

### 小结

**JavaScript**

本身提供动态反应

**BOM**

浏览器对象模型

Browser Object Model

本质：连接浏览器和编程语言的

**DOM**

文档对象模型

Document Object Model

本质：连接Web页面和编程语言的

