# js之事件机制

# 1、事件初探

## 1.1 js事件的概述

JavaScript事件：JavaScript是基于事件驱动模型的，所有的内容几乎都可以和事件挂钩。它首先定义了一些事件，然后在具体的某个环境触发了该事件，再完成相应的操作。

事件可以这么理解：它拥有事件三要素（事件源、事件、监听器）。

- 事件源：在哪个元素上发生的，p、a、div、form表单
- 事件：到底发生了什么事件，click、mouseover、load、submit、focus
- 监听器：如何应对事件的发生，如何回应发生的事件，通常以函数的形式来出现。

重申事件的几个坑：

- 事件实际上应该称之为事件模型，事件本身只是一个单纯的行为。
- 事件不是以 on 开头的那个名称，如 onclick 不是事件，click才是事件。**onclick引用的是一个元素对象的属性**，它指向click事件类型绑定的实际处理函数。

## 1.2 js事件的几个概念

- 事件：指的是文档或者浏览器窗口中发生的一些特定交互瞬间。我们可以通过侦听器（或者处理程序）来预定事件，以便事件发生的时候执行相应的代码。

- 事件处理程序：我们用户在页面中进行的点击这个动作、鼠标移动的动作，网页页面加载完成的动作等，都可以称之为事件名称，即：click、mousemove、load等都是事件的名称。响应某个事件的函数则称为事件处理程序，或者叫做事件侦听器。事件处理程序的名字是以“on”开头，因此click事件的处理程序就是onclick。

- 事件类型：
	- UI事件：如load、unload、error、resize、scroll、select、DOMActive，是用户与页面上的元素交互时触发的。
	- 焦点事件：如blur、DOMFocusIn、DOMFocusOut、focus、focusin、focusout，在元素获得或失去焦点的时候触发，这些事件当中，最为重要的是blur和focus，有一点需要引起注意，这一类事件不会发生冒泡！
	- 鼠标与滚轮事件：如click、dblclick、mousedown、mouseenter、mouseleave、mousemove、mouseout、mouseover、mouseup，是当用户通过鼠标在页面执行操作时所触发的。
	- 滚轮事件：mousewheel（IE6+均支持）、DOMMouseScroll（FF支持的，与mousewheel效果一样）。是使用鼠标滚轮时触发的。
	- 文本事件：textInput，在文档中输入文本触发。
	- 键盘事件：keydown、keyup、keypress，当用户通过键盘在页面中执行操作时触发。
	- 合成事件：DOM3级新增，用于处理IME的输入序列。所谓IME，指的是输入法编辑器，可以让用户输入在物理键盘上找不到的字符。compositionstart、compositionupdate、compositionend三种事件。
	- 变动事件：DOMsubtreeModified、DOMNodeInserted、DOMNodeRemoved、DOMAttrModified、DOMCharacterDataModified等，当底层DOM结构发生变化时触发。IE8-不支持。
	- 变动名称事件：指的是当元素或者属性名变动时触发，当前已经弃用！
	
对于事件的基本类型，随着HTML5的出现和发展，又新增了HTML5事件、设备事件、触摸事件、手势事件

- 事件流：描述的是从页面中接收事件的顺序。
 	- 事件冒泡：事件开始的时候由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点（文档）。

	- 事件捕获：事件开始的时候由最不具体的节点接收，然后逐级向下传播到最具体的节点。

IE与原来的NetScape(网景)，对于事件流提出的是完全不同的顺序。IE团队提出的是**事件冒泡流**；NetScape的事件流是**事件捕获流**。

- 事件对象：在触发DOM上的某个事件的时候，会产生一个事件对象event，而在这个对象当中会包含着所有与事件有关的信息。

- 事件委托：给元素的父级或者祖级，甚至页面绑定事件，然后利用事件冒泡的基本原理，通过事件目标对象进行检测，然后执行相关操作。

- 移除事件处理程序：每当将一个事件处理程序指定给一个元素时，在运行中的浏览器代码与支持页面交互的JavaScript代码之间就会建立一个连接。连接数量也直接影响着页面的执行速度。所以，当内存中存在着过时的“空事件处理程序”的时候，就会造成Web应用程序的内存和性能问题。

上述的这些概念会在下面几个环节中逐个提出并解释，下面大概会有事件源（DOM）、事件绑定（处理程序）、事件对象、事件类型等几个部分介绍。


# 2、事件源

之所以这环节说DOM，一方面是因为它是事件三要素之一，另一方面可以看出javascript在操作HTML文档时，中间折现出的一些原理。下面对DOM只说大概历程，不谈具体方法。

## 2.1 DOM

Document Object Model：文档对象模型，是一组描述脚本与结构化文档进行交互的web的标准，它定义了一系列对象、方法和属性，用于访问、操作和创建文档中的内容、结构和行为。

- D：documnet，文档，html文档或者xml文档
- O：object，对象，在转成树模型的时候，得到的对象，它有相应的属性和方法，利用他们可以完成任何操作。
- M：model，模型，树模型，有节点构成的一颗树。节点（元素、属性和文本）转成对象。

在W3C的标准中，DOM是独于平台和语言的接口，它允许程序和脚本动态地访问和更新文档的内容、结构和样式。

W3C DOM由以下三部分组成：

- 核心DOM - 针对任何结构化文档的标准模型
- XML DOM - 针对 XML 文档的标准模型
- HTML DOM - 针对 HTML 文档的标准模型

javascript是ECMAscript的产物、DOM是W3C的产物、两者结合相得益彰。

### 2.1.1 DOM0、DOM1、DOM2、DOM3的区别

![DOM0](http://i.imgur.com/QKTvVkw.png)


实际上是未形成标准的试验性质的初级阶段的DOM，现在习惯上被称为DOM0。它定义了一些document的属性和方法，提供了查询和操作Web文档的内容API。

常在使用的有：forms，cookie，title，其它不建议使用。

W3C结合网景和IE的优点于1998年10月推出了一个标准化的DOM，完成了第一级DOM，即：DOM1。W3C将DOM定义为一个与平台和编程语言无关的接口，通过这个接口程序和脚本可以动态的访问和修改文档的内容、结构和样式。

DOM1级主要定义了HTML和XML文档的底层结构。在DOM1中，DOM由两个模块组成：DOM Core（DOM核心）和DOM HTML。其中，DOM Core规定了基于XML的文档结构标准，通过这个标准简化了对文档中任意部分的访问和操作。DOM HTML则在DOM核心的基础上加以扩展，添加了针对HTML的对象和方法，如：JavaScript中的Document对象。

DOM2级在原来DOM的基础上又扩充了鼠标、用户界面事件、范围、遍历等细分模块，而且通过对象接口增加了对CSS的支持。

在DOM2中引入了下列模块，在模块包含了众多新类型和新接口：

- DOM视图（DOM Views）：定义了跟踪不同文档视图的接口
- DOM事件（DOM Events）：定义了事件和事件处理的接口
- DOM样式（DOM Style）：定义了基于CSS为元素应用样式的接口
- DOM遍历和范围（DOM Traversal and Range）：定义了遍历和操作文档树的接口

DOM3进一步扩展了DOM，在DOM3中引入了以下模块：

- DOM加载和保存模块（DOM Load and Save）：引入了以统一方式加载和保存文档的方法
- DOM验证模块（DOM Validation）：定义了验证文档的方法
- DOM核心的扩展（DOM Style）：支持XML 1.0规范，涉及XML Infoset、XPath和XML Base

其整体的发展历程如下：

![DOM发展图](http://i.imgur.com/Jk2kwXu.png)

# 3、事件绑定

## 3.1 事件的绑定方式

第一种方式:DOM0级，即以属性的方式直接写在行内。一般的验证码切换就有这样的机制。

	<a href="#" id="dom0" onclick="changeCaptcha();">

第二种方式：DOM1级，给元素添加属性（例：onclick），属性的值就是一个具体的函数（click事件类型绑定的处理函数）。这里就有一个问题，无法允许团队不同人员对同一元素监听同一事件但做出不用的响应。

	<body>
	  <div id="event">这是事件机制学习</div>
	  <script>
	    var div=document.getElementById('event');
	    // 甲程序猿
	    div.onclick=function(){
	        console.log('甲要红背景');
	        div.setAttribute('style', 'background: #ff0000');
	    };
	    // 乙程序猿
	    div.onclick=function(){
	        console.log('乙要黄背景');
	        div.setAttribute('style', 'background: #ffff00');
	    };
	  </script>
	</body>

![DOM1点击响应效果](http://i.imgur.com/KJFc3MD.png)

这里面出现的问题：无法给同一个元素绑定多个相同的事件，然而在web开发中，这个是非常普遍的一个应用。

第三种方式：DOM2级，对主流浏览器来说，使用`addEventListener`和`removeListener`方法，它们都接受3个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。最后的布尔值参数如果是true，表示在捕获阶段调用事件处理程序；如果是false，表示在冒泡阶段调用事件处理程序。若最后的布尔值不填写，则和false效果一样。这里它支持同一dom元素注册多个同种事件，还有新增了`捕获`和`冒泡`的概念。

	<body>
	  <div id="event">这是事件机制学习</div>
	  <script>
	    var div=document.getElementById('event');
	    div.addEventListener('click', function(){
	        console.log('这是DOM2级，甲绑定事件的的响应');
	    });
	    div.addEventListener('click', function(){
	        console.log('这是DOM2级，乙绑定事件的的响应');
	    });
	  </script>
	</body>

![DOM2点击响应效果](http://i.imgur.com/rM9gxXk.png)

它也有问题：兼容性，IE8对此自定义了两个自己的方法`attachEvent`和`detachEvent`方法。同时，需要注意IE在这里是‘onclick’。

    div.attachEvent('onclick', function(){
        console.log('这是DOM2级，IE绑定事件的的响应');
    });

那么为了保持浏览器兼容性问题，我们还需要自己封装一个简单的函数去实现事件的绑定。同时，由于attachEvent()方法中的this指向window(下面会有说明)，所以需要对this进行显式修改。

	<body>
	<div id="event">这是事件机制学习</div>
	<script>
	    var div=document.getElementById('event');
	    function reponseCode(){
	        console.log('这是封装的绑定事件');
	    }
	    // 事件的绑定机制
	    function addHandle(obj, type, handle){
	        if(obj.addEventListener){
	            obj.addEventListener(type, handle，false);
	        }else if(obj.attachEvent){
	            obj.attachEvent('on'+type, function(event) {
	                return handler.call(target, event);
	            });
	        }else{
	            obj['on'+type]=handle;
	        }
	    }
	    addHandle(div, 'click', reponseCode);
	</script>
	</body>

移除事件绑定：通过addEventListener()添加的事件处理程序只能使用removeEventListener()来移除，移除时传入的参数与添加处理程序时使用的参数相同。这意味着，addEventListener()添加的匿名函数将无法移除。同理attachEvent()和detachEvent();

无效代码：

	<div id="box" style="height:30px;width:200px;background-color:pink;"></div>
	<script>
	    box.addEventListener("click",function(){
	        this.innerHTML += '1'
	    },false);
	    box.removeEventListener('click',function(){
	        this.innerHTML += '1'
	    },false);
	</script>

有效代码：

	<div id="box" style="height:30px;width:200px;background-color:pink;"></div>
	<script>
		var handle = function(){
			this.innerHTML += '1'
		};
		box.addEventListener("click",handle,false);
		box.removeEventListener('click',handle,false);    
	</script>
　
说完上面绑定事件的几种方式，这里还要指出一点，即事件处理程序中的`this`所指。

	<div id="box" style="height:100px;width:300px;background-color:pink;"
	     onclick = "console.log(this)">//<div>
	</div>
	
	<div id="box" style="height:100px;width:300px;background-color:pink;"></div>
	<script>
	    box.onclick= function(){
	        console.log(this);//<div>
	    }
	</script>
	
	<div id="box" style="height:100px;width:300px;background-color:pink;"></div>
	<script>
	    box.addEventListener('click',function(){
	        console.log(this);//<div>
	    });
	</script>
	
	<div id="box" style="height:100px;width:300px;background-color:pink;"></div>
	<script>
	    box.attachEvent('onclick',function(){
	        console.log(this);//window
	    });
	</script>

与其他三个事件处理程序不同，**IE事件处理程序的this指向window**，而非被绑定事件的元素。

## 3.2 事件流

javascript操作CSS称为脚本化CSS，而javascript与HTML的交互是通过事件实现的。事件就是文档或浏览器窗口中发生的一些特定的交互瞬间，而事件流(又叫事件传播)描述的是从页面中接收事件的顺序。

### 3.2.1 历史渊源

当浏览器发展到第四代时(IE4及Netscape4)，浏览器开发团队遇到了一个很有意思的问题：页面的哪一部分会拥有某个特定的事件？想象画在一张纸上的一组同心圆。如果把手指放在圆心上，那么手指指向的不是一个圆，而是纸上的所有圆。

两家公司的浏览器开发团队在看待浏览器事件方面还是一致的。如果单击了某个按钮，他们都认为单击事件不仅仅发生在按钮上，甚至也单击了整个页面。

但有意思的是，IE和Netscape开发团队居然提出了差不多是完全相反的事件流的概念。`IE`的事件流是`事件冒泡流`，而`Netscape`的事件流是`事件捕获流`。

一个普通的HTML文档，下面统一使用。

	<!DOCTYPE HTML>
	<html lang="en">
	<head>
	  <meta charset="UTF-8">
	  <title>Document</title>
	  <body>
	    <div></div>
	  </body>    
	</html>

### 3.2.2 'IE'的'事件冒泡流'

IE的事件流叫做事件冒泡(event bubbling)，即事件开始时由最具体的元素(文档中嵌套层次最深的那个节点)接收，然后逐级向上传播到较为不具体的节点(文档)。

如果单击了页面中的'div'元素，那么这个click事件沿DOM树向上传播，在每一级节点上都会发生，按照如下顺序传播：

	(1)    <div>
	(2)    <body>
	(3)    <html>
	(4)    document

所有现代浏览器都支持事件冒泡，但在具体实现在还是有一些差别。IE9、Firefox、Chrome、Safari将事件一直冒泡到window对象，如下。

	(1)    <div>
	(2)    <body>
	(3)    <html>
	(4)    document
	(5)    window

事件冒泡流测试代码：

	<body>
	  <div id="box" style="height: 100px;width: 300px;background: pink;"></div>
	  <button id="reset">还原</button>
	  <script>
	    var box=document.getElementById('box');
	    var reset=document.getElementById('reset');
	    reset.onclick=function(){
	        history.go();
	    };
	    box.onclick=function(){
	        box.innerHTML+='div、';
	    };
	    document.documentElement.onclick=function(){
	        box.innerHTML+='html、';
	    };
	    document.body.onclick=function(){
	        box.innerHTML+='body、';
	    };
	    document.onclick=function(){
	        box.innerHTML+='document、';
	    };
	    window.onclick=function(){
	        box.innerHTML+='window<br>';
	    };
	  </script>
	</body>

效果如下：

![事件冒泡流](http://i.imgur.com/U3sqJ6f.png)

### 3.2.3 'Netscape'的'事件捕获流'

网景的事件捕获流，思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。事件捕获的用意在于在事件到达预定目标之前就捕获它。

在事件捕获过程中，document对象首先接收到click事件，然后事件沿DOM树依次向下，一直传播到事件的实际目标，即<div>元素。

	(1)    document
	(2)    <html>
	(3)    <body>
	(4)    <div>

IE9、Firefox、Chrome、Safari等现代浏览器都支持事件捕获，但是从window对象开始捕获。

	(1)    window
	(2)    document
	(3)    <html>
	(4)    <body>
	(5)    <div>

事件捕获流代码：

	<body>
	  <div id="box" style="height: 100px;width: 300px;background: #ccc;overflow: auto;"></div>
	  <button id="reset">还原</button>
	  <script>
	    //IE8-浏览器返回div body html document
	    //其他浏览器返回div body html document window
	    var box=document.getElementById('box');
	    var reset=document.getElementById('reset');
	    reset.onclick=function(){
	        history.go();
	    };
	    box.addEventListener('click', function(){
	        box.innerHTML+='div\n';
	    }, true);
	    document.documentElement.addEventListener('click', function(){
	        box.innerHTML+='html\n';
	    }, true);
	    document.body.addEventListener('click', function(){
	        box.innerHTML+='body\n';
	    }, true);
	    document.addEventListener('click', function(){
	        box.innerHTML+='document\n';
	    }, true);
	    window.addEventListener('click', function(){
	        box.innerHTML+='window\n';
	    }, true);
	  </script>
	</body>

addEventListener()方法中的第三个参数设置为true时，即为事件捕获阶段。

### 3.2.4 'W3C'的'DOM2'

事件流又称为事件传播，DOM2级事件规定的事件流包括三个阶段：事件捕获阶段(capture phase)、处于目标阶段(target phase)和事件冒泡阶段(bubbling phase)。

首先发生的是事件捕获，为截获事件提供了机会，然后是实际的目标接收到事件，最后一个阶段是冒泡阶段，可以在这个阶段对事件做出响应，如下图。

![DOM2事件流](http://i.imgur.com/YiS9TuP.png)

即真正触发事件的dom元素，是捕获事件的终点，是冒泡事件的起点，所以这里就不区分事件了，哪个先注册，就先执行哪个。

## 3.3 事件委托

事件委托就是利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。它能让你避免对特定的每个节点添加事件监听器；相反，事件监听器是被添加到它们的父元素上。事件监听器会分析从子元素冒泡上来的事件，找到是哪个子元素的事件。

### 3.3.1 事件委托现象比拟

网摘的大牛基本都用取快递去描述事件委托这个现象，大体是这么个内容。

有三个同事预计会在周一收到快递。为签收快递，有两种办法：一是三个人在公司门口等快递；二是委托给前台MM代为签收。现实当中，我们大都采用委托的方案（公司也不会容忍那么多员工站在门口就为了等快递）。前台MM收到快递后，她会判断收件人是谁，然后按照收件人的要求签收，甚至代为付款。这种方案还有一个优势，那就是即使公司里来了新员工（不管多少），前台MM也会在收到寄给新员工的快递后核实并代为签收。

这里其实还有2层意思的：

第一，现在委托前台的同事是可以代为签收的，即程序中的现有的dom节点是有事件的；

第二，新员工也是可以被前台MM代为签收的，即程序中新添加的dom节点也是有事件的。

### 3.3.2 事件委托实现

我们以下面的代码举例：
	
	<body>
	  <ul id="outul">
	    <li id="innerli1">事件冒泡流</li>
	    <li id="innerli2">事件捕捉流</li>
	    <li id="innerli3">事件委托</li>
	    <li id="innerli4">事件对象</li>
	  </ul>
	</body>

1、假设我们要实现，点击每个li元素会在终端输出标签内的文本内容，传统的思想是这样的：我们遍历li标签，然后为他们绑定点击事件。那么脚本是这样的;

	<script>
 	  var outul=document.getElementById('outul');
	  var allli=document.getElementsByTagName('li');
	  for(var i=0; i<allli.length; i++){
	    allli[i].onclick=function(){
	      console.log(this.innerHTML);
	    }
	  }
	</script>

2、那么假设我们使用事件委托的方式呢。即我们为li标签的父级标签ul绑定点击事件，从事件冒泡流的原理来说，li的点击事件会冒泡到ul上面，这时，因为之前ul已经绑定过了点击事件，那么这个点击事件就会被触发。这里面就存在了一个问题：我们想要对不同的li标签在响应各自的点击事件时，即事件三要素之一的监听器（事件处理程序）是不一样的，那我们为ul绑定的点击事件又有什么意义呢？

为了解决上面的问题，即我们要知道点击事件被触发时，如何找到相对应的不同的li标签。Event对象(下面的环节会单独介绍)提供了一个了一个属性叫做`target`，可以返回事件的目标节点，即事件三要素之一的事件源，也就是说target可以表示为当前的事件操作的DOM，但不是真正操作DOM。

Event对象存在兼容性问题，代码大体如下，在下环节里面会具体解释。

	  <script>
	    var outul=document.getElementById('outul');
	    outul.onclick=function(event){
	        // event即Event对象
	        var ent=event || window.event;
	        var target=ent.target || ent.srcElement;
	        console.log(target);
	        if(target.nodeName.toLowerCase()=='li'){
	            console.log(target.innerHTML);
	        }
	    }
	  </script>

这时有人说，上面的都是点击li操作的是同样的效果，要是每个li被点击的效果都不一样，那么用事件委托还有用吗？继续来。

这次换原代码。
	
	<div id="box">
	  <input type="button" id="add" value="添加" />
	  <input type="button" id="remove" value="删除" />
	  <input type="button" id="move" value="移动" />
	  <input type="button" id="select" value="选择" />
	</div>

假设我们对不用的按钮实现相应的功能，简单输出终端名称来代替。

3、同父元素不同子元素事件监听器不同，传统代码。

	<script>
	  var add=document.getElementById("add");
	  var remove=document.getElementById("remove");
	  var move=document.getElementById("move");
	  var select=document.getElementById("select");
	  add.onclick = function(){
	      console.log('添加');
	  };
	  remove.onclick = function(){
	      console.log('删除');
	  };
	  move.onclick = function(){
	      console.log('移动');
	  };
	  select.onclick = function(){
	      console.log('选择');
	  }
	</script>

4、同父元素不同子元素事件监听器不同，事件委托代码。

	<script>
	  var box=document.getElementById('box');
	  box.onclick=function(event){
	      var ent=event || window.event;
	      var target=ent.target || ent.srcElement;
	      if(target.nodeName.toLowerCase()=='input'){
	          switch(target.id){
	              case 'add':
	                  console.log('添加');
	                  break;
	              case 'remove':
	                  console.log('删除');
	                  break;
	              case 'move':
	                  console.log('移动');
	                  break;
	              case 'select':
	                  console.log('选择');
	                  break;
	              default:
	                  console.log('测试');
	          }
	      }
	  };
	</script>

5、同父元素不同子元素+新添加子元素，事件委托。

	<body>
	<input type="button" id="add" value="添加" />
	<ul id="outul">
	  <li id="innerli1">事件冒泡流</li>
	  <li id="innerli2">事件捕捉流</li>
	  <li id="innerli3">事件委托</li>
	  <li id="innerli4">事件对象</li>
	</ul>
	<script>
	  var add=document.getElementById('add');
	  var outul=document.getElementById('outul');
	  add.onclick=function(){
	      var li=document.createElement('li');
	      var text=document.createTextNode('新事件');
	      li.appendChild(text);
	      outul.appendChild(li);
	  };
	  outul.onclick=function(event){
	      var ent=event || window.event;
	      var target=ent.target || ent.srcElement;
	      if(target.nodeName.toLowerCase()=='li'){
	          console.log(target.innerHTML);
	      }
	  };
	</script>
	</body>

最适合使用事件委托技术的事件包括click、mousedown、mouseup、keydown、keyup和keypress。

# 4、事件对象

Event 对象代表事件的状态，比如事件在其中发生的元素、键盘按键的状态、鼠标的位置、鼠标按钮的状态。

在触发DOM上的某个事件时，会产生一个事件对象event。这个对象中包含着所有与事件有关的信息。包括导致事件的元素，事件的类型以及其他与特定事件相关的信息。

例：当用户单击某个元素的时候,我们给这个元素注册的事件就会触发,该事件的本质就是一个函数,而该函数的形参接收一个event对象。

有浏览器都支持event对象，但支持方式不同。

## 4.1 获取事件对象

event对象是事件函数的第一个参数，如文本内容一样，IE8不支持；再者如终端输出，火狐不支持。

	<body>
	<b>IE8-浏览器输出undefined，其他浏览器则输出事件对象[object MouseEvent]</b><br />
	<div id="box" style="height: 30px; line-height: 30px; width: 200px; background: #ccc;"></div>
	<script>
	  var box=document.getElementById('box');
	  box.onclick=function(ent){
	      box.innerHTML=ent;
	      // 直接访问event变量、firefox浏览器不支持
	      console.log(event);
	  }
	</script>
	</body>

然后产生一个兼容所有浏览器的写法：

	<body>
	<div id="box" style="height: 30px; line-height: 30px; width: 200px; background: #ccc;"></div>
	<script>
	  var box=document.getElementById('box');
	  box.onclick=function(ent){
	      ent=ent || event;
	      box.innerHTML=ent;
	      console.log(ent);
	  }
	</script>
	</body>

这里在之前事件委托中已经使用到。

## 4.2 事件的属性和方法

事件对象包含与创建它的特定事件有关的属性和方法。触发的事件类型不一样，可用的属性和方法也不一样。不过，所有事件都有些共有的属性和方法。

### 4.2.1 事件类型

事件有很多类型，事件对象中的type属性表示被触发的事件类型。

	<body>
	<div id="box" style="height: 30px; line-height: 30px; width: 200px; background: #ccc;"></div>
	<script>
	  var box=document.getElementById('box');
	  box.onclick=box.onmouseover=box.onmouseout=function(ent){
	      ent=ent || event;
	      box.innerHTML=ent.type;
	  }
	</script>
	</body>

上述代码分别在鼠标移入、点击、移出时显示：mouseover、click、mouseout。

### 4.2.2 事件目标

关于事件目标，共有currentTarget、target和srcElement这三个属性。

1、currentTarget

currentTarget属性返回事件当前所在的节点，即正在执行的监听函数所绑定的那个节点，但IE8-浏览器不支持。

一般地，currentTarget与事件中的this指向相同。但在attachEvent()事件处理程序中，this指向window。之前事件绑定中已提及。

	<body>
	<ul id="outul">
	  <li>currentTarget</li>
	  <li>target</li>
	</ul>
	<script>
	    var outul=document.getElementById('outul');
	    var allli=document.getElementsByTagName('li');
	    outul.onclick=function(ent){
	        ent=ent || event;
	        allli[0].innerHTML=ent.currentTarget;
	        allli[1].innerHTML=(this===ent.currentTarget);
	    }
	</script>
	</body>

2、target

currentTarget属性返回事件正在执行的监听函数所绑定的节点，而target属性返回事件的实际目标节点，但IE8-浏览器不支持。

	<body>
	<ul id="outul" style="background: #ccc; width: 300px; height: 60px; line-height: 30px;">
	  <li>currentTarget</li>
	  <li>target</li>
	</ul>
	<script>
	    var outul=document.getElementById('outul');
	    outul.onmouseover=function(ent){
	        ent=ent || event;
	        ent.target.style.background='red';
	        console.log(ent.target);
	    };
	    outul.onmouseout = function(ent){
	        ent=ent || event;
	        ent.target.style.background='#ccc';
	        console.log(ent.target);
	    };
	</script>
	</body>

上部分代码分别有三部分可以移入、移出，分别是左部、右侧上部、右侧下部。效果是移入变红、移出回显灰色，两者终端都会输出相应节点。

3、srcElement

srcElement属性与target的功能一致，但火狐不兼容。

	<body>
	<ul id="outul" style="background: #ccc; width: 300px; height: 60px; line-height: 30px;">
	  <li>currentTarget</li>
	  <li>target</li>
	</ul>
	<script>
	    var outul=document.getElementById('outul');
	    outul.onmouseover=function(ent){
	        ent=ent || event;
	        ent.srcElement.style.background='red';
	        console.log(ent.srcElement);
	    };
	    outul.onmouseout = function(ent){
	        ent=ent || event;
	        ent.srcElement.style.background='#ccc';
	        console.log(ent.srcElement);
	    };
	</script>
	</body>

故结合以上三点，一般兼容代码如下：

	<script>
	    var handler = function(ent){
	        ent=ent || event;
	        var target=ent.target || ent.srcElement;
	    };
	</script>

### 4.2.3 事件冒泡

事件冒泡是事件流的第三个阶段，通过事件冒泡可以在这个阶段对事件做出响应。

关于冒泡，事件对象中包含bubbles、cancelBubble、stopPropagation()和stopImmediatePropagation()这四个相关的属性和方法。

1、属性bubbles

bubbles属性返回一个布尔值，表示当前事件是否会冒泡。该属性为只读属性。

发生在文档元素上的大部分事件都会冒泡，但focus、blur和scroll事件不会冒泡。所以，除了这三个事件bubbles属性返回false外，其他事件该属性都为true。

	<body>
	<button id="btn">按钮</button>
	<input id="test">
	<script>
	    var btn=document.getElementById('btn');
	    var test=document.getElementById('test');
	    btn.onclick=function(ent){
	        ent=ent || event;
	        btn.innerHTML=ent.bubbles;
	        console.log(ent.bubbles);
	    };
	    test.onfocus=function(ent){
	        test.innerHTML=ent.bubbles;
	        console.log(ent.bubbles);
	    }
	</script>
	</body>

按钮点击后，上面的值会变成true，输入框聚焦后没反应，但两者终端都会输出相应的bubbles的值。

2、方法stopPropagation()

stopPropagation()方法表示取消事件的进一步捕获或冒泡，无返回值，但IE8-浏览器不支持。

	<body>
	<button id="btn" style="width: 100px;">按钮</button>
	<input id="test">
	<script>
	    var btn=document.getElementById('btn');
	    var test=document.getElementById('test');
	    btn.onclick=function(ent){
	        ent=ent || event;
	        test.value+='按钮栏、';
	        // ent.stopPropagation();
	    };
	    document.body.onclick=function(ent){
	        ent=ent || event;
	        test.value+='文档。';
	    }
	</script>
	</body>

正常代码这样，从W3C事件流的说法，假设window->div这样是从外到内，点击事件被由外到内分别捕捉，目标接收事件，再由内到外冒泡响应。

所以上面的代码结果是在点击按钮之后，输入框会显示“按钮栏、文档。”；那么如果把阻止冒泡的语句注释删去的话，响应结果就会变成这样“按钮栏、”。

3、方法stopImmediatePropagation()

stopImmediatePropagation()方法不仅可以取消事件的进一步捕获或冒泡，而且可以阻止同一个事件的其他监听函数被调用，无返回值，但IE8-浏览器不支持。

	<body>
	<button id="btn" style="width: 100px;">按钮</button>
	<input id="test">
	<script>
	    var btn=document.getElementById('btn');
	    var test=document.getElementById('test');
	    btn.addEventListener('click', function(ent){
	        ent=ent || event;
	        test.value+='按钮栏、';
	        // ent.stopImmediatePropagation()
	    }, false);
	    btn.addEventListener('click', function(ent){
	        ent=ent || event;
	        btn.style.background='#ff0000';
	    }, false);
	    document.body.addEventListener('click', function(ent){
	        ent=ent || event;
	        test.value+='文档。';
	    }, false);
	</script>
	</body>

上面的代码结果是在点击按钮之后，输入框会显示“按钮栏、文档。”,且按钮底色会变红；那么如果把阻止冒泡的语句注释删去的话，响应结果就会变成这样“按钮栏、”，它既阻止了点击事件向body层冒泡，还阻止了同层监听点击事件底色变化。

这里面我们可以知道，事件是先注册，先调用的原则。

4、cancelBubble

cancelBubble属性只能用于阻止冒泡，无法阻止捕获阶段。该值可读写，默认值是false。当设置为true时，cancelBubble可以取消事件冒泡。该属性全浏览器支持，但并不是标准写法

	<body>
	<button id="btn" style="width: 100px;">按钮</button>
	<input id="test">
	<script>
	    var btn=document.getElementById('btn');
	    var test=document.getElementById('test');
	    btn.addEventListener('click', function(ent){
	        ent=ent || event;
	        test.value+='按钮栏、';
	        ent.cancelBubble=true;
	    }, false);
	    document.body.addEventListener('click', function(ent){
	        ent=ent || event;
	        test.value+='文档。';
	    }, false);
	</script>
	</body>

当使用stopIPropagation()方法或stopImmediatePropagation()方法时，关于cancelBubble值的变化，各浏览器表现不同。

- chrome/safari/opera中，cancelBubble的值为false。
- IE9+/firefox中，cancelBubble的值为true。

兼容处理，阻止冒泡：

	var handler = function(ent){
	    ent=ent || event;
	    if(ent.stopPropagation){
	        ent.stopPropagation();
	    }else{
	        ent.cancelBubble = true;
	    }
	}

### 4.2.4 事件流：eventPhase属性

eventPhase属性返回一个整数值，表示事件目前所处的事件流阶段，但IE8-浏览器不支持。

0表示事件没有发生，1表示捕获阶段，2表示目标阶段，3表示冒泡阶段。

	<body>
	<button id="btn" style="width: 100px;">按钮</button>
	<script>
	  document.body.addEventListener('click', function(ent){
	      ent=ent || event;
	      btn.innerHTML=ent.eventPhase;
	  }, true);
	</script>
	</body>

效果为“按钮”变成“1”。
换脚本：

	<script>
	  var btn=document.getElementById('btn');
	  btn.addEventListener('click', function(ent){
	      ent=ent || event;
	      btn.innerHTML=ent.eventPhase;
	  }, false);
	</script>


效果为“按钮”变成“2”。

换脚本：

	<script>
	  document.body.addEventListener('click', function(ent){
	      ent=ent || event;
	      btn.innerHTML=ent.eventPhase;
	  }, false);
	</script>

效果为“按钮”变成“3”。

这里大致可以看出W3C对事件流定义的三个阶段。

### 4.2.5 取消默认行为

常见的浏览器默认行为有点击链接后，浏览器跳转到指定页面；或者按一下空格键，页面向下滚动一段距离。

关于取消默认行为的属性包括cancelable、defaultPrevented、preventDefault()和returnValue。

使用：

- 在DOM0级事件处理程序中取消默认行为，使用returnValue、**preventDefault()**和**return false**都有效

- 在DOM2级事件处理程序中取消默认行为，使用**return false**无效

- 在**IE事件处理程序**中取消默认行为，使用**preventDefault()**无效

1、cancelable属性

cancelable属性返回一个布尔值，表示事件是否可以取消。该属性为只读属性。返回true时，表示可以取消。否则，表示不可取消。IE8-浏览器不支持。

	<a id="test" href="#">链接</a>
	<script>
	var test=document.getElementById('test');
	test.onclick= function(ent){
	    ent=ent || event;
	    test.innerHTML=e.cancelable;
	}
	</script>

效果：点击“链接”变成“true”。

2、preventDefault()方法

preventDefault()方法取消浏览器对当前事件的默认行为，无返回值，IE8-浏览器不支持。

	<a id="test" href="http://www.cnblogs.com">链接</a>
	<script>
	  var test=document.getElementById('test');
	  test.onclick= function(ent){
	      ent=ent||event;
	      ent.preventDefault();
	  }
	</script>

效果：不转跳。

3、returnValue属性

returnValue属性可读写，默认值是true，但将其设置为false就可以取消事件的默认行为，与preventDefault()方法的作用相同，firefox和IE9+浏览器不支持。

做兼容处理：

	var handler = function(ent){
	  ent=ent || event;
	  if(ent.preventDefault){
	      ent.preventDefault();
	  }else{
	      ent.returnValue=false;
	  }
	}

4、return false

	<script>
	  var test=document.getElementById('test');
	  test.onclick= function(ent){
	    return false;
	  }
	</script>

效果：不转跳。

5、defaultPrevented属性

defaultPrevented属性表示默认行为是否被阻止，返回true时表示被阻止，返回false时，表示未被阻止。

	<a id="test" href="http://www.cnblogs.com">链接</a>
	<script>
	  var test=document.getElementById('test');
	  test.onclick= function(ent){
	    ent=ent || event;
	    if(ent.preventDefault){
	        ent.preventDefault();
	    }else{
	        ent.returnValue=false;
	    }
	    test.innerHTML=ent.defaultPrevented;
	  }
	</script>

效果：点击“链接”变为“true”。

# 5、完整的简单事件相关代码

	<script>
	  var EventUtil={
	    // 事件对象
	    getEvent: function(event){
	      return event||window.event;
	    },
	    // 事件目标节点
	    getTarget: function(event){
	      return event.target||event.srcElement;
	    },
	    // 阻止事件的默认行为
	    preventDefault: function(){
	      if(event.preventDefault){
	        event.preventDefault();
	      }else{
	        event.returnValue=false;
	      }
	    },
	    // 阻止向上冒泡
	    stopPropagation: function(){
	      if(event.stopPropagation){
	          event.stopPropagation();
	      }else{
	          event.cancelBubble=true;
	      }
	    },
	    // DOM2级添加事件  
	    addHandler: function(element, type, handler){
	      if(element.addEventListener){
	          element.addEventListener(type, handler, false);
	      }else if(element.attachEvent){
	        element["e"+type]=function(){
	          handler.call(element)
	        };
	        element.attachEvent("on"+type, element["e"+type]);
	      }else{
	        element["on"+type]=handler;
	      }
	    },
	    // DOM2级移除事件    
	    removeHandler: function(element, type, handler){
	      if(element.removeEventListener){
	        element.removeEventListener(type, handler, false);
	      }else if(element.detachEvent){
	        element.detachEvent("on"+type, element["e"+type]);
	        element["e"+type]=null;
	      }else{
	        element["on"+type]=null;
	      }
	    }
	  };
	</script>

# 6、内存泄漏

程序的运行需要内存。只要程序提出要求，操作系统或者运行时（runtime）就必须供给内存。
对于持续运行的服务进程（daemon），必须及时释放不再用到的内存。否则，内存占用越来越高，轻则影响系统性能，重则导致进程崩溃。

不再用到的内存，没有及时释放，就叫做内存泄漏（memory leak）。

## 6.1 垃圾回收机制

大多数语言提供自动内存管理，减轻程序员的负担，这被称为"垃圾回收机制"（garbage collector）。

垃圾回收机制最常使用的方法叫做"引用计数"（reference counting）：语言引擎有一张"引用表"，保存了内存里面所有的资源（通常是各种值）的引用次数。如果一个值的引用次数是0，就表示这个值不再用到了，因此可以将这块内存释放。

	let arr = [1, 2, 3, 4];
	console.log('hello world');
	arr = null;

数组[1, 2, 3, 4]是一个值，会占用内存。变量arr是仅有的对这个值的引用，因此引用次数为1。

如果增加最下面那行代码，解除arr对[1, 2, 3, 4]引用，这块内存就可以被垃圾回收机制释放了。

JavaScript中最常用的垃圾收集方式是标记清除(mark-and-sweep)。当变量进入环境（例如，在函数中声明一个变量）时，就将这个变量标记为“进入环境”。从逻辑上讲，永远不能释放进入环境的变量所占的内存，因为只要执行流进入相应的环境，就可能用到它们。而当变量离开环境时，这将其 标记为“离开环境”。虽然JavaScript 会自动垃圾收集，但是如果我们的代码写法不当，会让变量一直处于“进入环境”的状态，无法被回收。

## 6.2 意外的全局变量

JavaScript 处理未定义变量的方式比较宽松：未定义的变量会在全局对象创建一个新变量。在浏览器中，全局对象是 window 。

可能一般写了这段代码：

	function foo(arg) { 
	  bar = "this is a hidden global variable"; 
	}

然而这段代码的执行是这样：

	function foo(arg) { 
	  window.bar = "this is a hidden global variable"; 
	}

另一种意外导致定了了全局变量：

	function foo() { 
	    this.variable = "potential accidental global"; 
	} 
	// foo 调用自己，this 指向了全局对象（window），而不是 undefined 
	foo();


在JavaScript文件头部加上 'use strict'，可以避免此类错误发生。启用严格模式解析 JavaScript ，避免意外的全局变量。

## 6.3 被遗忘的计时器或回调函数

    var someResource=getData();
    setInterval(function() {
      var node=document.getElementById('Node');
      if(node) {
        // 处理 node 和 someResource 
        node.innerHTML = JSON.stringify(someResource);
      }
    }, 1000);

如果`id`为`Node`的元素从 DOM 中移除,该定时器仍会存在,同时,因为回调函数中包含对 `someResource`的引用,定时器外面的`someResource`也不会被释放。

## 6.4 脱离DOM的引用

保存DOM节点内部数据结构很有用。假如你想快速更新表格的几行内容，把每一行 DOM 存成字典（JSON键值对）或者数组很有意义。此时，同样的**DOM元素存在两个引用：一个在 DOM 树中，另一个在字典中。**将来你决定删除这些行时，需要把两个引用都清除。

	  var elements = {
	    button: document.getElementById('button'),
	    image: document.getElementById('image'),
	    text: document.getElementById('text')
	  };
	
	  function doStuff() {
	    image.src = 'http://some.url/image';
	    button.click();
	    console.log(text.innerHTML);
	    // 更多逻辑 
	  }
	
	  function removeButton() {
	    // 按钮是 body 的后代元素 
	    document.body.removeChild(document.getElementById('button'));
	    // 此时，仍旧存在一个全局的 #button 的引用 
	    // elements 字典。button 元素仍旧在内存中，不能被 GC 回收。 
	  }


然后还有'DOM树'内部或子节点的引用问题。假如你的JavaScript代码中保存了表格某一个'td'的引用。将来决定删除整个表格的时候，直觉认为'GC'会回收除了已保存的'td'以外的其它节点。实际情况并非如此：此'td'是表格的子节点，子元素与父元素是引用关系。由于代码保留了'td'的引用，导致整个表格仍待在内存中。保存'DOM'元素引用的时候，要小心谨慎。


## 6.5 闭包

闭包是 JavaScript 开发的一个关键方面：匿名函数可以访问父级作用域的变量。

简单来说是这个样子的：

	<script>
	  var leaks=(function(){
	    var leak='closure';
	    return function(){
	      console.log(leak);
	    }
	  })();
	</script>

参见博文：[一个意想不到的js内存泄漏](http://www.cnblogs.com/friskfly/p/3171834.html)。

参考博文：[JS常见4种内存泄漏和chrome开发工具监测介绍](http://web.jobbole.com/88463/)。

全文参考博文：[javascript学习目录](http://www.cnblogs.com/xiaohuochai/p/5613593.html)。
