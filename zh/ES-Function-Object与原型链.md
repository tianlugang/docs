# Function/Object与原型链

所有对象都来自`Object`，从`Object.prototype`继承方法和属性。所有`Function`都是对象，除过`Function.prototype`比较特殊（是个函数，但同样继承了`Object.prototype`），其他普通函数的原型都是间接或者直接继承自`Object.prototype`的对象。因此，`Function`是`Object`的实例, 并且定义了特殊函数（`Function.prototype`），`Object`同时也是`Function`的实例，并实现了`Object.prototype`(`__proto__ 与 constructor`)，并且`Function`在实例化时又继承了它; 

1. `Function` 对象只有`length`、`name`与`prototype`属性，没有方法，但是因为它本身就是一个函数，所以它也会通过原型链从自己的原型`Function.prototype` 上抄写一些属性和方法，同时又继承了`Object.prototype`生成自己的原型链

2. `Function.length`与`Function.name` 不可覆盖，不可被`for...in`遍历，可以通过`Object.defineProperty`来修改特性

3. `Function.prototype`和一般函数的区别在于：它不可写，不可配置，不可遍历, 即它永远指向一个固定的函数，是js中唯一一个默认`prototype`为函数的对象，其他所有函数本身的`__proto__`都指向它。普通函数的`prototype`是一个带有`constructor`属性的普通对象

4. `Function.prototype`函数上定义的属性与方法，这些将会被所有函数继承。同时`Function.prototype`继承自`Object.prototype`

5. `Object`是一个普通的函数，因此继承自`Function`，获得了`Function.prototype`上的属性与方法。除此之外`Object`还实现了很多私有方法，可以理解为静态方法`（在 ES class 中 static 声明过的方法）`，这些方法将不会被继承

6. `Object.prototype`来自于`Function.prototype`，是不可写、不可配置、不可for...in遍历的，与其他内置的引用类型的原型（`Array.prototype、String.prototype`）一样，但依然可以被扩展，而`Object.prototype`的原型是最顶级原型(`源型`), 因此它有一个重要特性，它是所有对象原型链的终点，因为`Object.prototype.__proto__`的值为 `null`
 
```js
    // 说明 `Function` 是 `Object`的实例
    Function instanceof Object;  // true
    // 说明 `Object` 是 `function`
    Object instanceof Function; // true
    // 说明 `Function.prototype` 是 `Object` 的实例
    Function.prototype instanceof Object; // true
    // `Function`的原型是一个内置的函数
    typeof Function.prototype === 'function'; // true
    // 说明了`Function`从自己的原型上继承或誊写方法到自己的构造函数上
    Function.apply === Function.prototype.apply; // true
    // 因为 `Function.prototype` 本身是一个函数，而且是`Object`的实例,
    // 所以 `Function.prototype` 继承了 `Object.prototype`
    Function.prototype.__proto__ === Object.prototype; // true
    // 而`Function`本身继承了自己的原型函数，拥有了其原型上的方法与属性
    Function.__proto__ === Function.prototype; // true
    
    // `Object`的原型是一个普通对象, 与普通函数的原型一样
    typeof Object.prototype === 'object'; // true
    // 因为，`Object`继承了`Function`的原型
    Object.__proto__ === Function.prototype; // true
    // 所以，`Object`的原型链与自己的原型无关
    Object.prototype === Object.__proto__; // false

    // 所有函数本身的 `__proto__` 都指向 `Function.prototype`
    // 因此，所有函数声明时，继承`Function`的原型
    function F () {}
    F.__proto__ === Function.prototype; // true
    // 说明普通函数的 `prototype` 是一个带有 `constructor` 属性的普通对象
    typeof F.prototype === 'object'; // true

    // `Function`本身提供的属性
    Function.length;
    Function.prototype; 
    Function.name;
    Function.[Symbol.hasInstance]

    // `Function.prototype `上实现的方法与属性，将会被所有普通函数继承
    Function.prototype.apply;
    Function.prototype.bind;
    Function.prototype.call;

    // `Object.prototype ` 上的属性和方法，会被js中的所有方法和对象所继承
    Object.prototype.constructor;
    Object.prototype.hasOwnProperty();
    Object.prototype.isPrototypeOf();
    Object.prototype.propertyIsEnumerable();
    Object.prototype.toLocaleString();
    Object.prototype.toString();
    Object.prototype.valueOf();
    // 原型链的终点，
    // 因此，一个对象的实例，沿着它的原型链，通过__proto__一层层往上找某一个属性，如果在Object.prototype上没找到，那就会返回undefined，所以，原型链不会无限的找下去。
    Object.prototype.__proto__ === null;
    Object.prototype.__proto__; // 出口 null

    // Object 的私有方法
    Object.assign()
    Object.create()
    Object.defineProperties()
    Object.defineProperty()
    Object.entries()
    Object.freeze()
    Object.getOwnPropertyDescriptor()
    Object.getOwnPropertyDescriptors()
    Object.getOwnPropertyNames()
    Object.getOwnPropertySymbols()
    Object.getPrototypeOf()
    Object.is()
    Object.isExtensible()
    Object.isFrozen()
    Object.isSealed()
    Object.keys()
    Object.preventExtensions()
    Object.seal()
    Object.setPrototypeOf()
    Object.values()
```