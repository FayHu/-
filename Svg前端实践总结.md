# Svg 前端实践总结

> SVG 意为可缩放矢量图形（Scalable Vector Graphics）。
SVG 使用 XML 格式定义图像。

　　本文是学习和使用svg的一些总结，不介绍svg作图的方法。  
　　svg标签元素参考：[MDN - svg](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/svg)  
　　结构化标签也可参考：[SVG中的结构化、分组和引用元素](http://www.w3cplus.com/svg/structuring-grouping-referencing-in-svg.html)  
　　svg的icon方案参考：[腾讯干货！超实用的高清图标SVG解决方案全总结（下）](http://www.uisdc.com/svg-icon-part-two)

### 一、通过js生成svg元素
　　svg与普通html元素不同，需要指定命名空间，所以使用``createElement()``、``appendChild()``抑或JQ的``append()``方法添加到dom上的svg元素是无法正常显示的。需要用到``createElementNS(ns,'svg')``，另外有些svg标签也需要单独的命名空间，例如a标签。为了避免每次添加这么长的命名空间，使用时可以简单包装下(``NS_SVG``、``NS_XLINK``分别为svg和a标签命名空间)：

```javascript
	var 
		NS_SVG = "http://www.w3.org/2000/svg",
		NS_XLINK = "http://www.w3.org/1999/xlink";
	
	var _createSvgEle = function(tagName){
		return document.createElementNS(NS_SVG,tagName);
	};
	
	//eg
	document.body.appendChild(_createSvgEle('svg'));
	_createSvgEle('a').setAttributeNS(NS_XLINK,
                     'xlink:href', 
                     'http://baidu.com');
```

### 二、通过js操作svg元素　
　　SVG如果是嵌套在HTML中的话，即使用svg标签，会和其他dom元素共用一个document对象，因此可以直接通过该对象来获取到SVG元素对象；如果使用object、embed、iframe标签来引入svg,则其中的内容会有自己document对象，此时的操作需要借助``getSVGDocument()``方法：

```	javascript
	document.getElementById('svg-foreign').getSVGDocument();
```
　　对于object、iframe来引入的svg对象，还可以使用``contentDocumnet``属性来获取。当然，contentDocument不是专为svg设计的，同时可以获取其他内嵌xml、html对象。  
　　操作的前提需要满足[同源策略(Same-origin policy)](https://en.wikipedia.org/wiki/Same-origin_policy)，跨域将无法获取document对象。
　　
### 三、svg平移、缩放的应用
　　平移和缩放表面看起来很简单，svg的平移缩放机制也不复杂，但是dom元素往往不固定宽度，应用场景也多变。例如有时候我们希望随浏览器响应，svg按照比例自动缩放；有时候我们希望为svg添加手动缩放功能或者手动拖拽平移功能（类似于百度地图的缩放平移机制）。  
　　关于svg的viewBox相关知识，张鑫旭有篇文章讲的很详细：[理解SVG viewport,viewBox,preserveAspectRatio缩放](http://www.zhangxinxu.com/wordpress/?p=4323)。这里谈谈在具体的应用场景中如何利用这些属性。
　　
### 四、svg动画的实现方式
> [JavaScript操作SVG的一些知识](http://blog.iderzheng.com/something-about-svg-with-javascript/)