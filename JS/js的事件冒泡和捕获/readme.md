# 什么是事件
事件，就是文档或浏览器窗口中发生的一些特定的交互瞬间。JavaScript与HTML之间的交互就是通过事件实现的。可以用侦听器或处理程序来预定事件，以便事件发生时执行相应的代码。
# 事件流
事件流描述的是从页面接收事件的顺序。
**为什么会有事件流呢？**
我们知道，前端HTML代码是逐层嵌套的。也就是说，当你点击了一个按钮，点击事件不仅仅只发生在按钮上。在你点击按钮的同时，你也点击了按钮的容器元素，也点击了整个页面。就像一张纸上的一组同心圆，如果你把手指放在圆心上，那么你的手指指向的不只是最内层的一个圆，而是所有圆。

然而，对于事件流，IE和Netscape开发团队提出了几乎相反的概念。IE事件流是**事件冒泡流**（常用），也就是从内而外。Netscape Communicator的事件流是**事件捕获流**，也就是从外而内。
# 事件冒泡和事件捕获的概念
对于下面一段代码：

```html
<!DOCTYPE html>
<html>
<head>
	<title>事件流</title>
</head>
<body>
	<div id="myDiv">Click me</div>
</body>
</html>
```

## 事件冒泡
单击页面中的`<div>`元素，click事件会按照如下顺序传播：

 1. `<div>`
 2. `<body>`
 3. `<html>`
 4. `document`

事件冒泡过程：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131143524355.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNTMyMTI4,size_16,color_FFFFFF,t_70)
注：IE9、Firefox、Safari、Chrome将事件一直冒泡到`window`对象。
## 事件捕获
单击页面中的`<div>`元素，click事件会按照如下顺序传播：

 1. `document`
 2. `<html>`
 3. `<body>`
 4. `<div>`
事件捕获过程：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131143522816.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyNTMyMTI4,size_16,color_FFFFFF,t_70)
注：IE9、Firefox、Safari、Chrome都是从`window`对象开始捕获事件的。
# 事件冒泡示例
> addEventListener的第三个参数决定了是事件捕获还是事件冒泡
true:注册的事件在捕获阶段触发
false:注册的事件在冒泡阶段触发——默认值

对于以下html代码，把 addEventListener的第三个参数设置为false

```html
<!DOCTYPE html>
<html>
<head>
	<title>事件冒泡</title>
</head>
<body>
	<div id="obj1">
		外层文本
		<div id="obj2">第一段文本</div>
		<div id="obj3">第二段文本</div>
	</div>
</body>
<script type="text/javascript">
	var obj1 = document.getElementById("obj1");
	var obj2 = document.getElementById("obj2");
	var obj3 = document.getElementById("obj3");
	obj1.addEventListener('click',function(e){
		alert("obj1:::外层文本");
	},false);
	obj2.addEventListener('click',function(e){
		alert("obj2:::第一段文本");
	},false);
	obj3.addEventListener('click',function(e){
		alert("obj3:::第二段文本");
	},false);
</script>
</html>
```
点击外层文本，弹出
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131155012482.png)
点击第一段文本，弹出
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131155030593.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131155038134.png)
由此可以看到事件冒泡的过程（从内而外）。

# 事件捕获示例
将上面事件冒泡的代码中addEventListener的第三个参数改为true
点击外层文本，弹出
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131155012482.png)
点击第一段文本，弹出
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131155038134.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200131155030593.png)

由此可以看到事件冒泡的过程（从外而内）。
# 阻止冒泡
## 方法一
**w3c的方法是e.stopPropagation()，IE则是使用e.cancelBubble = true**

在js中加上代码：
```js
window.event? window.event.cancelBubble = true : e.stopPropagation();
```
即可阻止冒泡

例如将事件冒泡中的代码改为
```html
<!DOCTYPE html>
<html>
<head>
	<title>事件冒泡</title>
</head>
<body>
	<div id="obj1">
		外层文本
		<div id="obj2">第一段文本</div>
		<div id="obj3">第二段文本</div>
	</div>
</body>
<script type="text/javascript">
	var obj1 = document.getElementById("obj1");
	var obj2 = document.getElementById("obj2");
	var obj3 = document.getElementById("obj3");
	obj1.addEventListener('click',function(e){
		alert("obj1:::外层文本");
	},false);
	obj2.addEventListener('click',function(e){
		window.event? window.event.cancelBubble = true : e.stopPropagation();	// 添加的代码
		alert("obj2:::第一段文本");
	},false);
	obj3.addEventListener('click',function(e){
		alert("obj3:::第二段文本");
	},false);
</script>
</html>
```
再点击第一段文本时，只会弹出  obj2:::第一段文本。
## 方法二（jQuery，同时会阻止默认事件）
**return false**

javascript的return false只会阻止默认行为，但是用jQuery的话则既阻止默认行为又防止对象冒泡。