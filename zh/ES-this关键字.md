# this 关键字

`this`指的是当前执行代码的环境对象, 在非严格模式下，总是指向一个对象，在严格模式下可以是任意值，在原型链中`this`被绑定为最直接的调用对象

* 在全局执行环境中（在任何函数体外部）`this` 都指向全局对象, `[globalThis](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/globalThis)` 获取全局对象, 无论代码是否在当前上下文运行
* 在函数内部，this的值取决于函数被调用的方式。
  1. 在`use strict`环境中，简单调用时，this被该调用设置为 `undefined`, 反之不被调用设置，指向默认的全局对象
  2. 在函数主体中使用 this 关键字时，可以通过使用该函数继承自 `Function.prototype`的 `call` 或 `apply` 方法将 this 值绑定到调用中的特定对象
  3. 在函数使用时，可以使用其继承自`Function.prototype`的`bind`方法来创建一个与该函数具有相同函数体和作用于的函数，在新函数中this将被永久绑定到`bind`的第一个参数上
  >  `bind()` 函数会创建一个新的绑定函数（bound function，BF）。绑定函数是一个 exotic function object（怪异函数对象，ECMAScript 2015 中的术语），它包装了原函数对象。调用绑定函数通常会导致执行包装函数。
  > 绑定函数具有以下内部属性：
        `[[BoundTargetFunction]]` - 包装的函数对象
        `[[BoundThis]]` - 在调用包装函数时始终作为 this 值传递的值。
        `[[BoundArguments]]` - 列表，在对包装函数做任何调用都会优先用列表元素填充参数列表。
        [[Call]] - 执行与此对象关联的代码。通过函数调用表达式调用。内部方法的参数是一个this值和一个包含通过调用表达式传递给函数的参数的列表。
  > 当调用绑定函数时，它调用 `[[BoundTargetFunction]]` 上的内部方法 `[[Call]]`，就像这样 Call(boundThis, args)。其中，boundThis 是 `[[BoundThis]]`，args 是 `[[BoundArguments]]` 加上通过函数调用传入的参数列表。
  > 绑定函数也可以使用 new 运算符构造，它会表现为目标函数已经被构建完毕了似的。提供的 this 值会被忽略，但前置参数仍会提供给模拟函数。

  4. 在箭头函数中，this与封闭词法环境的this保持一致。在全局代码中，它将被设置为全局对象
  ```js
    /*
        1. 箭头函数 忽略 apply/call 传入的第一个参数，即 this 指针的值，不执行 this调用绑定
        2. 箭头函数 忽略 bind 传入的第一个参数，不执行 this 的绑定，但依然会返回一个新的 bound 函数
        3. 箭头函数 与 严格模式中的 this 规则都被忽略
        4. 箭头函数 不绑定 `arguments`, 因此在其函数体中不存在 arguments
        5. 箭头函数 作为成员方法时，this 指向 上一层作用域
        6. 箭头函数 不能用作构造器， 和new一起用会抛出错误
        7. 箭头函数 没有prototype属性
        8. 箭头函数 不能用作函数生成器，即不能在其函数体中使用 yeild 关键字
    */
  ```
  5. 当函数作为对象的成员时，在使用对象成员调用符`.`或者`[]`调用该函数时，该对象函被绑定到数将的this指针
  6. 在`setter` 与 `getter`函数中都会把 this 绑定到设置或获取属性的对象
  7. 在原型链中`this`被绑定为最直接的调用对象
  8. 函数被作为构造器使用时（使用new关键字），`this`指向正在被构造的新对象
  9. 当代码被内联on-event 处理函数调用时，它的this指向监听器所在的DOM元素