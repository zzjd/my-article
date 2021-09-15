# 异步

- 为什么 JS 是单线程的？
  - JS 的单线程，与它的用途有关，JS 作为浏览器的脚本语言，主要作用是实现用户与浏览器的交互，以及操作 DOM。如果被设计成多线程，那么如果有一个线程需要修改一个 DOM 元素，而另一个线程要删除改 DOM 元素，浏览器会一脸懵。
  - 为了利用多核 CPU 的计算能力，HTML5 提出了 Web Woker 标准，允许 JS 创建多个线程，但是子线程完全由主线程控制，而且不能操作 DOM，本质上还是单线程。

* 线程与进程

  - 进程就像一个公司，每个公司都有自己的资源可以调度；公司之间是相互独立的；而线程就是公司中的每个员工，多个员工一起合作，完成任务，公司可以有一名员工或多个，员工之间共享公司的空间。线程粒度更小（线）。

* JS 异步执行过程
  - 主任务队列（先进先出）,setTimeout 将自己里面的函数放入异步队列中,前面的任务执行完成后，事件循环发现异步队列中有任务，将它添加到主任务队列中执行。

# 闭包

- 我们通常所用的闭包是函数嵌套函数的形式,内部的那个函数通过 return 出来,然后内部的函数又保持对上层作用域的引用,而且内部函数还必须要在外部调用,这个时候整个结构,内部外部结构,整个就形成了闭包

```javascript
function f1() {
  var a = 1;
  function f2() {
    return (a = a + 1);
  }
  return f2;
}

var n = f1(); //n指向返回的函数f2
n(); //2
n(); //3
n(); //4
```

- f2 就是闭包，不仅可以访问和修改函数内部变量，还能使其一直存在内存中，不被回收。

## 闭包应用

### 闭包的优点

- 在函数外读取函数内部的变量(避免全局变量的污染)
- 让局部变量的值能够被保存下来(可以让变量常驻内存)
- 将模块的公有属性和方法暴露出来，可以看下面这个例子：

```javascript
var moduleA = function () {
  var num = 100; //私有属性
  var rem = 200; //公有属性
  function add() {
    //公有方法
    num++;
    console.log(num);
  }
  function divide() {
    //公有方法
    num = num / 10;
    console.log(num);
  }
  function show() {
    //私有方法
    console.log(num);
  }
  //将需要暴露出去的属性和方法return出去
  return {
    add: add,
    divide: divide,
    rem: rem,
  };
};

moduleB.add(); //101
moduleB.divide(); //10.1
moduleB.show(); //moduleB.show is not a function
console.log(moduleB.rem); //200
```
