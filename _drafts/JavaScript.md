# JavaScript

## 函数

```javascript
function abs(x){
    if(x >= 0){
        return x;
    }else{
        return -x;
    }
}
```

如果函数没有return语句，那么函数执行完毕后也会返回undefined。

注意JS引擎在行末自动添加分号。

函数也是一个对象，因此变量也可以指向一个函数。

```javascript
var abs = function(x){
  	if(x >= 0){
      	return x;
  	} else {
      	return -x;
  	}
}
```

JS允许传入任意个参数而不影响调用，因此传入的参数比定义多也没有关系，虽然内部函数并不需要这些参数。多余的参数会被放在**rest**参数里

传入参数少，会变为NaN。可以使用typeof进行检查。

JS有一个免费赠送的关键字**arguments**，表示函数内部的所有参数。

### 闭包 closure

高阶函数除了可以接收函数作为参数外，还可以把函数作为结果值返回。

比如不需要立刻求和，可以不返回求和的结果，而是返回求和的函数。

```javascript
//注意，当我们调用lazy_sum的时候，每次调用都会返回一个新的函数，即使传入相同的参数
function lazy_sum(arr) {
    var sum = function () {
        return arr.reduce(function (x, y) {
            return x + y;
        });
    }
    return sum;
}
```

另外，一个函数返回了一个函数后，其内部的局部变量还会被新函数引用。

**返回函数不要引用任何循环变量，或后续会发生变化的变量**。

```javascript
function count() {
    var arr = [];
    for (var i=1; i<=3; i++) {
        arr.push(function () {
            return i * i;
        });
    }
    return arr;
}

var results = count();
var f1 = results[0];
var f2 = results[1];
var f3 = results[2];
f1(); // 16
f2(); // 16
f3(); // 16

//TIPS：创建一个匿名函数并立刻执行：
(function (x) {
    return x * x;
})(3); // 9
```



### map

map()方法定义在JS的Array中，我们调用Array的map()方法，传入自己的函数，就得到了一个新的Array作为结果

```javascript
function pow(x) {
    return x * x;
}

var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
```

### reduce

同样，需要传入两个参数，将array输出成一个结果。

### filter

用于把Array的某些元素过滤掉，然后返回剩下的元素。

### sort

sort会直接对Array进行修改，它返回的结果仍是当前Array

## 变量

不在任何函数内定义的变量就具有全局作用域。

实际上，JS默认有一个全局对象window，全局作用域的变量实际上被绑定到window的一个属性。

同样的，可以修改window.alert()等等各种方法。

```javascript
'use strict';

function foo(){
    alert('foo');
}

foo();
window.foo();
```

### 命名空间

全局变量会绑定到window上，故而不同的JS文件如果使用了相同的全局变量，或者定义了相同名字的顶层函数，都会造成命名冲突，并且很难被发现。

减少冲突的一个方法是把自己的所有变量和函数全部绑定到一个全局变量中。

```javascript
var MYAPP = {};

MYAPP.name = 'name';
MYAPP.version = 1.0;

MYAPP.foo = function(){
    return 'foo';
}
```

> jQuery , YUI , underscore就是这么干的



### 局部作用域

let可以申明一个块级作用域的变量。

### 常量

const

const与let都具有块级作用域

