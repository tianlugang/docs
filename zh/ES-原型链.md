# 关于原型链

`原型链`的定义: 通过对象初始化得到的实例对象`object`都有一个私有属性`__proto__`指向它的构造函数的原型对象`prototype`, 该原型对象也有一个自己的原型对象(`__proto__`), 层层向上直到一个对象的原型对象为`null`, 这个链称之为`原型链`。根据定义,`null`没有原型, 并作为原型链中的最后一个环节

---

- 当访问对象的一个属性或方法时，会先在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾，下文会说明其原理

- 原型链的遮蔽性，优先访问自身属性，其次是原型属性，当原型上存在同名属性时，直接返回自身属性，忽略原型属性

- 在原型链上查找属性比较耗时，对性能有副作用，即试图访问不存在的属性时会遍历整个原型链, 因此可以使用 `Object.prototype.hasOwnProperty`进行检测，阻止不必要的查找

```js
    function Obj(){
        this.a = 1;
    }
    let obj = new Obj();  // {a: 1}

    if (obj.hasOwnProperty('b')){
        // 在 obj 自身上找到 b 时
        console.log(obj.b);
    }

    // 让Obj的有自身属性 a，同时也有原型属性 a 与 b
    // 在 Obj 函数的原型上定义属性
    // 不要在 Obj 函数的原型上直接定义 Obj.prototype = {a:2,b:3}; 这样会直接打破原型链
    Obj.prototype.a = 2;
    Obj.prototype.b = 3;  // obj.__proto__ 为 {a:2,b:3}

    // 在执行 obj.a 的时候先查找自身属性，找了 a 属性即可停止，直接忽略了 原型链上的 a 属性，
    // 这种情况被称为"属性遮蔽 (property shadowing)"
    console.log('obj.a =>',obj.a); // 1

    // 在执行 obj.b 的时候会先查找自身属性，未找到 b 属性，则继续在原型链（obj.__proto__）上查找
    // 而 obj.__proto__ 继承自 Obj.prototype，因此可以访问到 Obj.prototype.b，停止了查找;
    console.log('obj.b =>',obj.b); // 3

    // 执行 obj.c 的时候先在自身属性中查找，没有找到，就在原型链 `obj.__proto__` 上查找，
    // 同样没有未找到，因此继续在`obj.__proto__.__proto__`上查找，
    // 同样没有未找到，而`obj.__proto__.__proto__` 是 `Object.prototype`的实例，`Object.prototype.__proto__`为null，因此在`obj.__proto__.__proto__`这一层就停止了查找，返回了 undefined
    console.log('obj.c =>', obj.c); // undefined

    // 综上所述，整个原型链如下: 
    // {a:1} ---> {a:2, b:43} ---> Object.prototype ---> null
    // obj.__proto__.__proto__.__proto__ 
```