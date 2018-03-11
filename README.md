# css样式相关

## DOM的reflow & repaint

[参考文章](https://juejin.im/entry/58d8d4b3570c350058e27ec6)

reflow对性能的开销会比repaint大

渲染过程 DOM --> cssDOM --> renderTree

### 造成重排的原因（reflow)

  对元素盒模型的修改（height width border padding margin font-size等等）。

### 造成重绘的原因（repaint)

  不影响css盒模型的属性只会造成重绘（颜色 背景等）

### 特殊情况

普通情况下浏览器会缓存并进行批量操作， 但是下面的操作会实时的反应到dom上，造成性能问题。
另外以下属性会让浏览器立刻重排，为什么呢？因为重排并不是立刻产生作用的，浏览器有自身的优化手段。

offsetTop, offsetLeft, offsetWidth, offsetHeight

scrollTop/Left/Width/Height

clientTop/Left/Width/Height

getComputedStyle(), or currentStyle in IE

请求这些属性之前，如果有修改css盒模型，就会造成浏览器立刻重排以便计算最新属性。

### 如何优化

1. 不单独设置css属性，如document.body.style.width='100px'; 
2. 如果一定通过js修改样式有以下两种方法document.body.style.cssText=''; // 一次性修改document.body.className = 'newClass'; // 修改class来实现最小次数重排
3. 通过display:none来将节点脱离文档流，在修改完节点之后再显示，这样只有隐藏，显示两次。
4. 将会立刻造成重排的属性数据存入临时变量中。
5. position:absolute,fixed 会将节点脱离文档流，避免整个dom tree重排
6. 移动通过translate来实现
7. 浏览器原理中,(root节点,css position:relative,absolute,transform，有节点溢出overflow, alpha mask, reflection,css filter,2dCanvas,WebGl,video)都会为这些元素创造一个新的Layer以便浏览器加速渲染，这些元素的修改只会涉及自己重排重绘。

## 常用布局

### 圣杯和双飞翼（三栏式布局）

[参考链接](https://segmentfault.com/a/1190000012213999)

关键代码
```html
<div class="container">
  <div class="center">center</div> //中间这一块写在最上面
  <div class="left">left</div>
  <div class="right">right</div>
</div>
```

#### 相同 
三块div都是float: left;
left margin-left: -100%
right margin-left: -自己的宽度

#### 区别 
双飞翼： 更简单一点 container使用margin空出两拦的宽度, that's all;
圣杯：container使用padding来空出两拦的宽度, 这时候需要把left往左边移动一倍自己的宽度， right往右边再移动自己一倍的宽度

### float flex grid 自适应屏幕布局

[参考链接](https://juejin.im/post/5a22d0086fb9a0451a7631ee)

float: left 来进行布局 通过media对不同屏幕宽度进行width操作（@media screen and (max-width: 1024) 用来控制屏幕宽度小于1024px的）
inline-block: 显示属性改为display: inline 和float类似
flex: 同样display: flex; 同时要增加个属性flex-wrap: wrap 使得一行中排不下的元素可以自动换行
grid: 网格状划分页面 参考上面文章链接

## 零碎的知识点

### 字体大小：

em是相对于父元素的，rem是相对于根节点的

### position:

absolute是相对于relative属性的元素 如果找不到relative的元素 会一直找到body上(ps: absolute是脱离文档流的，不会构成reflow);

relative是相对原来在文档流中的位置，进行位移

fixed则是相对于屏幕进行定位， 屏幕滚动不会影响到fixed元素的定位

# JS相关

## JavaScript 节流（throttle）

[原文链接](https://github.com/mqyqingfeng/Blog/issues/26)

[参考链接](http://www.alloyteam.com/2012/11/javascript-throttle/)

在持续触发的情况下 每隔一段时间

有两种实现方式

## JavaScript 防抖(debouce)

[参考链接](https://github.com/mqyqingfeng/Blog/issues/22)

停止触发之后等若干秒， 才执行

## cookie localStorage sessionStorage      

三者都是不能跨域的

| | 大小 | 存储位置 | 生命周期 |
|:---:|----|---|---|
|cookie|    4K左右  | 每次都会携带在HTTP头中| 一般由服务器生成，可设置失效时间。如果在浏览器端生成Cookie，默认是关闭浏览器后失效|
|localStorage | 5M  |浏览器本地| 永久|
|sessionStorage| 5M |浏览器本地 |会话（关闭浏览器窗口后，数据会被删除）|

## 雅虎web网站优化34条规则

[雅虎web网站优化34条规则](https://www.jianshu.com/p/3d77c3d3cc8f)

## new运算做了什么

[参考链接](https://axiu.me/coding/what-new-done-in-javascript/)

[MDN 对new运算的解释](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)
1.将对象的 __phroto__  属性设置为函数的phrototype
2.把this指向该对象
3.返回这个对象

可以归纳为下面这个代码

```javascript
function New (f) {
	var n = { '__proto__': f.prototype }; //步骤1
	return function () {
		f.apply(n, arguments); //步骤2&3
		return n; //步骤4
	};
}
```
### prototype 的其他注意点
先看个题目      
```javascript
Object.prototype.a = 'a';
Function.prototype.a = 'b';

funtion Foo() {};
var obj = new Foo();
console.log(obj.a);// 'a'
console.log(obj.b);// undefined
console.log(Foo.a);// 'a'
console.log(Foo.b);// 'b'
```
对象的__proto__指向自己构造函数的prototype。obj.\_\_proto\_\_.\_\_proto\_\_...的原型链由此产生，包括我们的操作符instanceof正是通过探测obj.\_\_proto\_\_.\_\_proto\_\_... === Constructor.prototype来验证obj是否是Constructor的实例。

```javascript
obj.__proto__ === Foo.prototype;
obj.__proto__.__proto__ === Foo.prototype.__proto__ = Object.prototype;
Object.prototype.__proto__ === null;

```
所以obj能够找到a的值，但是因为无法链到Function.prototype上，所以b的值就是undefined了。

``` javascript
Foo.__proto__ === Function.prototype; // 这就能找到b的值
Foo.__proto__.__proto__ === Function.prototype.__proto__ === Object.prototype; // 所以a也找到了。
```
有个有趣的事实得记住Object.\_\_proto\_\_ === Function.prototype, 因为Object也是个构造函数。

## js对类型的封装

### 构造函数模式

```javascript
  function Cat(name, color) {
    this.name = name;
    this.color = color;
  }

  let cat1 = new Cat('bin1', 'red');
  let cat2 = new Cat('bin2', 'white');

  cat1.constructor === cat2.constructor === Cat; // true
  cat1.__proto__ === cat2.__proto__ === Cat.prototype;


```
缺点：

Cat 中加个eat的方法
```javascript
  function Cat(name, color) {
    this.name = name;
    this.color = color;
    this.eat = function() {return 'eat'};
  }

  cat1.eat === cat2.eat // false
```

创建函数的时候每次都会重现创建一个eat方法， 浪费资源;

### prototype模式

可以将不变的属性直接加在prototype上
```javascript
  function Cat(name, color) {
    this.name = name;
    this.color = color;
  }

  Cat.phrototype.eat = function() {return 'eat'};

  let cat1 = new Cat('bin1', 'red');
  let cat2 = new Cat('bin2', 'white');

  cat1.eat === cat2.eat // true
```

## js中继承模式

[参考阮一峰博客](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)

```javascript
  function Animal() {
    this.type = '动物';
  }
```
有个Cat类如何来继承Animal?

### 构造函数绑定
```javascript
  function Cat(name, color) {
    // 直接使用apply 来调用父类Animal
    Animal.apply(this, arguments);
    this.name = name;
    this.color = color;
  }

  const cat1 = new Cat('bin1', 'black');

  alert(cat1.type); // 动物
```

### prototype 模式

```javascript
  Cat.prototype = new Animal(); // 这里会改变constructor函数的指向 Animal
  Cat.prototype.constructor = Cat; // 重现将构造函数指向Cat

  const cat1 = new Cat('bin1', 'black');
  alert(cat1.type) // 动物；
```
如果不重现分配constructor 则直接指向了 Animal

```javascript
  Cat.prototype = new Animal();
  alert(Cat.prototype.constructor === Animal) // true
```

### 直接继承prototype
```javascript
  Cat.prototype = Animal.prototype;
  Cat.prototype.constructor = Cat; // 还是需要修改constructor
  const cat1 = new Cat('bin1', 'black');
  alert(cat1.type) // 动物；
```
但是这种模式有个弊端 把Animal的构造函数也改了

```javascript
  alert(Animal.prototype.constructor === Cat); // true
```

### 利用空对象作为中介

```javascript
  var O = function() {};
  O.prototype = new Animal(); // 需要一个实例化的对象 不然属性type无法被继承
  Cat.prototype = new O();
  // 把Cat的构造函数重新指向Cat
  Cat.prototype.constructor = Cat;
```

### 拷贝继承
将父类prototype上的属性全部复制下来

```javascript
  function extend(child, parent) {
    let c = child.prototype;
    let p = parent.prototype;
    for (let i in p) {
      c[i] = p[i];
    }
    c.uber = p;
  }
```

### 没有构造函数的继承
``` javascript
var Person = {
    nation: 'china'
  }

var Doctor = {
  career: 'doctor'
}
```
Doctor 如何继承 Person？

1. object 方法
``` javascript
  function object(parent) {
    var F = function() {};
    F.prototype = parent;
    return new F()
  }
```

2.浅拷贝
``` javascript
  function copy(parent) {
    var child = {};
    for (let i in parent) {
      child[i] = parent[i];
    }
    return child;
  }
```

3.深拷贝
``` javascript
  function deepCopy(parent, c) {
    var child = c || {};
    for (let i in parent) {
      if (typeof parent[i] === 'object') {
        child[i] = deepCopy(parent[i], parent[i].constructor === Array ? [] : {});
      } else {
        child[i] = parent[i];
      }
    }
    return child;
  }
```
# REACT
## 组件的生命周期

### 实例化
#### 首次实例化

getDefaultProps

getInitialState

componentWillMount

render

componentDidMount

#### 实例化完成后的更新

getInitialState

componentWillMount

render

componentDidMount

### 存在期
#### 组件已存在时的状态改变

componentWillReceiveProps

shouldComponentUpdate

componentWillUpdate

render

componentDidUpdate
### 销毁&清理期
componentWillUnmount

一般在DOM加载完之后 调用接口获取数据 所以在componentDidMount中调用接口

willMount中setState并不会触发渲染， 而且willMount可能会被触发多次

# HTTP相关

## 浏览器对http请求并发的限制
浏览器对同一域名的请求有并发限制 一般同一域名最多并发6个请求 所以有些资源会放在不同的域名下
