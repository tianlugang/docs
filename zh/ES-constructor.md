# constructor
是一种用于创建和初始化类创建的对象的特殊方法, 每个 `EcmaScript` 函数实际上都是一个`Function`对象。

```js
    (function(){}).constructor === Function // true 便可以得到这个结论。
```

## 对象与 constructor
`值类型`的对象的`constructor`不能被修改，`引用类型`的对象的 `constructor`可以被修改，但`instanceof`运算结果为`false`
```js
    function T (){}
    var d = new Boolean();
    d.constructor = T;

    console.log(d instanceof Boolean);//true
    console.log(d instanceof T);// false
```

## 函数与 constructor 与原型链
1. `constructor`是一种特殊的函数
2. 定义的函数的`constructor`总是指向`Function`，说明函数在声明的过程中继承了`Function`对象的`prototype`, 同时定义了自己的`prototype`对象，这个`prototype`继承了`Object.prototype`，因此`function.__proto__.__proto__`就是`Object.prototype`, 而`Object.prototype.__proto__`为`null`
