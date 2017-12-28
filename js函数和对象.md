# 函数和对象

# 1、函数

## 1.1 函数概述

函数对于任何一门语言来说都是核心的概念。通过函数可以封装任意多条语句，而且可以在任何地方、任何时候调用执行。在javascript中，函数即对象，程序可以随意操控它们。函数可以嵌套在其他函数中定义，这样他们就可以访问它们被定义时所处的作用域中的任何变量，它给javascript带来了非常强劲的编程能力。

### 1.1.1 函数定义

共有三种函数定义的方式。

1、函数声明语句

使用function关键字，后跟一组参数及函数体。

	function funcname([arg1 [,arg2 [...,argn]]]){
	    statement;
	}

funcname是要声明的函数名称的标识符。函数名之后的圆括号是参数列表，参数之间用逗号分割。当调用函数时。这些标识符指代传入的函数的实参。

function语句里的花括号是必需的，这和while循环和其他一些语句所用的语句块是不同的，即使函数体内只包含一条语句，仍然必须使用花括号给其括起来。

	function test()//SyntaxError: Unexpected end of input
	function test(){};//不报错
	while(true);//不报错

a、提升

在作用域系列里面，提到过函数声明提升（hoisting），函数名和函数体都提升。

	foo();
	function foo(){
	    console.log(1);//1
	}

上面这个代码之所以能够在控制台输出1，就是因为foo()函数声明进行了提升，如下所示：

	function foo(){
	    console.log(1);
	}
	foo();

b、重复

变量的重复声明是无用的，但函数的重复声明会覆盖前面的声明（无论是变量还是函数声明）。

**变量的重复声明无用。**

	//变量的重复声明无用
	var a = 1;
	var a;
	console.log(a);//1

---

**函数声明提升优先于变量声明提升。**

	//由于函数声明提升优先于变量声明提升，所以变量的声明无作用
	var a;
	function a(){
	    console.log(1);
	}
	a();//1

---

**后面的函数声明会覆盖前面的函数声明。**

	//后面的函数声明会覆盖前面的函数声明
	a();//2
	function a(){
	    console.log(1);
	}
	function a(){
	    console.log(2);
	}

所以，应该避免在同一作用域进行函数重复声明。

c、删除

和变量声明一样，函数声明语句创建的变量无法被删除。

	function foo(){
	    console.log(1);
	}
	delete foo;//false
	console.log(foo());//1

2、函数定义表达式

以表达式的方式来定义函数，函数的名称是可选的。

	var functionName = function([arg1 [,arg2 [...,argn]]]){
	    statement;
	}
	
	var functionName = function funcName([arg1 [,arg2 [...,argn]]]){
	    statement;
	}

匿名函数(anonymous function)也叫做拉姆达函数，是function关键字后面没有标识符的函数。

通常来说，以表达式方式定义的函数都不要名称，这回让定义他们的代码更为紧凑。函数定义表达式特别适用那种只会使用一次的函数。

	var tensquared = (function(x) {return x*x;}(10));

而一个函数定义表达式包含名称，函数的局部作用域将会包含**一个绑定到函数对象的**名称。实际上，函数的名称将称为函数内部的一个局部变量。

	var test = function fn(){
	   return fn;
	}
	console.log(test);//fn(){return fn;}
	console.log(test());//fn(){return fn;}
	console.log(test()());//fn(){return fn;}

个人理解，**对于具名的函数表达式来说，函数名称相当于函数对象的形参，只能在函数内部使用；而变量名称相当于变量对象的实参，在函数内部和外部都可以使用。**
	
	var test = function fn(){
	   return fn === test;
	}
	console.log(test());//true
	console.log(test === fn);//ReferenceError: fn is not defined

函数定义了一个非标准的name属性，通过这个属性可以访问到给定函数指定的名字，这个属性永远跟在function关键字后面的标识符相等。匿名函数的name属性为空。

	//IE11-浏览器无效，均输出undefined
	//chrome在处理匿名函数的name属性时有问题，会显示函数表达式的名字
	function fn(){};
	console.log(fn.name);//'fn'
	var fn = function(){};
	console.log(fn.name);//''，在chrome浏览器中会显示'fn'
	var fn = function abc(){};
	console.log(fn.name);//'abc'

3、Function构造函数

Function构造函数接收任意数量的参数，但最后一个参数始终都会被看作函数体，而前面的参数则枚举了新函数的参数。
	
	var functionName = new Function(['arg1' [,'arg2' [...,'argn']]],'statement;');

Function构造函数无法指定函数名称，它创建的额是一个匿名函数。

从技术上来说，这是一个函数表达式。但，不推荐使用，因为这种写法会导致解析两次代码。第一次是解析常规javascript代码，第二次解析传入构造参数中的字符串，影响性能。

	var sum = new Function('num1','num2','return num1 + num2');
	//等价于
	var sum = function(num1,num2){
	    return num1+num2;
	}

**Function()构造函数常见的函数，其函数体的编译总是会在全局作用域中执行。**于是，Function()构造函数类似在全局函数中执行的eval()。

	var test = 0;
	function fn(){
	    var test = 1;
	    return new Function('return test');
	}
	console.log(fn()());//0

但，并不是所有的函数都可以成为构造函数(constructor)。

	var o = new Math.min();//Uncaught TypeError: Math.min is not a constructor

### 1.1.2 函数返回值

函数中的return语句用来返回调用函数后的返回值。

	return expression;

如果没有return语句，则函数调用仅仅会依次执行函数内的每一条语句直至结束，最后返回调用程序。这种情况下，调用表达式的结果是undefined。

	var test = function fn(){}
	console.log(test());//undefined

当执行到return语句时，函数终止执行，并返回expression的值给调用程序。

	var test = function fn(){
	    return 2;
	};
	console.log(test());//2

并不是函数中return语句后的所有语句都不执行，finally语句是例外，return语句不会阻止finally子句的执行。

	function testFinnally(){
	    try{
	        return 2;
	    }catch(error){
	        return 1;
	    }finally{
	        return 0;
	    }
	}
	testFinnally();//0

由于javascript可以自动插入分号，因此return关键字和它后面的表达式之间不能有换行。

	var test = function fn(){
	    return
	    2;
	};
	console.log(test());//undefined

一个函数中可以根据条件判断有多个return语句。

	function diff(iNum1, iNum2) {
	  if (iNum1 > iNum2) {
	    return iNum1 - iNum2;
	  } else {
	    return iNum2 - iNum1;
	  }
	}

return语句可以单独存在而不必带有expression，这样的话也会向调用程序返回undefined。

	var test = function fn(){
	    return;
	};
	console.log(test());//undefined

return语句经常作用函数内部的最后一条语句出现，这是因为return语句可用来是函数提前返回。当return被执行时，函数立即返回而不再执行余下的语句。
	
	//并没有弹出1
	var test = function fn(){
	    return;
	    alert(1);
	};
	console.log(test());//undefined

**如果函数调用时在前面加上了new前缀，且返回的不是一个对象，则返回this（该新对象）。**

	function fn(){
	    this.a = 2;
	    return 1;
	}
	var test = new fn();
	console.log(test);//{a:2}
	console.log(test.constructor);//fn(){this.a = 2;return 1;}

如返回值是一个对象，则返回该对象。
	
	function fn(){
	    this.a = 2;
	    return {a:1};
	}
	var test = new fn();
	console.log(test);//{a:1}
	console.log(test.constructor);//Object() { [native code] }

### 1.1.3 函数调用

只有函数被调用时，才会被执行。调用运算符是跟在**任何产生一个函数值的表达式之后**的一对圆括号，圆括号内可包含零个或多个用逗号隔开的表达式。每个表达式产生一个参数值，每个参数值被赋予函数声明时定义的形参名。

javascript中一共4中函数调用模式：函数调用模式、方法调用该模式、构造器调用模式和间接调用模式。

1、函数调用模式

当一个函数并非是一个对象的属性时，那么它就是被当作一个函数来调用的。对于普通的函数调用来说，函数的返回值就是调用表达式的值。

	function add(x,y){
	    return x+y;
	}
	var sum = add(3,4);
	console.log(sum)//7

使用函数调用的模式下调用函数时，非严格模式时，this被绑定到全局对象window；在严格模式下，this是undefined。

	function add(x,y){
	    console.log(this);//window
	}    
	add();

---
	
	function add(x,y){
	    'use strict';
	    console.log(this);//undefined
	}    
	add();//window

因此，this可以用来判断当前是否是严格模式。

	var strict = (function(){return !this;}());

注意，因为函数调用模式中的函数中的this是指向window，即绑定到了全局对象，所以会发生全局属性被重写的现象。

	var a = 0;
	function fn(){
	    this.a = 1;
	}
	fn();
	console.log(this,this.a,a);//window 1 1

同时再注意下面的代码与之的区别，详情见作用域。

	var a = 0;
	function fn(){
	    var a = 1;
	}
	fn();
	console.log(this,this.a,a);//window 0 0

2、方法调用模式

当一个函数被保存为一个对象的属性时，我们称之为方法。当一个方法被调用时，this被绑定到该对象。如果调用表达式包含一个提取属性的动作，那么它就是被当作一个方法来调用。

	var o = {
	    m: function(){
	        console.log(1);
	    }
	};
	o.m();//1

方法可以使用this来访问自己所属的对象，所以它能从对象中取值或对对象进行修改。this到对象的绑定发生在调用的时候。通过this可取得它们所属对象的上下文的方法称为公共方法。

	var o = {
	    a: 1,
	    m: function(){
	        return this;
	    },
	    n: function(){
	        this.a = 2;
	    }
	};
	console.log(o.m().a);//1
	o.n();
	console.log(o.m().a);//2

对象的属性会被修改。

	var o = {
	    a: 1,
	    m: function(){
	        return this;
	    },
	    n: function(){
	        this.a = 2;
	    }
	};
	o.n();
	console.log(o.m().a);//2
	console.log(o.m().a);//2

**任何函数主要作为方法调用实际上都会传入一个隐式的实参：**这个实参是一个对象，方法调用的母体就是这个对象，通常来讲，基于那个对象的方法可以执行多种操作，方法调用的语法已经很清晰地表明了函数将基于一个对象来操作。

	rect.setSize(width,height);
	setRectSize(rect,width,height);

假设上面的两行代码功能完全一样，它们都作用于一个假定的对象rect。可以看出，第一行的方法调用语法非常清晰地表明这个函数的执行载体是rect对象，函数的所偶操作都将基于这个对象。

和变量不同，**关键字this没有作用域的限制，嵌套的函数不会从调用它的函数中继承this。**如果嵌套函数作为函数调用，其this不是全局对象（非严格模式）就是undefined（严格模式）。

	var o = {
	    m: function(){
	         function n(){
	             return this;
	         }
	         return n();
	    }
	}
	console.log(o.m());//window

--- 

	var o = {
	    m: function(){
	         function n(){
	             'use strict';
	             return this;
	         }
	         return n();
	    }
	}
	console.log(o.m());//undefined

**如果想访问这个外部函数this值，需要将它保存在一个变量里，这个变量和内部函数都在同一个作用域内。通常使用变量self或that来保存this。**

	var o = {
	    m: function(){
	        var self = this;
	        console.log(this === o);//true
	         function n(){
	             console.log(this === o);//false
	             console.log(self === o);//true
	             return self;
	         }
	         return n();
	    }
	}
	console.log(o.m() === o);//true

3、构造函数调用模式

如果函数或者方法调用之前带有关键字new，它就构成构造函数调用。

	function fn(){
		console.log(this);
	    this.a = 1;
	};
	var obj = new fn();
	console.log(obj.a);//1

如果构造函数调用在圆括号内包含一组实参列表，先计算这些实参表达式，然后传入函数内。

	function fn(x){
	    this.a = x;
	};
	var obj = new fn(2);
	console.log(obj.a);//2

如果构造函数没有形参，javascript构造函数调用的语法就是允许省略实参列表和圆括号的。凡是没有形参的构造函数都可以省略圆括号。

	var o = new Object();
	//等价于
	var o = new Object;

**尽管构造函数看起来像一个方法调用，它依然会使用这个新对象作用调用上下文。也就是说，在表达式中new o.m();，调用上下文并不是o。**

	var o = {
	    m: function(){
	        return this;
	    }
	}
	var obj = new o.m();
	console.log(obj,obj === o);//{} false
	console.log(obj.constructor === o.m);//true

**构造函数通常不使用return关键字，它们通常初始化对象，当构造函数的函数体执行完毕时，它会显示返回。在这种情况下，构造函数调用表达式的计算结果就是这个新对象的值。**

	function fn(){
	    this.a = 2;
	}
	var test = new fn();
	console.log(test);//{a:2}

**如果构造函数使用return语句但没有指定返回值，或者返回一个原始值，那么这时将忽略返回值，同时使用这个新对象作为调用结果。**

	function fn(){
	    this.a = 2;
	    return;
	}
	var test = new fn();
	console.log(test);//{a:2}

**如果构造函数显示地使用return语句返回一个对象，那么调用表达式的值就是这个对象。**

	var obj = {a:1};
	function fn(){
	    this.a = 2;
	    return obj;
	}
	var test = new fn();
	console.log(test);//{a:1}

4、间接调用模式

javascript中函数也是对象，函数对象既可以包含方法。call()和apply()方法可以用来间接地调用函数。

这两个方法都允许显示地指定调用所需地this值，也就是说，任何函数都可以作为任意对象的方法来调用，哪怕这个函数不是那个对象的方法。两个方法都可以指定调用的实参。call()方法使用它自有的实参列表作用函数的实参，apply()则要求以数组的形式传入参数。

	var obj = {};
	function sum(x,y){
	    return x+y;
	}
	console.log(sum.call(obj,1,2));//3
	console.log(sum.apply(obj,[1,2]));//3

## 1.2 函数参数

javascript函数的参数与大多数语言的函数有所不同。函数不介意传递的参数有多少个参数，也不在意传进来的参数是什么类型，甚至可以不传参数。下面就详细探讨。

### 1.2.1 arguments

javascript中的函数定义并未指定函数形参的类型，函数调用也未对传入的实参值进行任何的检查。实际上，javascript函数调用甚至不检查传入形参的个数。

	function add(x){
	    return x+1;
	}
	console.log(add(1));//2
	console.log(add('1'));//'11'
	console.log(add());//NaN
	console.log(add(1,2));//2

1、同名形参

在非严格模式下，函数中可以出现同名形参，且只能访问最后出现该名称的形参。

	function add(x,x,x){
	    return x;
	}
	console.log(add(1,2,3));//3

而在严格模式下，出现同名形参会抛出语法错误。

	function add(x,x,x){
	    'use strict';
	    return x;
	}
	console.log(add(1,2,3));//SyntaxError: Duplicate parameter name not allowed in this context

2、参数个数

当实参比函数声明指定的形参个数要少，剩下的形参都将设置为undefined。

	function add(x,y){
	    console.log(x,y);//1 undefined
	}
	add(1);

常常使用逻辑或运算符给省略的参数设置一个合理的默认值。

	function add(x,y){
	    y = y || 2;
	    console.log(x,y);//1 2
	}
	add(1);

实际上，使用y || 2是不严谨的，显示地设置假值(undefined、null、false、0、-0、''、NaN)也会得到同样的结果。所以这里应该根据实际场合进行合理的设置。

当实参比形参的个数要多时，剩下的形参没有办法直接获得，需要使用即将提到的arguments对象（实参列表）。

javascript中的参数在内部用一个数组来表示。函数接收到的始终都是这个数组，而不关心数组中包含哪些参数。在函数体内可以通过arguments对象来访问这个参数数组，从而获取传递给函数的每一个参数。arguments对象并不是Array的实例，它是一个类数组对象，可以使用方括号语法来访问它的每一个元素。

	function add(x){
	    console.log(arguments[0],arguments[1],arguments[2])//1 2 3
	    return x+1;
	}
	add(1,2,3);

**arguments对象的length属性显示实参的个数，函数的length属性显示形参的个数。**

	function add(x,y){
	    console.log(arguments.length)//3
	    return x+1;
	}
	add(1,2,3);
	console.log(add.length);//2

形参只是提供便利，但不是必需的。
	
	function add(){
	    return arguments[0] + arguments[1];
	}
	console.log(add(1,2));//3

3、对象参数

当一个函数包含超过3个参数时，要记住调用函数中的实参的正确顺序实在让人肉疼。

	function arraycopy(/*array*/from,/*index*/form_start,/*array*/to,/*index*/to_start,/*integer*/length){
	    //todo
	}

通过名/值对的形式来引入参数。这样参数的顺序就无关紧要了。定义函数的时候，传入的实参都写入一个单独的对象之中，在调用的时候传入一个对象，对象中的名/值对是真正需要的实参数据。

	function arraycopy(/*array*/from,/*index*/form_start,/*array*/to,/*index*/to_start,/*integer*/length){
	    //todo
	}

通过名/键对的形式来传入参数，这样的参数的顺序就无关紧要了。定义函数的时候，传入的实参都写入一个单独的对象之中，在调用的时候传入一个对象，对象的名/键对是真正需要的实参数据。

	function easycopy(args){
	    arraycopy(args.from,args.from_start || 0,args.to,args.to_start || 0, args.length);
	}
	var a = [1,2,3,4],b =[];
	easycopy({from:a,to:b,length:4});

4、同步

当形参个数与实参个数相同时，arguments对象的值和对应形参的值保持一致。

	function test(num1,num2){
	    console.log(num1,arguments[0]);//1 1
	    arguments[0] = 2;
	    console.log(num1,arguments[0]);//2 2
	    num1 = 10;
	    console.log(num1,arguments[0]);//10 10
	}
	test(1);

虽然命名参数和对应的arguments对象的值相同，但并不是相同的命名空间。它们的命名空间是独立的，但值是同步。

但在严格模式下，arguments对象的值和形参的值是独立的。

	function test(num1,num2){
	    'use strict';
	    console.log(num1,arguments[0]);//1 1
	    arguments[0] = 2;
	    console.log(num1,arguments[0]);//1 2
	    num1 = 10;
	    console.log(num1,arguments[0]);//10 2
	}
	test(1);

当形参没有对应的实参时，arguments对象的值与形参的值并不对应。

	function test(num1,num2){
	    console.log(num1,arguments[0]);//undefined,undefined
	    num1 = 10;
	    arguments[0] = 5;
	    console.log(num1,arguments[0]);//10,5
	}
	test();

### 1.2.2 内部属性

1、callee

arguments对象有一个名为callee的属性，该属性是一个指针，指向拥有这个arguments对象的函数。

下面是经典的阶乘函数。

	function factorial(num){
	    if(num <=1){
	        return 1;
	    }else{
	        return num* factorial(num-1);
	    }
	}    
	console.log(factorial(5));//120

但是，上面这个函数的执行和函数名仅仅耦合在一起，可以使用arguments.callee进行函数解耦。

	function factorial(num){
	    if(num <=1){
	        return 1;
	    }else{
	        return num* arguments.callee(num-1);
	    }
	}    
	console.log(factorial(5));//120

但在严格模式下，访问这个属性会抛出TypeError错误。

	function factorial(num){
	    'use strict';
	    if(num <=1){
	        return 1;
	    }else{
	        return num* arguments.callee(num-1);
	    }
	}    
	//TypeError: 'caller', 'callee', and 'arguments' properties may not be accessed on strict mode functions or the arguments objects for calls to them
	console.log(factorial(5));

这时可以使用具名的函数表达式。

	var factorial = function fn(num){
	    if(num <=1){
	        return 1;
	    }else{
	        return num*fn(num-1);
	    }
	};    
	console.log(factorial(5));//120

2、caller 

实际上有两个caller属性。

a、函数的caller

函数的caller属性保存调用当前函数的函数引用，如果是在全局作用域中调用当前函数，它的值是null。

	function outer(){
	    inner();
	}
	function inner(){
	    console.log(inner.caller);//outer(){inner();}
	}
	outer();

---

	function inner(){
	    console.log(inner.caller);//null
	}
	inner();

在严格模式下，访问这个属性会抛出TypeError错误。

	function inner(){
	    'use strict';
	    //TypeError: 'caller' and 'arguments' are restricted function properties and cannot be accessed in this context
	    console.log(inner.caller);
	}
	inner();

b、arguments对象的caller

该属性始终是undefined，定义这个属性是为了分清arguments.caller和函数的caller属性。

	function inner(x){
	    console.log(arguments.caller);//undefined
	}
	inner(1);

同样地，在严格模式下，访问这个属性会抛出TypeError错误。

	function inner(x){
	    'use strict';
	    //TypeError: 'caller' and 'arguments' are restricted function properties and cannot be accessed in this context
	    console.log(arguments.caller);
	}
	inner(1);

### 1.2.3 函数重载

javascript函数不能像传统意义上那样实现重载，而在其他语言当中，可以为一个函数写两个定义，只要这两个定义的签名（接收参数的类型和数量不同即可），强类型语言才行。

javascript函数没有签名，因为其参数都是由包含0或多个值的数组来表示的；而没有函数签名，真正的函数重载是不可能做到的。

	//后面的声明覆盖了前面的声明
	function addSomeNumber(num){
	    return num + 100;
	}
	function addSomeNumber(num){
	    return num + 200;
	}
	var result = addSomeNumber(100);//300

只能通过检查传入函数中参数的类型和数量做出不同的响应，来模仿方法的重载。

	function doAdd(){
	    if(arguments.length == 1){
	        alert(arguments[0] + 10);
	    }else if(arguments.length == 2){
	        alert(arguments[0] + arguments[1]);
	    }
	}
	doAdd(10);//20
	doAdd(30,20);//50

### 1.2.4 参数传递

javascript中**所有的函数参数都是按值传递的**。也就是说把函数外部的值赋值到函数内部的参数，就和把值从一个变量复制到另一个变量一样。

1、基本类型值

在向参数传递基本类型的值时，被传递的值会被复制给另外一个局部变量（命名参数或arguments对象的一个元素）。

	function addTen(num){
	    num += 10;
	    return num;
	}
	var count = 20;
	var result = addTen(count);
	console.log(count);//20，没有变化
	console.log(result);//30

2、引用类型值

在向参数传递引用类型的值时，会把这个值在内存中的地址复制给另一个局部变量，因此这个局部变量的变化会反映在函数的外部。

	function setName(obj){
	    obj.name = 'test';
	}
	var person = new Object();
	setName(person);
	console.log(person.name);//'test'

当在函数内部重写引用类型的形参时，这个变量引用的就是一个局部对象了。而这个局部对象执行完毕后立即被销毁。

	function setName(obj){
	    obj.name = 'test';
	    console.log(person.name);//'test'
	    obj = new Object();
	    obj.name = 'white';
	    console.log(person.name);//'test'
	}
	var person = new Object();
	setName(person);

## 1.3 函数的属性和方法

函数是javascript中特殊的对象，可以拥有属性和方法，就像普通的对象拥有属性和方法一样。甚至可以用Function()构造函数来创建新的函数对象。下面是对函数方法和属性的介绍。

### 1.3.1 属性

1、length属性

之前介绍过arguments的length属性表示实参个数，而函数的length属性表示形参个数。

	function add(x,y){
	    console.log(arguments.length)//3
	    console.log(add.length);//2
	}
	add(1,2,3);

2、name属性

函数定一了一个非标准的name属性，通过这个属性可以访问到给定函数指定的名字，这个属性永远跟function关键字后面的标识符，匿名函数的name属性为空。

	//IE11-浏览器无效，均输出undefined
	//chrome在处理匿名函数的name属性时有问题，会显示函数表达式的名字
	function fn(){};
	console.log(fn.name);//'fn'
	var fn = function(){};
	console.log(fn.name);//''，在chrome浏览器中会显示'fn'
	var fn = function abc(){};
	console.log(fn.name);//'abc'  

name属性早被浏览器广泛支持，但直到ES6才被写入标准。

ES6对这个属性的行为做出了一些修改。如果将一个匿名函数赋值给一个变量，ES5的name属性，会返回空字符串，而ES6的name属性会返回实际的函数名。

	var func1 = function () {};
	func1.name //ES5:  ""
	func1.name //ES6: "func1"

如果一个具名函数赋值给一个变量，则ES5和ES6的name属性都返回这个具名函数原本的名字。
	
	var bar = function baz() {};
	bar.name //ES5: "baz"
	bar.name //ES6: "baz"

Function构造函数返回的函数实例，name属性为anonymous。

	(new Function).name // "anonymous"

bind返回的函数，name属性值会加上“bound”前缀。

	function foo() {};
	foo.bind({}).name // "bound foo"
	(function(){}).bind({}).name // "bound "

3、prototype属性

每个函数都有一个prototype属性，这个属性指向一个对象的引用，这个对象称做原型对象（prototype object）。每一个函数都包含不同的原型对象。将函数用作构造函数时，新创建的对象会从原型对象上继承属性。

	function fn(){};
	var obj = new fn;
	fn.prototype.a = 1;
	console.log(obj.a);//1

### 1.3.2 方法

每个函数都包含两个非继承而来的方法：apply()和call()。这个方法的用途都是在特定的作用域下调用函数，实际上等于函数体内this对象的值。

**1、apply()和call()**

要向以对象o的方法来调用函数f()，可以这么使用call()和apply()。

	f.call(o, arg1. arg2);
	f.apply(o. [arg1, arg2]);

假设o不存在m方法，则等价于：

	o.m = f; //将f存储为o的临时方法
	o.m(); //调用它，不传入参数
	delete o.m; //将临时方法删除

下面是一个实际的例子：

	window.color = "red";
	var o = {color: "blue"};
	function sayColor(){
	    console.log(this.color);
	}
	sayColor();            //red
	sayColor.call(this);   //red
	sayColor.call(window); //red
	sayColor.call(o);      //blue

sayColor.call(o)等价于:

	o.sayColor = sayColor;
	o.sayColor();   //blue
	delete o.sayColor;

apply()方法接收两个参数：一个是在其中运行函数的作用域（或者可以说是调用函数的母对象，它是调用上下文，在函数体内通过this来获得对它的引用），另一个是参数数组。其中，第二个参数可以是Array的实例，也可以arguments对象。

	function sum(num1, num2){
	    return num1 + num2;
	}
	//因为运行函数的作用域是全局作用域，所以this代表的是window对象
	function callSum1(num1, num2){
	    return sum.apply(this, arguments);
	}
	function callSum2(num1, num2){
	    return sum.apply(this, [num1, num2]);
	}
	console.log(callSum1(10,10));//20
	console.log(callSum2(10,10));//20

call()方法和apply()方法的作用相同，它们的区别仅仅在于接收参数的方式不同。对于call()方法而言，第一个参数是this值没有变化，变化的是其余参数都是直接传递给函数。换句话说，在使用call()方法时，传递给函数的参数必须逐一列举出来。

	function sum(num1, num2){
	    return num1 + num2;
	}
	function callSum(num1, num2){
	    return sum.call(this, num1, num2);
	}
	console.log(callSum(10,10));   //20

至于在使用apply()还是call()，取决于采取哪种函数传递参数的方式最方便，如果打算直接传入arguments对象，或者包含函数中先接收到的也是一个数组，那么使用apply()肯定更方便，否则，选择call()可能更合适。

在非严格模式下，使用函数call()或apply()方法时，null或undefined值会被转为全局对象。而在严格模式下，函数的this值始终是指定的值。

	var color = 'red';
	function displayColor(){
	    console.log(this.color);
	}
	displayColor.call(null);//red

---

	var color = 'red';
	function displayColor(){
	    'use strict';
	    console.log(this.color);
	}
	displayColor.call(null);//TypeError: Cannot read property 'color' of null

apply()与call()应用

a、调用对象的原生方法

	var obj = {};
	obj.hasOwnProperty('toString');// false
	obj.hasOwnProperty = function (){
	  return true;
	};
	obj.hasOwnProperty('toString');// true
	Object.prototype.hasOwnProperty.call(obj, 'toString');// false

b、找出数组最大元素

javascript不提供找出数组最大元素的函数，结合使用apply方法和Math.max方法，就可以返回数组的最大元素。

	var a = [10, 2, 4, 15, 9];
	Math.max.apply(null, a);//15

---

	var obj={};
	var a = [10, 2, 4, 15, 9];
	Math.max.apply(obj, a);//15

c、将类数组转为真正的数组

	Array.prototype.slice.apply({0:1,length:1});//[1]

或者

	[].prototype.slice.apply({0:1,length:1});//[1]

d、绑定回调函数的对象

由于apply方法（或者call方法）不仅绑定函数执行所在的对象，还会立即执行函数，因此不得不把绑定语句写在一个函数体内，更简洁的写法是采用下面介绍的bind方法。

	var o = {};
	o.f = function () {
	  console.log(this === o);
	}
	var f = function (){
	  o.f.apply(o);
	};
	$('#button').on('click', f);

**2、bind()**

bind()是ES5新增的方法，这个方法的主要作用是将函数绑定到某个对象。

当在函数f()上调用bind()方法并传入一个对象o作为参数，**这个方法返回一个新的函数。**以函数调用的方式调用新的函数将会把原始的函数f()当作o的方法来调用，传入新函数的任何实参都将传入原始函数。但IE-8浏览器不兼容。

	function f(y){
	    return this.x + y; //这个是待绑定的函数
	}
	var o = {x:1};//将要绑定的对象
	var g = f.bind(o); //通过调用g(x)来调用o.f(x)
	g(2);//3

兼容处理：

	function bind(f,o){
	    if(f.bind){
	        return f.bind(o);
	    }else{
	        return function(){
	            return f.apply(o,arguments);
	        }
	    }
	} 

bind()方法不仅是将函数绑定到一个对象，它还附带一些其它的作用：除了第一个实参之外，传入bind()的实参也会绑定到this，这个附带的应用是一种常见的函数式编程技术，有时也被称为‘**柯里化（currying）**’。
	
	var sum = function(x,y){
	    return x+y;
	}
	var succ = sum.bind(null,1);
	succ(2); //3，x绑定到1，并传入2作为实参y

---

	function f(y,z){
	    return this.x + y + z;
	}
	var g = f.bind({x:1},2);
	g(3); //6，this.x绑定到1，y绑定到2，z绑定到3

使用bind()方法实现柯里化可以对函数进行拆分。
	
	function getConfig(colors,size,otherOptions){
	    console.log(colors,size,otherOptions);
	}
	var defaultConfig = getConfig.bind(null,'#c00','1024*768');
	defaultConfig('123');//'#c00 1024*768 123'
	defaultConfig('456');//'#c00 1024*768 456'

**3、toString()**

**函数的toString()实例方法是返回函数代码的字符串，而静态toString()方法返回一个类是‘[native code]’的字符串作为函数体。**

	function test(){
	    alert(1);//test
	}
	test.toString();/*"function test(){
	                    alert(1);//test
	                  }"*/
	Function.toString();//"function Function() { [native code] }"

**4、toLocalString()**

函数的toLocalString()方法和toString()方法返回的结果相同。

	function test(){
	    alert(1);//test
	}
	test.toLocaleString();/*"function test(){
	                    alert(1);//test
	                  }"*/
	Function.toLocaleString();//"function Function() { [native code] }"

**5、valueOf()**

函数的valueOf()方法返回函数本身。

	function test(){
	    alert(1);//test
	}
	test.valueOf();/*function test(){
	                    alert(1);//test
	                  }*/
	typeof test.valueOf();//'function'
	Function.valueOf();//Function() { [native code] }

## 1.4 ES6函数扩展

ES6标准关于函数扩展部分，主要涉及到一下四个方面：参数默认值、rest函数、扩展运算符（...）和箭头函数。

### 1.4.1 参数默认值

**1、一般情况**

一般地，为擦描述设置默认值需要进行如下设置：

	function log(x, y) {
	  y = y || 'World';
	  console.log(x, y);
	}

但这样实际上是由问题的，如果y的值本身是假值（false、undefined、null、‘’、0、NaN），则无法取得值本身。

	function log(x, y) {
	  y = y || 'World';
	  console.log(x, y);
	}
	log('Hello') // Hello World
	log('Hello', 'China') // Hello China
	log('Hello', NaN) // Hello World

ES6允许为函数的参数设置默认值，即直接写在参数定义的后面。

	function log(x, y = 'World') {
	  console.log(x, y);
	}
	log('Hello') // Hello World
	log('Hello', 'China') // Hello China
	log('Hello', NaN) // Hello NaN

参数变量是默认声明的，不能用let或cinst再次声明。

	function foo(x = 5) {
	  let x = 1; //SyntaxError: Identifier 'x' has already been declared
	  const x = 2; //SyntaxError: Identifier 'x' has already been declared
	}

**2、尾参数**

通常情况下，定义了默认值的参数，应该是函数的尾参数。因为这样比较容易看出来，到底省略了那些参数。如果尾部的参数设置默认值，实际上这个参数是没法省略的。

	function f(x = 1, y) {
	  return [x, y];
	}
	f() // [1, undefined]
	f(2) // [2, undefined])
	f(, 1) // 报错
	f(undefined, 1) // [1, 1]

如果传入undefined，将触发该参数等于默认值，null则没有这个效果。

	function foo(x = 5, y = 6) {
	  console.log(x, y);
	}
	foo(undefined, null)// 5 null

**3、length**

指定了默认值以后，函数的length属性，将返回没有指定默认值的参数个数。

	(function (a) {}).length // 1
	(function (a = 5) {}).length // 0
	(function (a, b, c = 5) {}).length // 2

如果设置了默认值的参数不是尾参数，那么length属性也不再计入后面的参数了。

	(function (a = 0, b, c) {}).length // 0
	(function (a, b = 1, c) {}).length // 1

**4、作用域**

a、如果参数默认值是一个变量，则该变量所处的作用域，与其它变量的作用域规则是一样的，**即先是当前函数的作用域，然后才是全局作用域。**

	var x = 1;
	function f(x, y = x) {
	  console.log(y);
	}
	f(2) // 2

b、如果函数调用，函数作用域内部的变量x没有生成，则x指向全局变量。

	var x = 1;
	function f(y = x) {
	  var x = 2;
	  console.log(y);
	}
	f() // 1

**5、应用**

**利用函数默认值，可以指定某一个参数不得省略，如果省略就抛出一个错误。**
	
	function throwIfMissing() {
	  throw new Error('Missing parameter');
	}
	function foo(mustBeProvided = throwIfMissing()) {
	  return mustBeProvided;
	}
	foo()// Error: Missing parameter

将参数默认值设为undefined，表明这个参数可以省略。

	function foo(optional = undefined) {
	    //todo
	}

### 1.4.2 rest参数

ES6引入rest参数（形式为“**...变量名**”），用于获取函数的多余部分，这样就不需要使用arguments对象了。**rest参数搭配的变量是一个数组，该变量将多余的参数放入此数组中。**

	function add(...values) {
	  var sum = 0;
	  for (var val of values) {
	    sum += val;
	  }
	  return sum;
	}
	add(2, 5, 3) //10

[关于for...of的用法](http://www.csdn.net/article/1970-01-01/2824965)。

rest参数中的变量代表一个数组，所有数组特有的方法都可以用于这个变量。

下面是利用rest参数改写数组push方法的例子。

	function push(array, ...items) {
	  items.forEach(function(i) {
	    array.push(i);
	    console.log(i);
	  });
	}
	var a = [];
	push(a, 1, 2, 3);

函数的length属性也不包括rest参数。

	(function(a) {}).length  // 1
	(function(...a) {}).length  // 0
	(function(a, ...b) {}).length  // 1

rest参数之后不能再有其它的参数。

	//Uncaught SyntaxError: Rest parameter must be last formal parameter
	function f(a, ...b, c) {
	  //todo
	}

### 1.4.3 扩展运算符

扩展运算符（spread）是三个点（...）。它好比是rest参数的逆运算，**将一个数组转为逗号分隔的参数序列。**

	console.log(...[1, 2, 3])// 1 2 3
	console.log(1, ...[2, 3, 4], 5)// 1 2 3 4 5
	[...document.querySelectorAll('div')]// [<div>, <div>, <div>]

该运算符主要用于函数调用。

	Math.max方法的简化。
	
	// ES5
	Math.max.apply(null, [14, 3, 77])
	
	// ES6
	Math.max(...[14, 3, 77])
	
	//等同于
	Math.max(14, 3, 77)

push方法的简化：
	
	// ES5
	var arr1 = [0, 1, 2];
	var arr2 = [3, 4, 5];
	Array.prototype.push.apply(arr1, arr2);
	
	// ES6
	var arr1 = [0, 1, 2];
	var arr2 = [3, 4, 5];
	arr1.push(...arr2);

扩展运算符可以将字符串转为真正的数组。

	console.log([...'hello']);// [ "h", "e", "l", "l", "o" ]

### 1.4.4 胖运算符

详情见 `js对象继承1.3` 箭头函数。

## 1.5 javascript函数中的5个技巧

函数对于任何一门语言都是一个核心的概念，在javascript中更是如此，下面是函数的5个高级技巧。

### 1.5.1 作用域安全的构造函数

构造函数其实就是一个使用new操作符调用的函数。

	function Person(name,age,job){
	    this.name=name;
	    this.age=age;
	    this.job=job;
	}
	var person=new Person('match',28,'Software Engineer');
	console.log(person.name);//match

如果没有使用new操作符，原本针对Person对象的三个属性被添加到window对象。

	function Person(name,age,job){
	    this.name=name;
	    this.age=age;
	    this.job=job;
	}          
	var person=Person('match',28,'Software Engineer');
	console.log(person);//undefined
	console.log(window.name);//match

如果是使用new操作符，原本针对Person对象的三个属性被添加到window对象。

	function Person(name,age,job){
	    this.name=name;
	    this.age=age;
	    this.job=job;
	}          
	var person=Person('match',28,'Software Engineer');
	console.log(person);//undefined
	console.log(window.name);//match

window的name属性是用来标识链接目标和框架，这里对该属性的偶然覆盖可能会导致页面上的其他错误，这个问题的解决方法就创建一个作用域安全的构造函数。

	function Person(name,age,job){
	    if(this instanceof Person){
	        this.name=name;
	        this.age=age;
	        this.job=job;
	    }else{
	        return new Person(name,age,job);
	    }
	}
	var person=Person('match',28,'Software Engineer');
	console.log(window.name); // ""
	console.log(person.name); //'match'
	var person= new Person('match',28,'Software Engineer');
	console.log(window.name); // ""
	console.log(person.name); //'match'

但是，对构造函数窃取模式的继承，会带来副作用。这是因为，在下列代码中，this对象并非是Polygon对象实例，所以构造函数Polygon()会创建一个新的实例。

	function Polygon(sides){
	    if(this instanceof Polygon){
	        this.sides=sides;
	        this.getArea=function(){
	            return 0;
	        }
	    }else{
	        return new Polygon(sides);
	    }
	}
	function  Rectangle(wifth,height){
	    Polygon.call(this,2);
	    this.width=this.width;
	    this.height=height;
	    this.getArea=function(){
	        return this.width * this.height;
	    };
	}
	var rect= new Rectangle(5,10);
	console.log(rect.sides); //undefined

如果要使用作用域安全的构造函数窃取模式的话，需要结合原型链继承，重写Rectangle的prototype属性，是它的实例也变成Poltgon的实例。

	function Polygon(sides){
	    if(this instanceof Polygon){
	        this.sides=sides;
	        this.getArea=function(){
	            return 0;
	        }
	    }else{
	        return new Polygon(sides);
	    }
	}
	function  Rectangle(wifth,height){
	    Polygon.call(this,2);
	    this.width=this.width;
	    this.height=height;
	    this.getArea=function(){
	        return this.width * this.height;
	    };
	}
	Rectangle.prototype= new Polygon();
	var rect= new Rectangle(5,10);
	console.log(rect.sides); //2

### 1.5.2 惰性载入函数

因为各浏览器之间的行为差异，我们经常在函数中包含了大量的if语句，以检查浏览器特性，解决不同浏览器的兼容问题。比如，我们最常见的dom节点添加事件的函数。

	function addEvent(type, element, fun) {
	    if (element.addEventListener) {
	        element.addEventListener(type, fun, false);
	    }
	    else if(element.attachEvent){
	        element.attachEvent('on' + type, fun);
	    }
	    else{
	        element['on' + type] = fun;
	    }
	}

每次调用addEvent函数的时候，它都要对浏览器所支持的能力进行检查，首先检查是否支持addEvementListener方法，如果不支持，再检查是否支持attachEvent方法，如果还不支持，就用dom0级的方法添加事件。这个过程，在addEvent函数的时候每次都要走一遍，其实，如果浏览器支持其中一种方法，那么他就会一直支持了，就没有必要再进行其它分支的检测了。也就是说，if语句不必每次都执行，代码可以运行地更快一点。

解决方案就是惰性加载。所谓的惰性加载，指函数执行的分支只会执行一次，有两种方式实现惰性载入的方式。

1、第一种是在函数被调用时，再处理函数。函数在第一次调用时，该函数会被覆盖为另一个按合适方式执行的函数，这样任何对原函数的调用都不再经过执行的分支了。

	function addEvent(type, element, fun) {
	    if (element.addEventListener) {
	        addEvent = function (type, element, fun) {
	            element.addEventListener(type, fun, false);
	        }
	    }
	    else if(element.attachEvent){
	        addEvent = function (type, element, fun) {
	            element.attachEvent('on' + type, fun);
	        }
	    }
	    else{
	        addEvent = function (type, element, fun) {
	            element['on' + type] = fun;
	        }
	    }
	    return addEvent(type, element, fun);
	}

在这个惰性载入的addEvent的中，if语句的每个分支都会为addEvent变量赋值，覆盖了原函数，最后一步便是调用了新的函数。下次调用addEvent时，便会直接调用新赋值的函数，这样就不用再执行if语句了。

但是这个方法有个缺点，如果函数名称有变化，修改起来比较麻烦。

2、第二种是声明函数时就指定适当的函数。这样在第一次调用函数就不会损失性能了，只在代码加载的时候会损失点性能。

一下就是按照这一思路进行重写的addEvent。以下嗲吗创建一个匿名的自执行函数，通过不同的分支以确定应该使用哪个函数实现。

	var addEvent = (function () {
	    if (document.addEventListener) {
	        return function (type, element, fun) {
	            element.addEventListener(type, fun, false);
	        }
	    }
	    else if (document.attachEvent) {
	        return function (type, element, fun) {
	            element.attachEvent('on' + type, fun);
	        }
	    }
	    else {
	        return function (type, element, fun) {
	            element['on' + type] = fun;
	        }
	    }
	})();

### 1.5.3 函数绑定

在javascript与DOM交互中经常需要使用函数绑定，定义一个函数然后将其绑定到特定的DOM元素或集合的某个事件触发程序上，绑定函数经常和回调函数及事件处理程序一起使用，以便把函数作为变量传递的同时保留代码执行环境。

	<button id="btn">按钮</button>
	<script>            
	    var handler={
	        message:"Event handled.",
	        handlerFun:function(){
	            alert(this.message);
	        }
	    };
	btn.onclick = handler.handlerFun;
	</script>

上面的代码创建了一个叫做handler的对象。handler.handlerFun()方法被分配为一个DOM按钮的事件处理程序。当按下该按钮时，就调用该函数，显示一个警告框。虽然貌似警告框应该显示Event handled，然而实际上显示的是undefined。这个问题在于没有保存handler.handleClick()的环境，所以this对象最后指向了DOM按钮而非handler。

可以使用闭包来修复这个问题。
	
	<button id="btn">按钮</button>
	<script>            
	var handler={
	    message:"Event handled.",
	    handlerFun:function(){
	        alert(this.message);
	    }
	};
	btn.onclick = function(){
	    handler.handlerFun();    
	}
	</script>

当然只是特定于此场景的解决方案，创建多个闭包可能会另代码能以理解和调试。更好的办法是使用函数绑定。

一个简单的绑定函数bing()接收一个函数和一个环境，并返回一个给定环境中调用给定函数的函数，并且将所有参数原封不动的传递过去。

	function bind(fn,context){
	    return function(){
	        return fn.apply(context,arguments);
	    }
	}

这个函数似乎很简单，但其功能是十分强大的。在bind()中创建了一个闭包，闭包使用apply()调用传入的函数，并给apply()传递context对象和参数。当调用返回的函数时，它会在给定的环境中执行被传入的函数并给出所有的参数。

	<button id="btn">按钮</button>
	<script>  
	function bind(fn,context){
	    return function(){
	        return fn.apply(context,arguments);
	    }
	}          
	var handler={
	    message:"Event handled.",
	    handlerFun:function(){
	        alert(this.message);
	    }
	};
	btn.onclick = bind(handler.handlerFun,handler);
	</script>

ECMAscript5为所有的函数定义了一个原生的bind()方法，进一步简化了操作。

只要是将某个函数指针以值的形式进行传递，同时该函数必须在特定的环境下执行，被绑定的函数的作用就凸显出来了。它们的作用于事件处理程序以及setTimeout()和setInterval()。然而，被绑定函数与普通函数相比有更多的开销，它们需要更过内存，同时也因为多重函数调用稍微慢一点，所以最好只在必要时使用。

### 1.5.4 函数柯里化

与函数绑定紧密相关的主体是函数柯里化（function currying），它用于创建已经设置好了的一个或多个参数的函数。函数柯里化的基本方法和函数绑定是一样的。两者的区别在于当函数被调用时，返回的函数还要设置一些传入的参数。

	function add(num1,num2){
	    return num1+num2;
	}
	function curriedAdd(num2){
	    return add(5,num2);
	}
	console.log(add(2,3));//5
	console.log(curriedAdd(3));//8

这个代码定义了两个函数：add()和curriedAdd()。后者本质上是在任何情况下第一个参数为5的add()版本。尽管从技术来说curriedAdd()并非柯里化的函数，但它很好地展示了其**概念**。

**柯里化函数通常由以下步骤动态创建：调用另一个函数并为它传入柯里化的函数和必要的参数。**下面是创建柯里化函数的通用方式。

	function curry(fn){
	    var args = Array.prototype.slice.call(arguments, 1);
	    return function(){
	        var innerArgs = Array.prototype.slice.call(arguments),
	        finalArgs = args.concat(innerArgs);
	        return fn.apply(null, finalArgs);
	    };
	}

curry()函数的主要工作就是将被返回函数的参数进行排序。curry()的第一个参数是要进行柯里化的函数，其它参数是要传入的值。为了获取第一参数之后的所有参数，在arguments对象上调用了slice()方法，并传入参数1表示获取被返回数组包含从第二个参数开始的所有参数；然后args数字包含了来自外部函数的参数。在内部函数中，创建了innerArgs数组来存放所有传入的参数（又一次用到了slice()）。有了存放来自外部函数和内部函数的数组后，就可以使用concat()犯法将它们组合为finalArgs，然后用apply()将结果传递给函数。注意这个函数并没有考虑的到执行环境，所以调用apply()时的第一个参数是null。curry()函数还可以按照以下方式应用。

	function add(num1, num2){
	    return num1 + num2;
	}
	var curriedAdd = curry(add, 5);
	alert(curriedAdd(3)); //8

在这个例子中，创建了第一个参数绑定为5的add()柯里化版本。当调用curriedAdd()并传入3时，3会成为add()的第二个参数，同时第一个参数依然是5，最后的结果便是8.也可以像下例这样的给出所有的函数参数。

	function add(num1, num2){
	    return num1 + num2;
	}
	var curriedAdd2 = curry(add, 5, 12);
	alert(curriedAdd2()); //17

在这里，柯里化的add()函数的两个参数都提供了，所以以后就无需再传递给它们了。

函数柯里化还常常作为函数绑定的一部分包含在其中，过早处更为复杂的bind()函数。

	function bind(fn, context){
	    var args = Array.prototype.slice.call(arguments, 2);
	    return function(){
	        var innerArgs = Array.prototype.slice.call(arguments),
	        finalArgs = args.concat(innerArgs);
	        return fn.apply(context, finalArgs);
	    };
	}

对curry()函数的主要更改在于传入参数的个数，以及它如何影响代码的结果。curry()仅仅接受一个要包裹的函数作为参数，而bind()同时接收函数和一个object对象。这表示给被绑定的函数的参数是从第三个开始而不是第二个，这就要更改slice()的第一处调用。另一处更改是在倒数第三行将object对象传递给apply()。当使用bind()时，它会返回绑定到给定环境的函数，并且可能它其中某些函数参数已经被设好。想要除了event对象再额外给事件处理程序传递参数时，这非常有用。

	var handler = {
	    message: "Event handled",
	    handleClick: function(name, event){
	        alert(this.message + ":" + name + ":" + event.type);
	    }
	};
	var btn = document.getElementById("my-btn");
	EventUtil.addHandler(btn, "click", bind(handler.handleClick, handler, "my-btn"));

handler.handleClick()方法接收两个参数：要处理的元素的名字和event对象。作为第三个参数传递给bind()函数的名字，又被传递给了handler.handleClick()，而handler.handleclick()也会同时接收到event对象。

	ECMScript5的bind()方法也可以实现函数柯里化，只要在this的值之后再差传入另一个参数即可。
	
	var handler = {
	    message: "Event handled",
	    handleClick: function(name, event){
	        alert(this.message + ":" + name + ":" + event.type);
	    }
	};
	var btn = document.getElementById("my-btn");
	EventUtil.addHandler(btn, "click", handler.handleClick.bind(handler, "my-btn"));

javascript里的柯里化函数和绑定函数提供了强大的动态函数创建功能。使用bind()还是curry()要依据是否粗腰object对象影响来决定。他们都能用于创建复杂的算法和功能，当然两者不应滥用，因为每个函数都会带来额外的开销。

### 1.5.6 函数重写

由于一个函数可以返回另一个函数，因此可以用新的函数来覆盖旧的函数。

	function a(){
	    console.log('a');
	    a = function(){
	        console.log('b');
	    }
	}

这样一来，当我们第一次调用该函数时会console.log('a')，会被执行；全局变量a被重定义，并赋予新的函数。

当该函数再次被调用时，console.log('b')会被执行，再复杂的情况如下所示。

	var a = (function(){
	    function someSetup(){
	        var setup = 'done';
	    }
	    function actualWork(){
	        console.log('work');
	    }
	    someSetup();
	    return actualWork;
	})()

我们使用了私有函数someSetup()和actualWork()，当函数a()第一次被调用时，它会调用someSetup()，并返回函数actualWork()的引用。

这项技术对于某些浏览器相关操作相当有用，因为在不同的浏览器中，实现相同任务的方法可能是不同的。浏览器的特性不可能因为函数调用而发生任何改变，因此最好的选择是让函数根据其当所在的浏览器来重新定义自己。

## 1.6 函数式编程

和Lisp、Haskell不同，javascript并非函数式编程语言，但在javascript中可以操作对象一样操作函数，也就是说可以在javascript中应用函数式编程技术。ES5中的数组方法（map()和reduce()）就可以非常适用于函数式编程风格。本文将详细介绍函数式编程。

### 1.6.1 函数处理数组

假设有一个数组，数组元素都是数字，想要计算这些元素的平均值和标准差。若使用非函数式编程风格的话，如下所示。

	var sum = function(x,y){
	    return x+y;
	}
	var square = function(x){
	    return x*x;
	}
	var data = [1,1,3,5,5];
	var mean = data.reduce(sum)/data.length;
	var deviations = data.map(function(x){
	    return x - mean;
	});
	var stddev = Math.sqrt(deviations.map(square).reduce(sum)/(data.length-1));

可以使用数组方法map()和reduce()实现同样的计算。这样实现及其简洁。

	var sum = function(x,y){
	    return x+y;
	}
	var square = function(x){
	    return x*x;
	}
	var data = [1,1,3,5,5];
	var mean = data.reduce(sum)/data.length;
	var deviations = data.map(function(x){
	    return x - mean;
	});
	var stddev = Math.sqrt(deviations.map(square).reduce(sum)/(data.length-1));

在ES3中，并不包含这些数组方法，需要自定义map()和reduce()。

	//对于每个数组元素调用函数f()，并返回一个结果数组
	//如果Array.prototype.map定义了的话，就使用这个方法
	var map = Array.prototype.map ? function(a,f){return a.map(f);}
	          : function (a,f){
	                var results = [];
	                for(var i = 0,len=a.length; i < len; i++){
	                    if(i in a){
	                        results[i] = f.call(null,a[i],i,a);
	                    }
	                }
	                return results;
	            }

---

	//使用函数f()和可选的初始值将数组a减到一个值
	//如果Array.prototype.reduce存在的话，就使用这个方法
	var reduce = Array.prototype.reduce 
	            ? function(a,f,initial){
	                if(arguments.length > 2){
	                    return a.reduce(f,initial);                    
	                }else{
	                    return a.reduce(f);
	                }
	            }
	            : function(a,f,initial){
	                var i = 0, len = a.length ,accumulator;
	                if(argument.length > 2){
	                    accumulator = initial;
	                }else{
	                    if(len == 0){
	                        throw TypeError();
	                    }
	                    while(i < len){
	                        if(i in a){
	                            accumulator = a[i++];
	                            break;
	                        }else{
	                            i++;
	                        }
	                    }
	                    if(i == len){
	                        throw TypeError();
	                    }
	                }
	                while(i < len){
	                    if(i in a){
	                        accumulator = f.call(undefined,accumulator,a[i],i,a);
	                    }
	                    i++;
	                }
	                return accumulator;
	            }

### 1.6.2 高阶函数

高阶函数(higher-order function)指操作函数的函数，它接收一个或多个函数作为参数，并返回一个新函数。

	//这个高阶函数返回一个新的函数，这个新函数将它的实参传入f()，并返回f的返回值的逻辑非
	function not(f){
	    return function(){
	        var result = f.apply(this,arguments);
	        return !result;
	    };
	}
	var even = function(x){
	    return x % 2 === 0;
	}
	var odd = not(even);
	[1,1,3,5,5].every(odd);//true

上面的not()函数就是一个高阶函数，因为它接收一个函数作为参数，并返回一个新函数。

下面的mapper()函数，也是接收一个函数作为参数，并返回一个新函数，这个新函数将一个数组映射到另一个使用这个函数的数组上。

	function mapper(f){
	    return function(a){
	        return a.map(f);
	    }
	}
	var increment = function(x){
	    return x+1;
	}
	var incrementer = mapper(increment);
	incrementer([1,2,3]);//[2,3,4]

下面一个更常见的例子，它接收两个函数f()和g()，并返回一个新函数用以计算f(g());

	//返回一个新的可以计算f(g(...))的函数
	//返回的函数h()将它所有的实参传入g()，然后将g()的返回值传入f()
	//调用f()和g()时的this值和调用h()时的this值是同一个this
	function compose(f,g){
	    return function(){
	        //需要给f()传入一个参数，所以使用f()的call()方法
	        //需要给g()传入很多参数，所以使用g()的apply()方法
	        return f.call(this,g.apply(this,arguments));
	    };
	}
	var square = function(x){
	    return x*x;
	}
	var sum = function(x,y){
	    return x + y;
	}
	var squareofsum = compose(square,sum);
	squareofsum(2,3);//25

### 1.6.3 不完全函数

不完全函数是一种函数变换技巧，即把一次完整的函数调用拆分多次函数调用，每次传入的实参都是完整实参一部分，每个拆分的函数叫做不完全函数，每次函数调用叫做不完全调用。这种函数变换的特点是每次调用都返回一个函数，知道调用到最终的运行结果。

	//实现一个工具函数将类数组对象(或对象)转换为真正的数组
	function array(a,n){
	    return Array.prototype.slice.call(a,n||0);
	}
	//这个函数的实参传递到左侧
	function partialLeft(f){
	    var args = arguments;
	    return function(){
	        var a = array(args,1);
	        a = a.concat(array(arguments));
	        return f.apply(this,a);
	    };
	}
	//这个函数的实参传递到右侧
	function partialRight(f){
	    var args = arguments;
	    return function(){
	        var a = array(arguments);
	        a = a.concat(array(args,1));
	        return f.apply(this,a);
	    };
	}
	//这个函数的实参被用作模板，实参列表中的undefined值都被填充
	function partial(f){
	    var args = arguments;
	    return function(){
	        var a = array(args,1);
	        var i = 0, j = 0;
	        //遍历args，从内部实参填充undefined值
	        for(;i<a.length;i++){
	            if(a[i] === undefined){
	                a[i] = arguments[j++];
	            }
	            //现在将剩下的内部实参都追加进去
	        };
	        a = a.concat(array(arguments,j));
	        return f.apply(this,a);
	    }
	}
	//这个函数有三个实参
	var f = function(x,y,z){
	    return x*(y - z);
	}
	//注意这三个不完全调用之间的区别
	partialLeft(f,2)(3,4);//2*(3-4)=-2
	partialRight(f,2)(3,4);//3*(4-2)=6
	partial(f,undefined,2)(3,4);//3*(2-4)=-6

利用这种不完全函数的编程技巧，可以编写一些有意思的代码，利用已有的函数来定义新的函数。

	var increment = partialLeft(sum,1);
	var cuberoot = partialRight(Math.pow,1/3);
	String.prototype.first = partial(String.prototype.charAt,0);
	String.prototype.last = partial(String.prototype.substr,-1,1);


可以使用不完全调用的组合来重新组织求平均数和标准差的代码，这种编程风格是非常纯粹的函数式编程。

	var data = [1,1,3,5,5];
	var sum = function(x,y){return x+y;}
	var product = function(x,y){return x*y;}
	var neg = partial(product,-1);
	var square = partial(Math.pow,undefined,2);
	var sqrt = partial(Math.pow,undefined,.5);
	var reciprocal = partial(Math.pow,undefined,-1);
	var mean = product(reduce(data,sum),reciprocal(data.length));
	var stddev = sqrt(product(reduce(map(data,compose(square,partial(sum,neg(mean)))),sum),reciprocal(sum(data.length,-1))));

### 1.6.4 记忆

将上次的计算结果缓存起来，在函数式编程中，这种缓存技巧叫做记忆（memorization）。记忆只是一种编程技巧，本质是牺牲算法的空间复杂度来换取更优的时间复杂度，在客户端javascript中代码的执行时间复杂度往往称为瓶颈。因为在大多数场景下，这种空间换取时间的做法以提升程序执行效率的做法是非常可取的。

	//返回f()的带有记忆功能的版本
	//只有当f()的实参的字符串表示都不相同时它才会工作
	function memorize(f){
	    var cache = {};//将值保存到闭包内
	    return function(){
	        //将实参转换为字符串形式，并将其用做缓存的键
	        var key = arguments.length + Array.prototype.join.call(arguments ,",");
	        if(key in cache){
	            return cache[key];
	        }else{
	            return cache[key] = f.apply(this,arguments);
	        }
	    }
	}

memorize()函数常见一个新的对象，这个对象被当作缓存的宿主，并赋值给一个局部变量，因此对于返回函数来说它是私有的。所返回的函数将它的实参数组转换成字符串，并将字符串用作缓存对象的属性名。如果在缓存中存在这个值，就直接返回它，否则就调用既定的函数对实参进行计算，并将计算结果缓存起来并返回。

	//返回两个整数的最大公约数
	function gcd(a,b){
	    var t;
	    if(a < b){
	        t = b, b = a, a = t; 
	    }
	    while(b != 0){
	        t = b, b = a % b, a = t;
	    }
	    return a;
	}
	var gcdmemo = memorize(gcd);
	gcdmemo(85,187);//17

写一个递归函数时，往往需要实现记忆功能，我们更希望调用实现了记忆功能和递归函数，而不是原递归函数。

	var factorial = memorize(function(n){
	    return (n<=1) ? 1 : n*factorial(n-1);
	});
	factorial(5);//120

### 1.6.5 连续调用单调函数

下面利用连续调用单调函数来实现一个简易的加法算法。

	add(num1)(num2)(num3)…; 
	add(10)(10) = 20
	add(10)(20)(50) = 80
	add(10)(20)(50)(100) = 180

如果完全按照上面来实现，则无法实现，因为add(1)(2)如果返回3，那么add(1)(2)(3)必然报错。于是，有以下下面两种变形方法。

第一种变形如下：

	add(num1)(num2)(num3)…; 
	add(10)(10)() = 20
	add(10)(20)(50)() = 80
	add(10)(20)(50)(100)() = 180

---

	function add(n){
	    return function f(m){  
	        if(m === undefined){
	            return n;
	        }else{
	            n += m;
	            return f;
	        }
	    }
	}
	console.log(add(10)());//10
	console.log(add(10)(10)());//20
	console.log(add(10)(10)(10)());//30

第二种变形如下：

	add(num1)(num2)(num3)…; 
	+add(10)(10) = 20
	+add(10)(20)(50) = 80
	+add(10)(20)(50)(100) = 180

---

	function add(n) {
	    function f(m){
	        n += m;
	        return f;
	    };
	    f.toString = f.valueOf = function () {return n}
	    return f;
	}
	console.log(+add(10));//10
	console.log(+add(10)(10));//20
	console.log(+add(10)(10)(10));//30

# 2、对象

## 2.1 初识对象

### 2.1.1 对象定义

javascript的基本数据类型包括undefined、null、string、boolean、number、object。对象和其它数据类型不同的是，对象是一种复合值：它将许多值（原始值或其他对象）聚合在一起，可通过名字访问这些值。

于是，对象可以看作是属性的无序属性，每个属性都是一个名值对。属性名字符串，因此我们可以把对象看作是字符串到值的映射。

### 2.1.2 对象创建

有以下3中方式来创建对象，包括new构造对象、对象直接量和Object.create()函数。

1、new构造函数

使用new操作符后跟着Object构造函数用以初始化一个新创建。

	var person = new Object();
	//如果不给构造函数传递参数可以不加括号 var person = new Object;
	person.name = 'bai';
	person.age = 29;

---

	//创建无属性的空对象
	var cody1 = new Object();
	var cody2 = new Object(undefined);
	var cody3 = new Object(null);
	console.log(typeof cody1,typeof cody2, typeof cody3);//object object object

如果该参数是一个对象，则直接返回这个对象。

	var o1 = {a: 1};
	var o2 = new Object(o1);
	console.log(o1 === o2);// true
	
	var f1 = function(){};
	var f2 = new Object(f1);
	console.log(f1 === f2);// true

如果是一个原始类型的值，则返回该值对应的包装对象。

	//String {0: "f", 1: "o", 2: "o", length: 3, [[PrimitiveValue]]: "foo"}
	console.log(new Object('foo'));
	
	//Number {[[PrimitiveValue]]: 1}
	console.log(new Object(1));
	
	//Boolean {[[PrimitiveValue]]: true}
	console.log(new Object(true));

若Object()函数不通过new而直接使用，则相当于转换方法，可以将任意值转换为对象。

	var uObj = Object(undefined);
	var nObj = Object(null);
	console.log(Object.keys(uObj));//[]
	console.log(Object.keys(nObj));//[]

如果Object()的参数是一个对象，则直接返回原对象。

	var o = {a:1};
	var oObj = Object(o);
	console.log(Object.keys(oObj));//['a']

利用这一点，可以写一个判断变量是否是对象的函数。

	function isObject(value) {
	  return value === Object(value);
	}
	isObject([]) // true
	isObject(true) // false

2、对象字面量

javascript提供了叫做字面量的快捷方式，用于创建大多数原生对象值。使用字面量只是隐藏了与使用new操作符相同的基本过程，于是也可以叫做语法糖。

	//等价于var person = new Object();
	var person = {}; 

---

	var person = {
	    name : 'bai',
	    age : 29,
	    5 : true
	};

使用对象字面量的方法来定义对象，属性名会自动转成字符串。

	//同上
	var person = {
	    'name' : 'bai',
	    'age' : 29,
	    '5' : true
	}; 

一般地，对象字面量的最后一个属性后的逗号将忽略，但在IE7-浏览器中导致错误。

	//IE7-浏览器中报错 SCRIPT1028: 缺少标识符、字符串或数字
	var person = {
	    name : 'bai',
	    age : 29,
	    5 : true,
	};

3、Object.create()

**ES5定义了一个名为Object.create()，它会创建一个新对象，第一个参数就是这个对象的原型，第二个可选参数用以对对象的属性作进一步的描述。**

	var o1 = Object.create({x:1,y:1}); //o1继承了属性x和y
	console.log(o1.x);//1

可以通过传入参数null来创建一个没有原型的对象，但通过这个方式创建的对象不会继承任何东西，甚至不包括基础方法。比如toString()和valueOf()。

	var o2 = Object.create(null); // o2不继承任何属性和方法
	var o1 = {};
	console.log(Number(o1));//NaN
	console.log(Number(o2));//Uncaught TypeError: Cannot convert object to primitive value

如果想创建一个普通的空对象（比如通过{}或new Object()创建的对象），需要传入Object.prototype。

	var o3 = Object.create(Object.prototype); // o3和{}和new Object()一样
	var o1 = {};
	console.log(Number(o1));//NaN
	console.log(Number(o3));//NaN

Object.create()方法的第二个参数是属性描述符。

	var o1 = Object.create({z:3},{
	  x:{value:1,writable: false,emumerable:true,configurable:true},
	  y:{value:2,writable: false,emumerable:true,configurable:true}
	}); 
	console.log(o1.x,o1.y,o1.z);//1 2 3

### 2.1.3 对象组成

对象是属性的无序集合，由键名和属性值组成。

**键名：**

对象的所有简明都是字符串，所有加不加引号都可以，如果不是字符串也会自动转为字符串。
	
	var o = {
	  'p': 'Hello World'
	};
	var o = {
	  p: 'Hello World'
	};

---

	var o ={
	  1: 'a',
	  3.2: 'b',
	  1e2: true,
	  1e-2: true,
	  .234: true,
	  0xFF: true,
	};
	//Object {1: "a", 100: true, 255: true, 3.2: "b", 0.01: true, 0.234: true}
	o;

如果简明不符合标识符命名规则，则必须加上引号，否则会报错。

	//Uncaught SyntaxError: Unexpected identifier
	var o = {
	    1p: 123
	}
	
	var o = {
	    '1p': 123
	}

**属性值：**

属性值可以是任意类型的表达式，最终表达式的结果就是属性值的结果。
	
	var o ={
	    a: 1+2
	}
	console.log(o.a);//3

如果属性值为函数，则通常把这个属性称为方法。

	var o = {
	  p: function (x) {
	    return 2 * x;
	  }
	};
	o.p(1);//2

由于对象的方法就是函数，因此也有name属性。方法的name属性是紧跟在function关键字后面的函数名。如果是匿名函数，ES5环境会返回undefined，ES6会返回方法名。
	
	var obj = {
	  m1: function f() {},
	  m2: function () {}
	};
	obj.m1.name // "f"
	obj.m2.name //ES5： undefined
	obj.m2.name //ES6： "m2"

### 2.1.4 引用对象

如果不同的变量名指向用一个对象，那么它们都是这个对象的引用，也就是指向同一个内存地址，修改其中一个变量，会影响到其它所有的变量。

	var o1 = {};
	var o2 = o1;
	
	o1.a = 1;
	console.log(o2.a);// 1
	o2.b = 2;
	console.log(o1.b);// 2

如果取消一个变量对于原对象的引用，不会影响到另一个变量。

	var o1 = {};
	var o2 = o1;
	
	o1 = 1;
	console.log(o2);//{}

### 2.1.5 实例方法

valueOf()

valueOf()方法返回当前对象。

	var o = new Object();
	o.valueOf() === o // true

toString()

toString()方法返回当前对象对应的字符串形式（类属性）。

	var o1 = new Object();
	o1.toString() // "[object Object]"
	
	var o2 = {a:1};
	o2.toString() // "[object Object]"

一般地，使用Object.prototype.toString()方法来获取对象的类属性，进行类型识别。

toLocalString()

toLocalString()方法并不做任何本地化的自身的操作，它仅调用toString()方法并返回对应值。

Date和Number类对toLocalString()方法做了本地化定制。

	var o = {a:1};
	o.toLocaleString() // "[object Object]"

## 2.2 属性操作

对对象来说，属性操作也是离不开的话题。类似于增删改查的操作，属性分为属性查询、属性设置、属性删除和属性继承。下面将逐个讨论。

### 2.2.1 属性查询

属性查询一般有两种方法，包括点运算符和方括号运算符。

	var o = {
	  p: 'Hello World'
	};
	o.p // "Hello World"
	o['p'] // "Hello World"

变量中可以存在中文，因为中文相当于字符，对英文字符同样对待，因此可以写成Person.白或person['白']。

	var person = {
	    白 : 1
	}
	person.白;//1
	person['白'];//1

1、**点运算符：**

点运算符是很多面向对象语句的通用写法，由于其比较简单，所以较方括号运算符相比，更常用。

由于javascript是弱类型语言，在任何对象中都可以创建任意数量的属性。但通过点运算符(.)访问对象的属性时，属性名用一个标识符来表示，标识符要符合变量命名规则。标识符必须直接出现在javascript程序中，它们不是数据类型，因此程序无法修改它们。

	var o = {
	    a:1,
	    1:2
	};
	console.log(o.a);//1
	//由于变量不可以以数字开头，所以o.1报错
	console.log(o.1);//Uncaught SyntaxError: missing ) after argument list

2、**方括号运算符：**

当方括号运算符（[]）来访问对象的属性时，属性名通过字符串来表示。字符串是javascript的数据类型，在程序中可以修改和创建他们。

使用方括号有两个优点：

a、可以通过变量来访问属性

	var a = 1;
	var o = {
	    1: 10
	}
	o[a];//10

	o.1; // Uncaught SyntaxError: Unexpected number

b、属性名称可以为javascript无效标识符。

	var myObject = {
	    123:'zero',
	    class:'foo'
	};
	console.log(myObject['123'],myObject['class']);//'zero' 'foo'
	console.log(myObject.123);//报错

**方括号中的值若是非字符串类型会使用String()隐式转换成字符串再输出；如果是字符串类型，若有引号则原值输出，否则会被视为变量，若变量没有定义，则报错。**

	var person = {};
	person[0];  //[]中的数字不会报错，而是自动转换成字符串
	person[a];  //[]中符合变量命名规则的元素会被当成变量，变量未被定义，而报错
	person['']; //[]中的空字符串不会报错，是实际存在的且可以调用，但不会在控制台右侧的集合中显示
	
	person[undefined];//不会报错，而是自动转换成字符串
	person[null];//不会报错，而是自动转换成字符串
	person[true];//不会报错，而是自动转换成字符串
	person[false];//不会报错，而是自动转换成字符串

3、**可计算属性名**

在方括号内部可以使用表达式。

	var a = 1;
	var person = {
	    3: 'abc'
	};
	person[a + 2];//'abc'

但如果在对象字面量内部属性名使用表达式。则需要使用ES6的可计算属性名。

	var a = 1;
	//Uncaught SyntaxError: Unexpected token +
	var person = {
	    a + 3: 'abc'
	};

ES6增加了可计算属性名，可以在文字中使用[]包裹一个表达式来当作属性名。

	var a = 1;
	var person = {
	    [a + 3]: 'abc'
	};
	person[4];//'abc'

4、**属性查询错误**

a、查询一个不存在的属性不会报错，二十返回undefined。

	var person = {};
	console.log(person.a);//undefined

b、如果对象不存在，视图查询这个不存在的对象的属性会报错。

	console.log(person.a);//Uncaught ReferenceError: person is not defined

可以利用这一点，来**检查一个全局变量是否被声明。**

	// 检查a变量是否被声明
	if (a) {...} // 报错

---

	//所有全局变量都是window对象的属性。window.a的含义就是读取window对象的a属性，如果该属性不存在，就返回undefined，并不会报错
	if (window.a) {...} // 不报错

### 2.2.2 属性设置

属性设置又称为属性赋值，与属性查询相同，具有点运算符和方括号运算符这两种方法。

	o.p = 'abc';
	o['p'] = 'abc';

在给对象设置属性之前，一般要先检测对象是否存在。

	var len = undefined;
	if(book){
	    if(book.subtitle){
	        len = book.subtitle.length;
	    }
	}

上面的代码可以简化为：

	var len = book && book.subtitle && book.subtitle.length;

关于逻辑与&&运算符以后补充，博文见此。

[逻辑与&&运算符](http://www.cnblogs.com/xiaohuochai/p/5616992.html#anchor2)。

null和undefined不是对象，给它们设置属性会报错。

	null.a = 1;//Uncaught TypeError: Cannot set property 'a' of null
	undefined.a = 1;//Uncaught TypeError: Cannot set property 'a' of undefined

由于string、number、boolean有对应的包装对象，所以给它们设置属性不会报错。

	'abc'.a = 1;//1
	(1).a = 1;//1
	true.a = 1;//1

### 2.2.3 属性删除

使用delete运算符可以删除对象属性（包括数组元素）。

	var o = {
	    a : 1
	};
	console.log(o.a);//1
	console.log('a' in o);//true
	console.log(delete o.a);//true
	console.log(o.a);//undefined
	console.log('a' in o);//false

**给对象属性设置为null或undefined，并没有删除该属性。**

	var o = {
	    a : 1
	};
	o.a = undefined;
	console.log(o.a);//undefined
	console.log('a' in o);//true
	console.log(delete o.a);//true
	console.log(o.a);//undefined
	console.log('a' in o);//false、

用delete运算符删除数组元素时，不会改变数组长度。

	var a = [1,2,3];
	delete a[2];
	2 in a;//false
	a.length;//3
	console.log(a); //  [1, 2, undefined × 1]

delete运算符只能删除自有属性，不能删除继承属性（要删除继承属性必须从定义这个属性的原型对象上删除它，并且这会影响到所有继承自这个原型的对象。）

	var o  = {
	    a:1
	}
	var obj = Object.create(o);
	obj.a = 2;
	
	console.log(obj.a);//2
	console.log(delete obj.a);//true
	console.log(obj.a);//1
	console.log(delete obj.a);//true
	console.log(obj.a);//1

**返回值：**

delete操作符的返回值是个布尔值true或false。

1、当使用delete操作符删除对象属性或数组元素成功时，返回true。

	var o = {a:1};
	var arr = [1];
	console.log(delete o.a);//true
	console.log(delete arr[0]);//true

2、**当使用delete操作符删除不存在的属性或左值时，返回true。**

	var o = {};
	console.log(delete o.a);//true
	console.log(delete 1);//true
	console.log(delete {});//true

3、当delete操作符删除变量时，返回false，严格模式下会抛出ReferenceError错误。

	var a = 1;
	console.log(delete a);//false
	console.log(a);//1
	
	'use strict';
	var a = 1;
	//Uncaught SyntaxError: Delete of an unqualified identifier in strict mode
	console.log(delete a);

4、当使用delete操作符删除不可配置的属性时，返回false，严格模式下会抛出TypeError错误。

	var obj = {};
	Object.defineProperty(obj,'a',{configurable:false});
	console.log(delete obj.a);//false
	
	'use strict';
	var obj = {};
	Object.defineProperty(obj,'a',{configurable:false});
	//Uncaught TypeError: Cannot delete property 'a' of #<Object>
	console.log(delete obj.a);

### 2.2.4 属性继承

每一个javascript对象都和另一个对象相关联。“另一个对象”就是我们所熟知的原型，每一个对象都从原型链继承属性。所有通过对象直接量创建的对象都具有同一个原型对象，并可以通过Object.prototype获得对原型对象的引用。

	var obj = {};
	console.log(obj.__proto__ === Object.prototype);//true

Object.prototype的原型对象是null，所以它不继承任何属性。

	console.log(Object.prototype.__proto__ === null);//true

对象本身具有的属性叫做自有属性（own property），从原型对象继承而来的属性叫继承属性。

	var o = {a:1};
	var obj = Object.create(o);
	obj.b = 2;
	//继承自原型对象o的属性a
	console.log(obj.a);//1
	//自有属性b
	console.log(obj.b);//2

**in**

in操作符可以判断属性在不在该对象上，但无法区别是自有还是继承属性。

	var o = {a:1};
	var obj = Object.create(o);
	obj.b = 2;
	console.log('a' in obj);//true
	console.log('b' in obj);//true
	console.log('b' in o);//false

**for-in**

通过for-in可以遍历该对象中所有可枚举属性。

	var o = {a:1};
	var obj = Object.create(o);
	obj.b = 2;
	for(var i in obj){
	    console.log(obj[i]);//2 1
	}

**hasOwnproperty()**

通过hasOwnproperty()方法可以确定该属性是自有属性还是继承属性。

	var o = {a:1};
	var obj = Object.create(o);
	obj.b = 2;
	console.log(obj.hasOwnProperty('a'));//false
	console.log(obj.hasOwnProperty('b'));//true

**Object.keys()**

Object.keys()方法返回所有可枚举的自有属性。
	
	var o = {a:1};
	var obj = Object.create(o,{
	    c:{value:3,configurable: false}
	});
	obj.b = 2;
	console.log(Object.keys(obj));//['b']

**Object.getOwnPropertyNames()**

与Object.keys()方法不同，Object.getOwnPropertyNames()方法返回所有自有属性(包括不可枚举的属性)。

	var o = {a:1};
	var obj = Object.create(o,{
	    c:{value:3,configurable: false}
	});
	obj.b = 2;
	console.log(Object.getOwnPropertyNames(obj));//['c','b']

## 2.3 神秘的属性描述符

对操作系统中的文件，我们可以驾轻就熟将其设置为只读、隐藏、系统文件和普通文件。**对对象来说，属性操作符提供类似的功能，用来描述对象的值、是否可以配置、是否可以修改以及是否可以枚举。**

### 2.3.1 描述符类型

对象属性描述符的类型分为两种：数据属性和访问器属性。

**1、数据属性**

数据属性是（data property）包含一个数据值的位置，在这个位置可以读取和写入值。数据属性有4个特性。

a、Configurable（可配置性）

可配置性决定是否可以使用delete的删除属性，以及是否可以修改这些属性描述符的特性，默认为true。

b、Enumerable（可枚举性）

可枚举性决定属性是否出现在对象的枚举中，比如是否可以通过for-in循环返回改属性，默认值为true。

c、Writable（可写性）

可写性决定是否可以修改属性的值，默认值为true。

d、Value属性（属性值）

属性值包含这个属性的数据值，读取这个属性的时候，从这个位置读；写入属性的时候，将新值保存在这个位置。

**2、访问器属性**

对象属性是名字、值和一组属性描述符构成的。而属性值可以用一个或两个方法替代，这两个方法就是getter和setter。而这种属性类型叫做访问器属性（accessor property）。

a、Configurable（可配置性）

可配置性决定是否可以使用delete删除属性，以及是否可以修改属性描述符的特性，默认为true。

b、Enumerable（可枚举性）

可枚举性决定属性是否出现在对象的属性枚举中，比如是否可以通过for-in循环来返回该属性，默认值为true。

c、getter

在读取属性时调用的函数，默认值为undefined。

d、setter

在写入属性时调用的函数，默认值为undefined。

和数据属性不同，访问器属性不具有可写性（writable）。如果属性同时具有getter和setter方法，那么它是一个读/写属性。如果只有getter方法，那么它是一个只读属性。如果它只有setter方法，那么它是一个只读属性。读写只读属性总是返回undefined。

### 2.3.2 描述符方法

前面结合扫了属性描述符，要向设置它们，就需要用到描述符方法，描述符方法总共有以下4个：

**1、Object.getOwnPropertyDescriptor()**

Onject.getOwnpropertyDescriptor(o, name)方法用于描述查询一个属性的描述符，并以对象的形式返回。

查询obj.a属性时，可配置性、可枚举性、可写性都是默认的true，而value是a的属性值1.

查询obj.b属性时，因为obj.b属性不存在，该方法返回undefined。

	var obj = {a:1};
	//Object {value: 1, writable: true, enumerable: true, configurable: true}
	console.log(Object.getOwnPropertyDescriptor(obj,'a'));
	//undefined
	console.log(Object.getOwnPropertyDescriptor(obj,'b'));

**2、Object.defineProperty()**

Object.defineProperty(o, name, desc)方法用于创建或配置对象的一个属性的描述符，返回配置后的对象。

**使用该方法创建或配置属性的描述符时，如果不是针对该属性进行描述符的配置，则该项描述符默认为false。**

	var obj = {};
	//{a:1}
	console.log(Object.defineProperty(obj,'a',{
	        value:1,
	        writable: true
	    }));
	
	//由于没有配置enumerable和configurable，所以它们的值为false
	//{value: 1, writable: true, enumerable: false, configurable: false}
	console.log(Object.getOwnPropertyDescriptor(obj,'a'));

**3、Object.defineProperties()**

Object.defineProperties(o, descripties)方法用于创建或配置对象的多个属性的描述符，返回配置后的对象。

	var obj = {
	    a:1
	};
	//{a: 1, b: 2}
	console.log(Object.defineProperties(obj,{
	        a:{writable:false},
	        b:{value:2}
	    }));
	
	//{value: 1, writable: false, enumerable: true, configurable: true}
	console.log(Object.getOwnPropertyDescriptor(obj,'a'));
	//{value: 2, writable: false, enumerable: false, configurable: false}
	console.log(Object.getOwnPropertyDescriptor(obj,'b'));

**4、Object.create()**

Object.create(proto, descriptors)方法使用指定的原型和属性来创建一个对象。

	var o = Object.create(Object.prototype,{
	    a:{writable: false,value:1,enumerable:true}
	});
	//{value: 1, writable: false, enumerable: true, configurable: true}
	console.log(Object.getOwnPropertyDescriptor(obj,'a'));


### 2.3.3 描述符描述

前面分别介绍了数据属性和访问器属性的描述符，但没有详细说明其中的含义及其使用，接下来进一步进行说明。

**1、可写性（writable）**

可写性决定是否可以修改属性的值，默认值为true。

	var o = {a:1};
	o.a = 2;
	console.log(o.a);//2

将writable设置为false后，赋值语句会静默失效。

	var o = {a:1};
	Object.defineProperty(o,'a',{
	    writable:false
	});
	console.log(o.a);//1
	//由于设置了writable为false，所以o.a=2这个语句会静默失效
	o.a = 2;
	console.log(o.a);//1
	Object.defineProperty(o,'a',{
	    writable:true
	});
	//由于writable设置为true，所以o.a可以被修改为2
	o.a = 2;
	console.log(o.a);//2

在严格模式下通过赋值语句为writable为false的属性赋值，会提示类型错误TypeError。
	
	'use strict';
	var o = {a:1};
	Object.defineProperty(o,'a',{
	    writable:false
	});
	//Uncaught TypeError: Cannot assign to read only property 'a' of object '#<Object>'
	o.a = 2;

设置writable:false后，通过Object.defineProperty()方法改变value的值不会受影响，因为这也是以为着在重置writable的属性值为false。

	var o = {a:1};
	Object.defineProperty(o,'a',{
	    writable:false
	});
	console.log(o.a);//1
	Object.defineProperty(o,'a',{
	    value:2
	});
	console.log(o.a);//2

**2、可配置性（Configurable）**

可配置性决定是否可以使用delete的删除属性及是否可以修改属性描述符的特性，默认值为true。

a、设置为Configurable: false后，无法使用delete删除属性。

	var o = {a:1};
	Object.defineProperty(o,'a',{
	    configurable:false
	});
	delete o.a;//false
	console.log(o.a);//1

在严格模式下删除为configurable为false的属性，会提示类型错误TypeError。

	'use strict';
	var o = {a:1};
	Object.defineProperty(o,'a',{
	    configurable:false
	});
	//Uncaught TypeError: Cannot delete property 'a' of #<Object>
	delete o.a;

**使用var命令声明变量时，变量的configurable为false。即delete删除操作无法进行。**

	var a = 1;
	//{value: 1, writable: true, enumerable: true, configurable: false}
	Object.getOwnPropertyDescriptor(this,'a');

b、**一般地，设置Confugurable: false后，将无法使用defineProperty()方法来修改属性描述符。**

	var o = {a:1};
	Object.defineProperty(o,'a',{
	    configurable:false
	});
	//Uncaught TypeError: Cannot redefine property: a
	Object.defineProperty(o,'a',{
	    configurable:true
	});

有一个例外，设置Configurable: false,只允许writable的状态从true变为false。

	var o = {a:1};
	Object.defineProperty(o,'a',{
	    configurable:false,
	    writable:true
	});
	o.a = 2;
	console.log(o.a);//2
	Object.defineProperty(o,'a',{
	    writable:false
	});
	//由于writable:false生效，对象a的o属性无法修改值，所以o.a=3的赋值语句静默失败
	o.a = 3;
	console.log(o.a);//2

这里如何将Configurable属性为false设置为true？

**3、可枚举性（Enumerable）**
可枚举性决定属性是否出现在对象的属性枚举中，具体来说，for-in循环、Object.keys方法、JSON.stringify方法是否会取到该属性。

用户定义的普通属性默认是可枚举的，而原生继承的属性是不可枚举的。

	//由于原生继承的属性默认不可枚举，所以只取得自定义的属性a:1
	var o = {a:1};
	for(var i in o){
	    console.log(o[i]);//1
	}

---

	//由于enumerable被设置为false，在for-in循环中a属性无法被枚举出来
	var o = {a:1};
	Object.defineProperty(o,'a',{enumerable:false});
	for(var i in o){
	    console.log(o[i]);//undefined
	}

propertyIsEnumerable()

propertyIsEnumerable()方法用于判断对象的属性是否可以枚举。

	var o = {a:1};
	console.log(o.propertyIsEnumerable('a'));//true
	Object.defineProperty(o,'a',{enumerable:false});
	console.log(o.propertyIsEnumerable('a'));//false

**4、get和set**

get是一个隐藏函数，在获取属性时调用。set也是一个隐藏函数，在设置属性值的时候调用，它们的默认值都是undefined。Object.definedProperty()中的get()和set()对应于对象字面量中的get和set方法。

getter和setter取代了数据属性中的value和writable属性。

1、给只设置get方法，没有设置set方法的对象赋值会静默失败，在严格模式下会报错。

	var o = {
	    get a(){
	        return 2;
	    }
	}    
	console.log(o.a);//2
	//由于没有设置set方法，所以o.a=3的赋值语句会静默失败
	o.a = 3;
	console.log(o.a);//2

---

	Object.defineProperty(o,'a',{
	    get: function(){
	        return 2;
	    }
	})
	console.log(o.a);//2
	//由于没有设置set方法，所以o.a=3的赋值语句会静默失败
	o.a = 3;
	console.log(o.a);//2

在严格模式下，给没有设置set方法的访问器属性赋值会报错。

	'use strict';
	var o = {
	    get a(){
	        return 2;
	    }
	}    
	console.log(o.a);//2
	//由于没有设置set方法，所以o.a=3的赋值语句会报错
	//Uncaught TypeError: Cannot set property a of #<Object> which has only a getter
	o.a = 3;

---

	'use strict';
	Object.defineProperty(o,'a',{
	    get: function(){
	        return 2;
	    }
	})
	console.log(o.a);//2
	//由于没有设置set方法，所以o.a=3的赋值语句会报错
	//Uncaught TypeError: Cannot set property a of #<Object> which has only a getter
	o.a = 3;

2、只设置set方法，而不设置get方法，则对象属性值为undefined。

	var o = {
	    set a(val){
	        return 2;
	    }
	}    
	o.a = 1;
	console.log(o.a);//undefined

---

	Object.defineProperty(o,'a',{
	    set: function(){
	        return 2;
	    }
	})
	o.a = 1;
	console.log(o.a);//undefined

3、一般地，set和set方法是成对出现的。

	var o ={
	    get a(){
	        return this._a;
	    },
	    set a(val){
	        this._a = val*2;
	    }
	}
	o.a = 1;
	console.log(o.a);//2

---

	Object.defineProperty(o,'a',{
	    get: function(){
	        return this._a;
	    },
	    set :function(val){
	        this._a = val*2;
	    }
	})
	o.a = 1;
	console.log(o.a);//2

## 2.4 对象状态

属性描述符只能用来控制对象中一个属性的状态，而如果要控制对象的状态，就要用到下面的6中方法。

**1、Object.preventExtensions()（禁止扩展）**

Object.preventExtensions()方法使一个对象无法再添加新的属性，并返回当前对象。

**2、Object.isExtensiable()（测试扩展）**

Object.isExtensiable()方法用来检测该对象是否可以扩展。

	var o = {a:1};
	console.log(Object.isExtensible(o));//true
	o.b = 2;
	console.log(o);//{a: 1, b: 2}
	console.log(Object.preventExtensions(o));//{a: 1, b: 2}
	//由于对象o禁止扩展，所以该赋值语句静默失败
	o.c = 3;
	console.log(Object.isExtensible(o));//false
	console.log(o);//{a: 1, b: 2}

在严格模式下，给禁止扩展的对象添加属性会报TypeError错误。

	'use strict';
	var o = {a:1};
	console.log(Object.preventExtensions(o));//{a:1}
	//Uncaught TypeError: Can't add property c, object is not extensible
	o.c = 3;

Object.preventExtensions()方法并不改变对象中属性的描述符状态。

	var o = {a:1};
	//{value: 1, writable: true, enumerable: true, configurable: true}
	console.log(Object.getOwnPropertyDescriptor(o,'a'));
	Object.preventExtensions(o);
	//{value: 1, writable: true, enumerable: true, configurable: true}
	console.log(Object.getOwnPropertyDescriptor(o,'a'));

**3、Object.seal()（对象封印）**

对象封印又叫做对象密封，是一个对象不可扩展并且所有属性不可配置，并返回当前对象。

**4、Object.isSealed()（测试封印）**

Object.isSealed()方法用来检测方法是否被封印。

	var o = {a:1,b:2};
	console.log(Object.isSealed(o));//false
	console.log(Object.seal(o));//{a:1,b:2}
	console.log(Object.isSealed(o));//true
	console.log(delete o.b);//false
	o.c = 3;
	console.log(o);//{a:1,b:2}

在严格模式下，删除旧属性和添加新属性都会报错。

	'use strict';
	var o = {a:1,b:2};
	console.log(Object.seal(o));//{a:1,b:2}
	//Uncaught TypeError: Cannot delete property 'b' of #<Object>
	delete o.b;

这个方法实际上会在现有对象上调用Object.preventExtensions()方法，并把所有现有属性的configurable描述符设置为false。

	var o = {a:1,b:2};
	//{value: 1, writable: true, enumerable: true, configurable: true}
	console.log(Object.getOwnPropertyDescriptor(o,'a'));
	console.log(Object.seal(o));//{a:1,b:2}
	//{value: 1, writable: true, enumerable: true, configurable: false}
	console.log(Object.getOwnPropertyDescriptor(o,'a'));

**5、Object.freeze()（对象冻结）**

Object.freeze()方法使一个对象不可扩展，不可配置，也不可改写，变成一个仅可以枚举的只读常量，并返回当前对象。

**6、Object.isFrozen()（检测冻结）**

Object.isFrozen()方法用来检测一个对象是否被冻结。

	var o = {a:1,b:2};
	console.log(Object.isFrozen(o));//false
	console.log(Object.freeze(o));//{a:1,b:2}
	console.log(Object.isFrozen(o));//true
	o.a = 3;
	console.log(o);//{a:1,b:2}

在严格模式下，删除旧属性、添加新属性、更改现有属性都会报错。

	'use strict';
	var o = {a:1,b:2};
	console.log(Object.freeze(o));//{a:1,b:2}
	//Uncaught TypeError: Cannot assign to read only property 'a' of object '#<Object>'
	o.a = 3;

这个方法实际上会在现有对象上调用Object.seal()方法，并把所有的现有属性writable描述符设置为false。

	var o = {a:1};
	//{value: 1, writable: true, enumerable: true, configurable: true}
	console.log(Object.getOwnPropertyDescriptor(o,'a'));
	console.log(Object.freeze(o));//{a:1}
	//{value: 1, writable: false, enumerable: true, configurable: false}
	console.log(Object.getOwnPropertyDescriptor(o,'a'));

## 2.5 对象拷贝

对象拷贝分为浅拷贝（shallow）和深拷贝（deep）。浅拷贝只是复制一层对象的属性，并不会进行递归复制，而javascript存储对象都是存地址的，所以**浅拷贝导致对象中的子对象指向同一块内存地址；**而深拷贝则不同，它不仅将原对象的各个属性逐个复制出去，而且将原对象的各个属性所包含的对象也依次采用深拷贝的方法进行递归复制到新对象上，拷贝了所有层级。下面将介绍对象拷贝。

### 2.5.1 浅拷贝

1、简单拷贝

建立一个空对象，使用for-in循环，将对象的所有属性复制到新建的空对象中。

	function simpleClone1(obj){
	    if(typeof obj != 'object'){
	        return false;
	    }
	    var cloneObj = {};
	    for(var i in obj){
	        cloneObj[i] = obj[i];
	    }
	    return cloneObj;
	}
	
	var obj1={a:1,b:2,c:[1,2,3]};
	var obj2=simpleClone1(obj1);
	console.log(obj1.c); //[1,2,3]
	console.log(obj2.c); //[1,2,3]
	obj2.c.push(4);
	console.log(obj2.c); //[1,2,3,4]
	console.log(obj1.c); //[1,2,3,4]

2、使用属性描述符

通过对象的原型，建立一个空的实例对象。通过forEach语句。获取到对象的所有属性描述符，设置到新建的空实例对象中。

	function simpleClone2(orig){
	    var copy = Object.create(Object.getPrototypeOf(orig));
	    Object.getOwnPropertyNames(orig).forEach(function(propKey){
	        var desc = Object.getOwnPropertyDescriptor(orig,propKey);
	        Object.defineProperty(copy,propKey,desc);
	    });
	    return copy;
	}
	
	var obj1={a:1,b:2,c:[1,2,3]};
	var obj2=simpleClone1(obj1);
	console.log(obj1.c); //[1,2,3]
	console.log(obj2.c); //[1,2,3]
	obj2.c.push(4);
	console.log(obj2.c); //[1,2,3,4]
	console.log(obj1.c); //[1,2,3,4]

3、使用jquery中的extend()方法

	var obj1={a:1,b:2,c:[1,2,3]};
	var obj2=$.extend({},obj1);
	console.log(obj1.c); //[1,2,3]
	console.log(obj2.c); //[1,2,3]
	obj2.c.push(4);
	console.log(obj2.c); //[1,2,3,4]
	console.log(obj1.c); //[1,2,3,4]

### 2.5.2 深拷贝

1、遍历复制

复制对象的属性时，对其进行判断，如果是数组或对象，则再次调用拷贝函数；否则直接复制函数属性。

	function deepClone1(obj,cloneObj){
	    if(typeof obj != 'object'){
	        return false;
	    }
	    var cloneObj = cloneObj || {};
	    for(var i in obj){
	        if(typeof obj[i] === 'object'){
	            cloneObj[i] = (obj[i] instanceof Array) ? [] : {};
	            arguments.callee(obj[i],cloneObj[i]);
	        }else{
	            cloneObj[i] = obj[i]; 
	        }  
	    }
	    return cloneObj;
	}
	
	var obj1={a:1,b:2,c:[1,2,3]};
	var obj2=deepClone1(obj1);
	console.log(obj1.c); //[1,2,3]
	console.log(obj2.c); //[1,2,3]
	obj2.c.push(4);
	console.log(obj2.c); //[1,2,3,4]
	console.log(obj1.c); //[1,2,3]

2、json

用JSON全局对象的parse和stringify方法来实现深复制是一个简单讨巧的方法，他能正确处理的对象只有Numner、String、Boolean、Array、扁平对象，即那些能够被json直接表示的数据结构。

	function jsonClone(obj){
	    return JSON.parse(JSON.stringify(obj));
	}
	
	var obj1={a:1,b:2,c:[1,2,3]};
	var obj2=jsonClone(obj1);
	console.log(obj1.c); //[1,2,3]
	console.log(obj2.c); //[1,2,3]
	obj2.c.push(4);
	console.log(obj2.c); //[1,2,3,4]
	console.log(obj1.c); //[1,2,3]

3、使用jquery长度extend()方法

	// 注意与浅复制不同的参数
	var obj1={a:1,b:2,c:[1,2,3]};
	var obj2=$.extend(true,{},obj1);
	console.log(obj1.c); //[1,2,3]
	console.log(obj2.c); //[1,2,3]
	obj2.c.push(4);
	console.log(obj2.c); //[1,2,3,4]
	console.log(obj1.c); //[1,2,3]
