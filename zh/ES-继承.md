# ES6 继承
谈到`继承`就会涉及到`class`,`extends`,`super`,`constructor`,`this`,`new`等

## extends 关键词
首先 `extends` 关键词是语法糖，其次它实现了语法语义上的继承，而且它必然绑定`class`使用，在创建一个普通类或者内建对象的子类时继承的`prototype`必须是一个Object 或者 null。

```js
    class ChildClass extends ParentClass {  }
    class NullClass extends null {}
```

## super 关键词
`super`用于访问和调用一个对象的父对象上的成员,而且只能在类的内部或者函数内部使用，通过调用`super`我们可以父类的构造函数传参，当类中不存在`constructor`时，我们无效调用`super`

```js
    var obj = {
        abs(){
            console.log('obj.abs');
        }
    } 
    var sub = {
        call(){
            super.abs();
        }
    } 
    Object.setPrototypeOf(sub, obj); 
    sub.call(); // obj.abs
    sub.abs();  // obj.abs
```
上面的案例就是普通意义上的原型继承，一般情况下我们不会这样使用，更多的时候，我们结合`class`来使用`super`,可以通过`super`可以访问父类的成员

## class 关键词
* `class`顾名思义，它的诞生是为了让我们更加舒服的使用面向对象的`ecmascript`, 它是用来声明一个用户自定义类型的类，其本质上来说还是声明了一个`Function`, 这一点我们可以使用`typeof`操作符来确定
* 虽然`class`声明的类，本质上还是`Function`，但是它和常规意义上的函数存在一些区别,即`class`声明的类，只能通过`new`操作符进行实例化

```js
    class Super {
        static parentStaticProperty='this is parent static property.';
        constructor(){
            this.parentOwnProperty = 'this is parent own property.';
            this.parentOwnMethod = function (){
                console.log('this is parent own method.');
            }
        }
        parentPrototypeProperty='this parent proptype property.';
        parentMethod(){
            console.log('i am Super method.');
        }
    }
    class Subject extends Super {
        constructor(){
            super();
            this.subOwnProperty ='i am own property.';
            this.subOwnMethod = function (){
                console.log('i am own method.');
            };
        }
        subProperty='i am property';
        subMethod (){
            console.log('i am sub method.');
            super.parentOwnMethod();
            super.parentMethod()
        }
    }
    var sup = new Super();
    var sub = new Subject(); 

    sup.parentOwnMethod === sub.parentOwnMethod // false
    sup.parentMethod === sub.parentMethod // true
    
    // 下面时得到的 sub 对象
    // parentOwnMethod: ƒ ()
    // parentOwnProperty: "this is parent own property."
    // parentPrototypeProperty: "this parent proptype property."
    // subOwnMethod: ƒ () 
    // subOwnProperty: "i am own property."
    // subProperty: "i am property"
    // __proto__: Super
    //     constructor: class Subject
    //         prototype: Super {constructor: ƒ, subMethod: ƒ}
    //     subMethod: ƒ subMethod()
    //     __proto__: 
    //         constructor: class Super
    //             parentStaticProperty: "this is parent static property."
    //             prototype: {constructor: ƒ, parentMethod: ƒ} 
    //         parentMethod: ƒ parentMethod()
    //         __proto__: Object
    //             constructor: ƒ Object()
    //             hasOwnProperty: ƒ hasOwnProperty()
    //             isPrototypeOf: ƒ isPrototypeOf()
    //             propertyIsEnumerable: ƒ propertyIsEnumerable()
    //             toLocaleString: ƒ toLocaleString()
    //             toString: ƒ toString()
    //             valueOf: ƒ valueOf()
    //             __defineGetter__: ƒ __defineGetter__()
    //             __defineSetter__: ƒ __defineSetter__()
    //             __lookupGetter__: ƒ __lookupGetter__()
    //             __lookupSetter__: ƒ __lookupSetter__()
    //             get __proto__: ƒ __proto__()
    //             set __proto__: ƒ __proto__()
```

上面的例子，我们可以看到，子类`Subject`继承了父类`Super`,通过简单分析可以看出父类的自身属性或原型属性通过`extends`和`new`时，变成了实例`sub`的自身属性，原型上的常量属性也被复制了过去，确保了每个实例拥有一份，同时父类的自身方法也被复制到了`sub`自身，但是在原型之后的方法还是共享一份，因此我们在开发过程中，尽可能的少定义自身方法

---
通过上面的例子，可以分析得到如下结论：
1. `extends`关键词在`class`类的继承中的作用
   - 拷贝自身属性和方法到子类的实例对象自身
   - 父类的原型上的方法被链接到子类原型链上
2. 通过如上分析可以得出`extends`的本质是一种组合继承，分为两步
   - 创建子类的`super`，让其指向父类的
   - 将父类的原型设置给子类原型上
3. 既然是语法糖，那么可以使用`es`来模拟,
   - 获取到子类的原型并调用它，传入子类的`this`
   - 通过`Object.create`创建一个对象，用这个对象来覆盖子类的原型，将子类的`constructor`设置为子类的构造函数
```js
    function _getPrototypeOf(o) {
        _getPrototypeOf = Object.setPrototypeOf ? Object.getPrototypeOf : function _getPrototypeOf(o) {
            return o.__proto__ || Object.getPrototypeOf(o);
        };
        return _getPrototypeOf(o);
    }
    function createSuper(Derived) {
        return function () {
            var Super = _getPrototypeOf(Derived),
                call = Super.apply(this, arguments);

            if (call && (typeof call === 'object' || typeof call === 'function')) {
                return call;
            }

            if (this === void 0) {
                throw new ReferenceError("this hasn't been initialised - super() hasn't been called");
            }

            return this;
        };
    }
    
    function _setPrototypeOf(o, p) {
        _setPrototypeOf = Object.setPrototypeOf || function _setPrototypeOf(o, p) {
            o.__proto__ = p;
            return o;
        };

        return _setPrototypeOf(o, p);
    }
    function inherits(subClass, superClass) {
        if (typeof superClass !== "function" && superClass !== null) {
            throw new TypeError("Super expression must either be null or a function");
        }

        subClass.prototype = Object.create(superClass && superClass.prototype, {
            constructor: {
                value: subClass,
                writable: true,
                configurable: true
            }
        });

        if (superClass) _setPrototypeOf(subClass, superClass);
    }
```
