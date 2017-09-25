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

