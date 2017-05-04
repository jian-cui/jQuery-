# 主要架构

```js
(function (global, factory) {
    // 在这个函数内部，global即window, factory即真正的jQuery函数
    if ( typeof module === "object" && typeof module.exports === "object" ) {
        // 如果是在commonJS或类commonJS的环境中，则通过module.exports的方式将factory暴露出去
        // For CommonJS and CommonJS-like environments where a proper window is present,
        // execute the factory and get jQuery
        // For environments that do not inherently posses a window with a document
        // (such as Node.js), expose a jQuery-making factory as module.exports
        // This accentuates the need for the creation of a real window
        // e.g. var jQuery = require("jquery")(window);
        // See ticket #14549 for more info
        module.exports = global.document ?
            factory( global, true ) :
            function( w ) {
                if ( !w.document ) {
                    throw new Error( "jQuery requires a window with a document" );
                }
                return factory( w );
            };
    } else {
        // 如果不是在commonJS环境中，则直接调用函数
        factory( global );
    }
}(typeof window !== "undefined" ? window : this, function(window, noGlobal) {
    // 真正的代码在这里

    // ...
    var
    version = "1.11.0",

    // Define a local copy of jQuery
    jQuery = function( selector, context ) {
        // The jQuery object is actually just the init constructor 'enhanced'
        // Need init if jQuery is called (just allow error to be thrown if not included)
        return new jQuery.fn.init( selector, context );
    };
    // ...

    jQuery.fn = jQuery.prototype = {
        jquery: version,
        constructor: jQuery,
        ...
    };

    var init = jQuery.fn.init = function (selector, context) {
        ...
    };

    init.prototype = jQuery.fn;

    if ( typeof noGlobal === strundefined ) {
        // 这里解释了为什么在函数中将window作为参数传入，我认为有以下几个原因：
        // 1 保护全局作用域
        // 2 函数内容查找window时，不用再向作用域链往上查找，优化了函数
        // 3 压缩代码后，函数内部的window都被替换为更短的函数名，例如a，减少文件量(虽然很小，但是思想要有)
        window.jQuery = window.$ = jQuery;
    };

    return jQuery;
}))
```

## 立即执行函数

点开jQuery.js我们会发现类似下面的一个立即执行函数，

```js
(function (global, factory) {
    // 骨架函数
}(window, function(window, noGlobal) {
    // 工厂函数
}))
```

除了上面这种方式还有一种方式：

```javascript
(function (window, undefined) {
    // 函数体
})(window)
```

关键点：

1. 为什么将window作为参数传入函数中？
   1. **保护全局作用域**，不用担心代码污染全局作用域，减少变量冲突
   2. 函数在查找window变量时，**不用再按照作用域链向上查找**，优化了函数
   3. **压缩代码后**，函数内部的变量window可以被替换为更短的变量名
      ```js
      (function(a, b) {...}(window, function(window, noGlobal) {...}))
      ```
2. 既然所有内容都写在匿名函数内部，那我们是怎么直接调用$\(...\)？  
   我们将代码拉到最下面，会看到在工厂函数中包含下面代码：

   ```js
   window.jQuery = window.$ = jQuery;   // 整个函数只暴露除了$和jQuery给全局作用域，避免了变量重试
   ```

3. 第二种匿名函数方式中为什么要传入undefined?  
   在部分浏览器中undefined是可以**被赋值改变**的，这里传入undefined，保证函数内部的undefined是正确的  
   `undefined = 2;`

## commonJS处理

在骨架函数内部，jQuery实现了commonJS模块化定义，主要是通过判断当前运行环境中有没有**module**来实现

```javascript
if ( typeof module === "object" && typeof module.exports === "object" ) { 
        module.exports = global.document ?
            factory( global, true ) :
            function( w ) {
                if ( !w.document ) {
                    throw new Error( "jQuery requires a window with a document" );
                }
                return factory( w );
            };
    } else {
        factory( global );
    }
```

## 无new构建

使用jQuery时，并没有使用new关键字进行实例化。但是实际上，我们在$\('...'\)是，jQuery内部已经替我们做好了对象的实例化

> 本章知识点:
>
> 1. 立即执行函数
>
> 2. undefined在某些浏览器中可能会被改变



