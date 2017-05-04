# 主要架构

```javascript
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
	},
    // ...
    
    jQuery.fn = jQuery.prototype = {
        jquery: version,
        constructor: 
    }

    if ( typeof noGlobal === strundefined ) {
        // 这里解释了为什么在函数中将window作为参数传入，我认为有以下几个原因：
        // 1 保护全局作用域
        // 2 函数内容查找window时，不用再向作用域链往上查找，优化了函数
        // 3 压缩代码后，函数内部的window都被替换为更短的函数名，例如a，减少文件量(虽然很小，但是思想要有)
        window.jQuery = window.$ = jQuery;
    }

    return jQuery;
}))
```

点开jQuery.js我们会发现类似下面的一个立即执行函数，

```javascript
(function (global, factory) {
    // ...
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
   1. 保护全局作用域，不用担u心代码污染全局作用域
   2. 函数在查找window变量时，不用再按照作用域链向上查找，优化了函数
   3. 压缩代码后，函数内部的变量window可以被替换为更短的变量名


      ```javascript
      (function(a, b) {...}(window, function(window, noGlobal) {...}))
      ```
2. 既然所有内容都写在匿名函数内部，那我们是怎么直接调用$\(...\)？  
   我们将代码拉到最下面，会看到在工厂函数中包含下面代码：





本章知识点:

1. 立即执行函数
2. undefined在某些浏览器中可能会被改变



