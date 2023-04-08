---
title: this指向和闭包
date: 2023-03-20 23:14:33
tags:
- 闭包
- this指向
categories:
- [JavaScript]
---

<center><font size=6>js学习</font></center>

### this指向

在普通函数中和箭头函数中this指向会有所不同。

在普通函数中this指的是**调用函数的作用域**，而在箭头函数中没有自己的this，它的this指向**上一级作用域的this**。

1.普通函数

谁调用指向谁

```js
// 这里是a调用了say函数，this指向a
    const a={
      age: 12,
      say: function(){
        console.log(this);
      }
    }
    a.say();
```

独立调用指向window

```js
const a={
    age: 12,
    say: function(){
        console.log(this); //a
        function b(){
            console.log(this); //window
        }
        b(); //独立调用了b
    },
}
a.say();
```

2.箭头函数

```js
// 箭头函数this指向上一级作用域的this
// 对象没有作用域 say的上一级是对象o o没有作用域只能指向window
    const o={
      age: 12,
      say:()=>{
        console.log(this); //window
      }
    }
    o.say();
```

```js
function foo() {
      console.log(this); //指向obj foo由obj调用 指向obj
      test=()=>{
        console.log(this)  //指向obj 上一级作用域的this 即foo作用域中的this
      }
      test()
    }
    let obj = {
      a: 1,
      foo: foo
    }
obj.foo()
```



### 闭包

闭包指的是**引用了另一个函数作用域中的变量的函数**。

```js
function x(name){
 // 这里闭包应该指的是内部的匿名函数，a[name]和b[name]引用了外部函数的作用域变量name
  return function(a,b) {
    return a[name]-b[name];
  }
}

function makeFunc() {
    var name = 'Mozilla'
    function displayName() {
      alert(name)
    }
    return displayName
  }

  var myFunc = makeFunc()
  myFunc() //通过return就可以使用到函数内部的变量
```
