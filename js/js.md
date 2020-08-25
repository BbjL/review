### 声明提升

```
1. 变量声明提升  
* 通过var定义(声明)的变量, 在定义语句之前就可以访问到 
* 值: undefined
2. 函数声明提升 
* 通过function声明的函数, 在之前就可以直接调用 
* 值: 函数定义(对象)

先执行变量提升, 再执行函数提升

  /*
   测试题3:
   */
  var c = 1
  function c(c) {
    console.log(c)
    var c = 3		//不关这里的事
  }
  //第一行的 c = 1 是在变量提升后再执行的，执行时相当于function c = 1 会报错
  c(2) // 报错
```

### 执行上下文

```

1. 代码分类(位置)
  * 全局代码
  * 函数(局部)代码
2. 全局执行上下文
  * 在执行全局代码前将window确定为全局执行上下文
  * 对全局数据进行预处理
    * var定义的全局变量==>undefined, 添加为window的属性
    * function声明的全局函数==>赋值(fun), 添加为window的方法
    * this==>赋值(window)
  * 开始执行全局代码
3. 函数执行上下文
  * 在调用函数, 准备执行函数体之前, 创建对应的函数执行上下文对象(虚拟的, 存在于栈中)
  * 对局部数据进行预处理
    * 形参变量==>赋值(实参)==>添加为执行上下文的属性
    * arguments==>赋值(实参列表), 添加为执行上下文的属性
    * var定义的局部变量==>undefined, 添加为执行上下文的属性
    * function声明的函数 ==>赋值(fun), 添加为执行上下文的方法
    * this==>赋值(调用函数的对象)
  * 开始执行函数体代码
 
1. 在全局代码执行前, JS引擎就会创建一个栈来存储管理所有的执行上下文对象
2. 在全局执行上下文(window)确定后, 将其添加到栈中(压栈)
3. 在函数执行上下文创建后, 将其添加到栈中(压栈)
4. 在当前函数执行完后,将栈顶的对象移除(出栈)
5. 当所有的代码执行完后, 栈中只剩下window

  /*
   测试题1:  先执行变量提升, 再执行函数提升
   */
  function a() {}
  var a
  console.log(typeof a) // 'function'


  /*
   测试题2:
   */
  if (!(b in window)) {
    var b = 1
  }
  console.log(b) // undefined

  /*
   测试题3:
   */
  var c = 1
  function c(c) {
    console.log(c)
    var c = 3
  }
  c(2) // 报错
```

### 作用域

```
1. 理解
  * 就是一块"地盘", 一个代码段所在的区域
  * 它是静态的(相对于上下文对象), 在编写代码时就确定了
2. 分类
  * 全局作用域
  * 函数作用域
  * 没有块作用域(ES6有了)
3. 作用
  * 隔离变量，不同作用域下同名变量不会有冲突
  
区别1
  * 全局作用域之外，每个函数都会创建自己的作用域，作用域在函数定义时就已经确定了。而不是在函数调用时
  * 全局执行上下文环境是在全局作用域确定之后, js代码马上执行之前创建
  * 函数执行上下文是在调用函数时, 函数体代码执行之前创建
区别2
  * 作用域是静态的, 只要函数定义好了就一直存在, 且不会再变化
  * 执行上下文是动态的, 调用函数时创建, 函数调用结束时就会自动释放
联系
  * 执行上下文(对象)是从属于所在的作用域
  * 全局上下文环境==>全局作用域
  * 函数上下文环境==>对应的函数使用域
  
  
1. 理解
  * 多个上下级关系的作用域形成的链, 它的方向是从下向上的(从内到外)
  * 查找变量时就是沿着作用域链来查找的
2. 查找一个变量的查找规则
  * 在当前作用域下的执行上下文中查找对应的属性, 如果有直接返回, 否则进入2
  * 在上一级作用域的执行上下文中查找对应的属性, 如果有直接返回, 否则进入3
  * 再次执行2的相同操作, 直到全局作用域, 如果还找不到就抛出找不到的异常
  
  面试题1
 var x = 10;
  function fn() {
    console.log(x);
  }
  function show(f) {
    var x = 20;
    f();
  }
  show(fn); // 10
  
  
  面试题2
  var fn = function () {
    console.log(fn)   // function
  }
  fn()

  var obj = {
    fn2: function () {
     console.log(fn2)      // 外部作用域（全局作用域）找，没有则报错  
     //console.log(this.fn2)   // 找得到  this指向调用函数的对象
    }
  }
  obj.fn2()

```

### 循环绑定事件的几种办法

```
反例：
for (var i = 0,length=btns.length; i < length; i++) {  
    //btns.length 在这里会计算多次  所以应该踢出去单独获取
    var btn = btns[i]
    btn.onclick = function () {
    alert('第'+(i+1)+'个')
}
 //这个方法并不能实现循环绑定，for循环结束时，按钮被点击函数会获取全局作用域的i，所以每次都会显示length+1
  
方法1：将btn所对应的下标保存在btn上
for (var i = 0,length=btns.length; i < length; i++) {
    var btn = btns[i]
    //将btn所对应的下标保存在btn上
    btn.index = i
    btn.onclick = function () {
    alert('第'+(this.index+1)+'个')
}
  
 //方法2：利用闭包
for (var i = 0,length=btns.length; i < length; i++) {
  (function (j) {
    var btn = btns[j]
    btn.onclick = function () {
    	alert('第'+(j+1)+'个')
    }
   })(i)
}
  
//方法3：利用let形成单独的作用域
for (let i = 0,length=btns.length; i < length; i++) {  
    var btn = btns[i]
    btn.onclick = function () {
    	alert('第'+(i+1)+'个')
	}
}
```

### 闭包

```
1. 如何产生闭包?
  * 当一个嵌套的内部(子)函数引用了嵌套的外部(父)函数的变量(函数)时, 就产生了闭包
2. 闭包到底是什么?
  * 使用chrome调试查看
  * 理解一: 闭包是嵌套的内部函数(绝大部分人)
  * 理解二: 包含被引用变量(函数)的对象(极少数人)
  * 注意: 闭包存在于嵌套的内部函数中
3. 产生闭包的条件?
  * 函数嵌套
  * 内部函数引用了外部函数的数据(变量/函数)
  
function fn1 () {
	var a = 2
    var b = 'abc'
    function fn2 () { //执行函数定义就会产生闭包(不用调用内部函数)
      console.log(a)
    }
    //fn2()    // 调用它可以在chrome查看闭包
}
fn1()


常见闭包函数
 * 将函数作为另一个函数的返回值
 * 将函数作为实参传递给另一个函数调用
 // 1. 将函数作为另一个函数的返回值
  function fn1() {
    var a = 2
    function fn2() {
      a++
      console.log(a)
    }
    return fn2
  }
  var f = fn1()
  f() // 3
  f() // 4

  // 2. 将函数作为实参传递给另一个函数调用
  function showDelay(msg, time) {
    setTimeout(function () {
      alert(msg)
    }, time)
  }
  showDelay('atguigu', 2000)
  
  
闭包的作用
  1. 使用函数内部的变量在函数执行完后, 仍然存活在内存中(延长了局部变量的生命周期)
  2. 让函数外部可以操作(读写)到函数内部的数据(变量/函数)
  
  
问题:
  1. 函数执行完后, 函数内部声明的局部变量是否还存在?  一般是不存在, 存在于闭中的变量才可能存在
  2. 在函数外部能直接访问函数内部的局部变量吗? 不能, 但我们可以通过闭包让外部操作它
  
  
闭包的生命周期
1. 产生: 在嵌套内部函数定义执行完时就产生了(不是在调用)
2. 死亡: 在嵌套的内部函数成为垃圾对象时
  function fn1() {
    //此时闭包就已经产生了(函数提升, 内部函数对象已经创建了)
    var a = 2
    function fn2 () {
      a++
      console.log(a)
    }
    return fn2
  }
  var f = fn1()
  f() // 3
  f() // 4
  f = null //闭包死亡(包含闭包的函数对象成为垃圾对象)
  
  
 闭包的应用：自定义模块
 定义JS模块
  * 具有特定功能的js文件
  * 将所有的数据和功能都封装在一个函数内部(私有的)
  * 只向外暴露一个包含n个方法的对象或函数
  * 模块的使用者, 只需要通过模块暴露的对象调用方法来实现对应的功能
 例1
  var module = myModule()
  module.doSomething()
  module.doOtherthing()
  
 例2
  (function () {
  //私有数据
  var msg = 'My atguigu'
  //操作数据的函数
  function doSomething() {
    console.log('doSomething() '+msg.toUpperCase())
  }
  function doOtherthing () {
    console.log('doOtherthing() '+msg.toLowerCase())
  }

  //向外暴露对象(给外部使用的方法)
  window.myModule2 = {
    doSomething: doSomething,
    doOtherthing: doOtherthing
  }
})()


  myModule2.doSomething()
  myModule2.doOtherthing()
  
  
闭包存在问题：
1. 缺点
  * 函数执行完后, 函数内的局部变量没有释放, 占用内存时间会变长
  * 容易造成内存泄露
2. 解决
  * 能不用闭包就不用
  * 及时释放
  
面试题：
// 关键是返回的内部函数还是外部函数，内部函数返回的是全局作用域的fun
  function fun(n,o) {
    console.log(o)
    return {
      fun:function(m){
        return fun(m,n)
      }
    }
  }
  var a = fun(0)
  a.fun(1)
  a.fun(2)
  a.fun(3)//undefined,0,0,0

  var b = fun(0).fun(1).fun(2).fun(3)//undefined,0,1,2

  var c = fun(0).fun(1)
  c.fun(2)
  c.fun(3)//undefined,0,1,1
```

### 内存溢出与内存泄漏

```
1. 内存溢出
  * 一种程序运行出现的错误
  * 当程序运行需要的内存超过了剩余的内存时, 就出抛出内存溢出的错误
2. 内存泄露
  * 占用的内存没有及时释放
  * 内存泄露积累多了就容易导致内存溢出
  * 常见的内存泄露:
    * 意外的全局变量
    * 没有及时清理的计时器或回调函数
    * 闭包
    
// 1. 内存溢出
  var obj = {}
  for (var i = 0; i < 10000; i++) {
    obj[i] = new Array(10000000)
    console.log('-----')
  }

  // 2. 内存泄露
    // 意外的全局变量
  function fn() {
    a = new Array(10000000)
    console.log(a)
  }
  fn()

   // 没有及时清理的计时器或回调函数
  var intervalId = setInterval(function () { //启动循环定时器后不清理
    console.log('----')
  }, 1000)

  // clearInterval(intervalId)

    // 闭包
  function fn1() {
    var a = 4
    function fn2() {
      console.log(++a)
    }
    return fn2
  }
  var f = fn1()
  f()

  // f = null
```

### this指向

```
面试题
//代码片段一
  var name = "The Window";
  var object = {
    name : "My Object",
    getNameFunc : function(){
      return function(){
        return this.name;
      };
    }
  };
  alert(object.getNameFunc()());  //?  the window  
  //object.getNameFunc()返回的是一个函数  如果在调用this指向的自然是window

  //代码片段二
  var name2 = "The Window";
  var object2 = {
    name2 : "My Object",
    getNameFunc : function(){
      var that = this;
      return function(){
        return that.name2;
      };
    }
  };
  alert(object2.getNameFunc()()); //?  my object
```

### 进程与线程

```
1. 进程：程序的一次执行, 它占有一片独有的内存空间
2. 线程： CPU的基本调度单位, 是程序执行的一个完整流程
3. 进程与线程
  * 一个进程中一般至少有一个运行的线程: 主线程
  * 一个进程中也可以同时运行多个线程, 我们会说程序是多线程运行的
  * 一个进程内的数据可以供其中的多个线程直接共享
  * 多个进程之间的数据是不能直接共享的
4. 浏览器运行是单进程还是多进程?
  * 有的是单进程
    * firefox
    * 老版IE
  * 有的是多进程
    * chrome
    * 新版IE
5. 如何查看浏览器是否是多进程运行的呢?
  * 任务管理器==>进程
6. 浏览器运行是单线程还是多线程?
  * 都是多线程运行的
```

### 浏览器内核

```
1. 什么是浏览器内核?
  * 支持浏览器运行的最核心的程序
2. 不同的浏览器可能不太一样
  * Chrome, Safari: webkit
  * firefox: Gecko
  * IE: Trident
  * 360,搜狗等国内浏览器: Trident + webkit
3. 内核由很多模块组成
  * html,css文档解析模块 : 负责页面文本的解析
  * dom/css模块 : 负责dom/css在内存中的相关处理
  * 布局和渲染模块 : 负责页面的布局和效果的绘制
  * 布局和渲染模块 : 负责页面的布局和效果的绘制

  * 定时器模块 : 负责定时器的管理
  * 网络请求模块 : 负责服务器请求(常规/Ajax)
  * 事件响应模块 : 负责事件的管理
```

### JS是单线程的

```
1. 如何证明js执行是单线程的?
  * setTimeout()的回调函数是在主线程执行的
  * 定时器回调函数只有在运行栈中的代码全部执行完后才有可能执行
2. 为什么js要用单线程模式, 而不用多线程模式?
  * JavaScript的单线程，与它的用途有关。
  * 作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。
  * 这决定了它只能是单线程，否则会带来很复杂的同步问题

3. 代码的分类:
  * 初始化代码
  * 回调代码
4. js引擎执行代码的基本流程
  * 先执行初始化代码: 包含一些特别的代码   回调函数(异步执行)
    * 设置定时器
    * 绑定事件监听
    * 发送ajax请求
  * 后面在某个时刻才会执行回调代码
```

### 事件循环模型

```
1. 所有代码分类
  * 初始化执行代码(同步代码): 包含绑定dom事件监听, 设置定时器, 发送ajax请求的代码
  * 回调执行代码(异步代码): 处理回调逻辑
2. js引擎执行代码的基本流程:
  * 初始化代码===>回调代码
3. 模型的2个重要组成部分:
  * 事件(定时器/DOM事件/Ajax)管理模块
  * 回调队列
4. 模型的运转流程
  * 执行初始化代码, 将事件回调函数交给对应模块管理`
  * 当事件发生时, 管理模块会将回调函数及其数据添加到回调列队中
  * 只有当初始化代码执行完后(可能要一定时间), 才会遍历读取回调队列中的回调函数执行
```

![1596895442081](C:\Users\BJL\AppData\Roaming\Typora\typora-user-images\1596895442081.png)

### JS分线程执行

```
1. H5规范提供了js分线程的实现, 取名为: Web Workers
2. 相关API
  * Worker: 构造函数, 加载分线程执行的js文件
  * Worker.prototype.onmessage: 用于接收另一个线程的回调函数
  * Worker.prototype.postMessage: 向另一个线程发送消息
3. 不足
  * worker内代码不能操作DOM(更新UI)
  * 不能跨域加载JS
  * 不是每个浏览器都支持这个新特性
  
例子：计算斐波那契额数列
var input = document.getElementById('number')
  document.getElementById('btn').onclick = function () {
    var number = input.value

    //创建一个Worker对象
    var worker = new Worker('worker.js')
    // 绑定接收消息的监听
    worker.onmessage = function (event) {
      console.log('主线程接收分线程返回的数据: '+event.data)
      alert(event.data)
    }

    // 向分线程发送消息
    worker.postMessage(number)
    console.log('主线程向分线程发送数据: '+number)
  }
  // console.log(this) // window
  
  
//work.js
function fibonacci(n) {
  return n<=2 ? 1 : fibonacci(n-1) + fibonacci(n-2)  //递归调用
}

console.log(this)
onmessage = function (event) {
  var number = event.data
  console.log('分线程接收到主线程发送的数据: '+number)
  //计算
  var result = fibonacci(number)
  postMessage(result)
  console.log('分线程向主线程返回数据: '+result)
  // alert(result)  alert是window的方法, 在分线程不能调用
  // 分线程中的全局对象不再是window, 所以在分线程中不可能更新界面
}

```

Null与undefined的区别

```
JavaScript的最初版本是这样区分的：null是一个表示"无"的对象，转为数值时为0；undefined是一个表示"无"的原始值，转为数值时为NaN。

目前的用法
null表示"没有对象"，即该处不应该有值。典型用法是：
（1） 作为函数的参数，表示该函数的参数不是对象。

（2） 作为对象原型链的终点。

Object.getPrototypeOf(Object.prototype)
// null

undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。典型用法是：
（1）变量被声明了，但没有赋值时，就等于undefined。

（2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。

（3）对象没有赋值的属性，该属性的值为undefined。

（4）函数没有返回值时，默认返回undefined。

https://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html
```

