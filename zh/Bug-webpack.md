# webpack使用记录

## 报错

1. `Uncaught TypeError: __webpack_require__(...) is not a function`

   ```js
      // 报错描述
      /*
        Uncaught TypeError: __webpack_require__(...) is not a function
            at eval (VM125 es6.regexp.match.js:6)
            at Object../node_modules/core-js/modules/es6.regexp.match.js (vue.js:1397)
            at __webpack_require__ (vue.js:21)
            at eval (VM124 es6.function.name.js:3)
            at Object../node_modules/core-js/modules/es6.function.name.js (vue.js:1241)
            at __webpack_require__ (vue.js:21)
            at eval (VM132 _export.js:3)
            at Object../node_modules/core-js/modules/_export.js (vue.js:305)
            at __webpack_require__ (vue.js:21)
            at eval (VM131 es6.object.set-prototype-of.js:4)
      */

    // 解决办法：统一模块化标准
   ```