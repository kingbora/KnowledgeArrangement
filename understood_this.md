# 全面理解javascript中的this

## 普通this指向
> `this`的指向在函数定义时是无法确定的，只有函数执行时才能确定this到底指向谁，实际上this最终指向的是那个调用它的对象。

### 1、实例
```js
function a() {
	var user = 'kingbora';
	console.log(this.user); //undefined
	console.log(this); //Window
}

a();
```
看到上面的结果你是不是会质疑，咦，你刚刚不是说`this`最终指向的是那个调用它的对象么？别急，听我慢慢道来。其实这里的函数`a`实际是被`Window`对象所`.`出来的，上面的`a()`可以写成`window.a()`,这些明白了吧！顺便提一下alert也是window的一个属性，也是window`.`出来的。

##### 举一反三1 
```js
var o = {
	user: 'kingbora',
	fn: function(){
		console.log(this.user); //kingbora
	}
}

o.fn();
```
> 这里的this指向的是对象o，因为你调用函数fn是通过`o.fn()`执行的，那自然指向的就是对象o。

##### 举一反三2
```js
var o = {
	user: 'kingbora',
	fn: function() {
		console.log(this.user); //kingbora
	}

window.o.fn();
```

##### 举一反三3 
```js
var o = {
	a: 10,
	c: 'kingbora',
	b: {
		a: 12,
		fn: function() {
			console.log(this.a);  //12
			console.log(this.c); //undefined
		}
	}
}

o.b.fn();
```

##### 举一反三4
```js
var o = {
	a: 10,
	b: {
		a: 12,
		fn: function() {
			console.log(this.a); //undefined
			console.log(this); //window
		}
	}
}

var j = o.b.fn;
j();
```
> 虽然函数fn是被对象b所引用，但是在将fn赋值给变量j的时候并没有执行所以最终指向的是window，因为是`window.j()`调用了它，所以this的最终指向是window。

#### 总结
* 如果一个函数中有this，但是它没有被上一级的对象所调用，那么this指向的就是window，这里说明一下javascript严格模式下this指向的不是window,而是undefined。
* 如果一个函数中有this，这个函数被上一级的对象所调用，那么this指向的就是上一级的对象。
* 如果一个函数中有this，这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，this指向的也是它上一级的对象。


## 构造函数版this
### 实例
```js
function fn() {
	this.user = 'kingbora';
}

var a = new fn();
console.log(a.user); //kingbora
```
这里之所以对象a可以点出函数Fn里面的user是因为new关键字可以改变this的指向，将这个this指向对象a，为什么我说a是对象，因为用了new关键字就是创建一个对象实例，理解这句话可以想想我们的例子1，我们这里用变量a创建了一个Fn的实例（相当于复制了一份Fn到对象a里面），此时仅仅只是创建，并没有执行，而调用这个函数Fn的是对象a，那么this指向的自然是对象a，那么为什么对象Fn中会有user，因为你已经复制了一份Fn函数到对象a中，用了new关键字就等同于复制了一份。
> 除了上面的这些以外，我们还可以自行改变this的指向，关于自行改变this的指向请看JavaScript中call,apply,bind方法，详细的说明了我们如何手动更改this的指向，这个将在下面提到。

##### 举一反三1
```js
function fn() {
	this.user = 'kingbora';
	return {};
}

var a = new fn();
console.log(a.user); //undefined
```

##### 举一反三2
```js
function fn() {
	this.user = 'kingbora';
	return function() { };
}

var a = new fn();
console.log(a.user); //undefined
```

##### 举一反三3
```js
function fn() {
	this.user = 'kingbora';
	return 1;
}

var a = new fn();
console.log(a.user); //kingbora
```

##### 举一反三4
```js
function fn() {
	this.user = 'kingbora';
	return undefined;
}

var a = new fn();
console.log(a.user); //kingbora
```
#### 总结
如果返回值是一个对象，那么this指向的就是那个返回的对象，如果返回值不是一个对象，那么this还是指向函数的实例。（*虽然null也是对象，但在这里this还是指向那个函数的实例，因为null比较特殊*）

## call()
> 语法：call([thisObj[,arg1[,arg2[,..argN]]]])

> 定义：调用一个对象的一个方法，以另一个对象替换当前对象。

> 说明：call方法可以用来代替另一个的对象调用一个方法。call方法可将一个函数的对象上下文从初始的上下文改变为有thisObj指定的新对象。

```js
function add(a, b) {
	console.log(a + b);
}

function sub(a, b) {
	console.log(a - b);
}

add.call(sub, 3, 1);
```
> 这个例子中的意思是用add来代替sub，`add.call(sub, 3, 1) == add(3, 1)`，所以运行结果为4。

### 实例
```js
var a = {
	user: 'kingbora',
	fn: function() {
		console.log(this.user); //kingbora
	}
}

var b = a.fn;
b.call(a);
```
通过call方法，this会指向call方法中第一个参数的这个对象

##### 举一反三
```js
var a = {
	user: 'kingbora',
	fn: function(e, ee) {
		console.log(this.user); //kingbora
		console.log(e+ee); //3
	}
}

var b = a.fn;
b.call(a, 1, 2);
```

## apply()
> 语法：apply([thisObj[,argArray]])

> 定义：应用某一个对象的一个方法，用另一个对象替换当前对象。

> 说明：如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。 如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。

### 实例
```js
var a = {
	user: 'kingbora',
	fn: function(e, ee) {
		console.log(this.user); //kingbora
		console.log(e + ee); //11
	}
}

var b = a.fn;
a.apply(a,[10,1]);
```
apply方法和call方法有些相似，它也可以改变this的指向。同样apply也可以有多个参数，但是不同的是，第二个参数必需是一个数组。
##### 举一反三
```js
var a = {
	user: 'kingbora',
	fn: function() {
		console.log(this); //window
	}
}

var b = a.fn;
b.apply(null);
```
> 注意，如果call和apply的第一个参数写的是null，那么this指向的是window对象

## bind()
> 该方法创建一个新函数，称为绑定函数，绑定函数会以创建它时传入bind方法的第一个参数作为this，传入bind方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。

```js
var a = {
	user: 'kingbora',
	fn: function() {
		console.log(this.user);
	}
}

var b = a.fn;
b.bind(a);
```
我们发现并没有输出，对，这就是bind和call、apply方法不同之处，实际上bind方法返回的是一个修改过后的函数。再看看下面的例子
```js
var a = {
	user: 'kingbora',
	fn: function() {
		console.log(this.user); //kingbora
	}
}

var b = a.fn;
var c = b.bind(a);
c();
```
同样bind也可以有多个参数，并且参数可执行时再次被添加，但是要注意的是，参数是按照形参的顺序进行的。
```js
var a = {
	user: 'kingbora',
	fn: function(e, d, f) {
		console.log(this.user); //kingbora
		console.log(e, d, f); //10 1 2
	}
}
var b = a.fn;
var c = b.bind(a, 10);
c(1, 2);
```
#### 总结
call和apply都是改变上下文中的this并立即执行这个函数，bind方法可以让对应的函数想什么时候调用就什么时候调用，并且可以将参数在执行的时候添加，这是它们的区别。bind()主要解决回调函数绑定的问题。

## 其他情况this
```js
kingbora.onclick = function() {
	function fn() {
		console.log(this); //window
	}
	console.log(this); //[object HTMLButtonElement]->kingbora
	fn();
}
```
> 这里说明一下，为何第一个this指向的是window对象，这里普遍被认为是JavaScript语言的设计错误，因为没有人想让内部函数中的this指向全局对象。
