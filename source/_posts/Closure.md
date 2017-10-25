---
tag: [JS] title: JS中的闭包  
---

### 闭包是什么？

闭包是基于词法作用域书写代码时所产生的自然结果，你甚至不需要为了利用它们而有意识地创建闭包。闭包的创建和使用在JS代码中随处可见。因此我们需要有能力去辨别和主动使用它。

### 广义的闭包  

也因为闭包没有一个固定的概念，不同的人对于闭包的理解都不尽相同。  
有人会说：当子函数调用了父级作用域中的变量时就产生了闭包。
  
<!--more-->		
看个例子 
 
  
		function foo(){
			var a = 2;
			function bar(){
				console.log(a);
				debugger;
			}
			bar();
		}
		foo();
		
		//甚至直接这样
		var a = 2;
		(function IIFE(){
			console.log(a);
			debugger;
		})();
这是闭包吗？子函数bar调用了foo函数作用域中的a变量，自执行函数IIFE()调用了全局作用域中a，符合上面闭包的说法。而且debugger的时候确实能在浏览器调试界面中看到closure。但是这种闭包其实没有太大的意义(后面会说明)，而且按照这个说法的话JS里几乎所有的函数都是闭包，因为所有的函数都是基于词法作用域逐级向上回溯的规则创建的，这显然不符合我们的理解。
  
### 狭义的闭包  

我们通常所讲的闭包是狭义上的闭包，它的形成具有两个特点：  
1. 内部函数访问了所在词法作用域中的变量(即外部函数作用域中的变量)；  
2. 该内部函数在所引用的变量所在的词法作用域之外的地方执行。  
根据上面的狭义的闭包的说明，我们看几个例子

### 闭包的三种类型  

关于上面的说明，第一点其实就是变量作用域的规则，正常情况下我们定义的内部函数都会访问外部的词法作用域中的变量，不然这个函数就没必要写在函数的内部。而第二点恰恰又违背了作用域的规则，因为按照规则在内部函数词法作用域以外的地方是访问不到内部函数的。我们有三种方式来打破这种规则：  
**1.通过return将内部函数返回给外部**
  
	 	function foo(){
			var a = 2;

			 function bar(){
			 	console.log(a);
			 }

			 return bar; //返回内部函数
		}

		var baz = foo();
		baz();//2 
**2.将内部函数以参数的形式传递给外部**
  
		function foo(){
			var a = 2;
			
			function baz(){
				console.log(a);
			}
			bar(baz); //内部函数baz以参数形式传递
		}

		function bar(fn){
			fn();
		}
		
		foo();
**3.将内部函数的引用传递给外部变量**  
  
		var fn;

		function foo(){
			var a = 2;

			function baz(){
				console.log(a);
			}

			fn = baz;   //将baz分配给外部变量
		}

		function bar(){
			fn();
		}

		foo();

		bar();//2
小结：无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用域的的应用，之后无论在何处执行这个函数都会产生闭包。

### 闭包的应用  

**回调函数**  
分析闭包的第二种类型，我们发现传递内部函数的其实就是回调函数。因此下面的情况就是闭包
		
		function wait(message){
			setTimeout(function timer(){
				console.log(message);
			},1000);
		}

		wait('Hello,closure!');  

内部函数timer被传递给了setTimeout(),而setTimeout是全局的方法，因此timer的执行是在wait作用域以外的地方完成的，同时timer保有对wait作用域中的message变量的引用，这样就形成了闭包。  
同样的例子：  

		function setupBot(name,selector){
			$(selector).click(function activator(){
				console.log('activating: '+name);
			});
		}
		setupBot('Closure Bot1','#bot_1');
		setupBot('closure Bot2','#bot_2');
也形成了闭包。在定时器、事件监听器、Ajax请求、跨窗口通信、Web Workers或者任何其他的异步(或者同步)任务中，只要使用了回调函数，基本都会产生闭包，只是我们没有意识到而已。

**保存作用域**  
再看一个需求  

	var node = document.getElementsByTagName('div');
	
	for(var i=0;i<node.length;i<len;i++){
		node[i].onclick = function(){
				alert(i);
		}
	};
假如这里有5个div，我们的本意是想依次给这5个div绑定click事件，当我们点击的div的时候，能弹出这个div对应的值。比如点第一个div弹出0，点第二个弹出1...  
但实际当我们点击的时候，不管点那个div，弹出的都是5。为什么？因为当我们触发点击事件的时候这里的循环函数早就执行完了，i变成了5。因此从作用域链中找的i总是5。循环的时候并没有执行函数，只不过是把函数重复定义了5次而已。  
我们按照闭包的特定试图去修复一下这个问题  

	var node = document.getElementsByTagName('div');
	
	for(var i=0;i<node.length;i++){
		（function(i){
			 node[i].onclick = function(){
				alert(i);
			}
		})(i)
	};
这样就可以了，为什么？  
我们给匿名函数包了一层自执行的匿名函数，同时在IIFE中声明了一个i变量，用于保存传入的i。这样内部匿名函数引用到的i不再是全局作用域中的i，而是IIFE中定义的i。这样就形成了跟类型3一样的闭包。  
那么为什么IIFE中i的值是可变的呢？  
这其实是闭包的一种特性。我们知道JS引擎具有垃圾回收机制，函数的作用域在函数执行完以后会被销毁。用于释放占用的内存空间。但是由于闭包中内部函数保有对外部函数变量(作用域)的引用，就会阻止这一进程的实现，这样就使得闭包具有了延长变量寿命的作用(或者叫保存函数作用域)。  
再来看这个闭包我们就明白了，在每次循环中传入IIFE的i值被保存到了作用域中，整个循环过程创建了5个函数作用域，里面保存了不同的i值。这样就可以解释了。  
也正是因为闭包保有函数作用域，导致内存无法释放，当循环的次数够大的时候会造成内存泄漏，导致浏览器崩溃。所以在使用闭包的时候一定要注意这种DOM节点的绑定，它会一直占用你的内存，不管你是否触发它。  
继续看上面的例子，我们用的第3种类型改进的，如果用第1种是怎么样的呢？
	
	var node = document.getElementsByTagName('div');
	
	for(var i=0;i<node.length;i++){
		//创建一个helper函数(闭包)
		function helper(){
			var j=i;
			return function bar(){
				alert(j);
			}
		}
		node[i].onclick = helper();
	}
这样就不用把DOM节点写进闭包了。同样上面的例子把节点的绑定写在外面也是可以的。再换一种实现   
  
	var node = document.getElementsByTagName('div');
	
	for(var i=0;i<node.length;i++){
		//创建一个helper函数(闭包)
		function helper(){
			var j=i;
			callback(function(){
				alert(j);
			});
		}
		function callback(fn){
			node[i].onclick=fn;
		} 
	  helper();
	}
当然一般都不会这么去写...更多的是return的方法去实现闭包。  

**变量的封装**  
闭包可以帮助把一些不需要暴露在全局的变量封装成“私有变量”。例如下面的函数

	var cache = {}; //用于缓存计算结果
	
	var mult = function(){
		var args = Array.prototype.join.call(arguments,',');//args='1,2,3'
		if(cache[args]){   //查询缓存，如果有就直接返回结果
			return cache[args];
		}
		//计算
		var a = 1;		
		for(var i=0;i<arguments.length;i++){
			a=a*arguments[i];
		}
		
		return cache[args] = a; //将计算结果存入缓存对象
	};
	
	alert(mult(1,2,3));//6
	alert(mult(1,2,3));//6，直接从缓存中拿到的结果，不需要计算 
变量cache只用在来mult函数中，没有必要暴露在全局作用域中。可以用函数封装起来。
	
	var mult = (function(){

		var cache = {}; 
	
		return function(){
			var args = Array.prototype.join.call(arguments,',');
			if(cache[args]){
				return cache[args];
			}
			var a = 1;
			for(var i=0;i<arguments.length;i++){
				a=a*arguments[i];
			}
			return cache[args] = a;
		};

	})();
对上面的代码进行重构，提炼计算用的函数
	
	var mult = (function(){

		var cache = {}; 
		
		var calculate = function(){ //提炼calculate函数
			var a = 1;
			for(var i=0;i<arguments.length;i++){
				a=a*arguments[i];
			}
			return a;
		}
		return function(){
			var args = Array.prototype.join.call(arguments,',');
			if(cache[args]){
				return cache[args];
			}
			return cache[args] = calculate.apply(null,arguments);			
			//注意调用calculate的方式
		};
	})();
**模块模式**  
在面向对象的设计过程中，模块模式是很常见的。它具备两个必要条件：   
1. 必须有外部的封闭函数，该函数必须至少被调用一次(每次调用都会创建一个新的模块实例)。  
2. 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有状态。  

从这两个条件可以看出上面例子中mult函数就是一个模块。mult模块返回的是一个内部函数，也可以返回一个引用内部函数的对象。通过对象的方法来调用内部函数。  

	var mult = (function(){

		var cache = {}; 
		
		function calculate(){ //提炼calculate函数
			var a = 1;
			for(var i=0;i<arguments.length;i++){
				a=a*arguments[i];
			}
			return a;
		}
		function result(){
			var args = Array.prototype.join.call(arguments,',');
			if(cache[args]){
				return cache[args];
			}
			return cache[args] = calculate.apply(null,arguments);			
			//注意调用calculate的方式
		};
		return{
			result : result
		}
	})();
	mult.result(1,2,3);//6

这样来看，闭包在JS中无处不在，正确地理解闭包是JS学习中很必要的，理解了闭包我们就能识别闭包然后用它来做一些有用的事情。  

**（完）**
