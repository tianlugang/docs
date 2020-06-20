# get 
这是一个 `ecmascript` 内置的语法， 即将对象属性绑定到查询该属性时将被调用的函数，且允许属性名是一个表达式动态计算的值
```js
   // 语法
   // {get prop() { ... } }
   // {get [expression]() { ... } }
```


* 可以使用数值或字符串作为标识

* [必须不带参数](http://whereswalden.com/2010/08/22/incompatible-es5-change-literal-getter-and-setter-functions-must-now-have-exactly-zero-or-one-arguments/)

* 它不能与另一个 get 或具有相同属性的数据条目同时出现在一个对象字面量中
```js
    var obj = { get p() { }, get p() { } } 
    var obj2 = { p: '', get p() { } }
    // 上面都属于错误语法
```
* 可以使用 `delete` 关键字删除 `getter`
* 可以使用 `Object.defineProperty` 或 `Object.defineProperties` 函数为已有的对象定义 `getter` , 使用`get`语法时属性将被定义到实例的原型上，而使用`Object.defineProperty`会将属性定义到实例自身上

```js
    class Example {
        get hello() {
            return 'world';
        }
        get lazy (){
            delete this.lazy;
            return this.lazy = 'getter lazy! Only Computed one.';
        }
    }

    const obj = new Example();

    console.log(obj.hello); // "world"
    console.log(Object.getOwnPropertyDescriptor(obj, 'hello')); // undefined

    console.log(
        Object.getOwnPropertyDescriptor(
            Object.getPrototypeOf(obj), 'hello'
        )
    ); // { configurable: true, enumerable: false, get: function get hello() { return 'world'; }, set: undefined }

    console.log(this.lazy); 
```

---

* `ie9+`支持
* `ie9+`, `Safari`, `Safari on iOS`不支持属性名表达式计算
* 在属性懒处理时，对于Firefox，可以参阅定义[defineLazyGetter()](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/XPCOMUtils.jsm#defineLazyGetter())函数的`XPCOMUtils.jsm`代码模块。