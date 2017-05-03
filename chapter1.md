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



