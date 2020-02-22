# ES6入门学习笔记

## let和const

- `let`只会在命令所在代码块内生效

- 在`for`循环中使用`let`，会在设置循环变量的地方建立一个父作用域（即`for`代码块内依旧可以声明同名变量

- 相比较`var`，`let`不能在声明之前使用

- 只要块级作用域中存在`let`，那么外部的同名变量就被屏蔽，这时候如果在声明前使用，会进入『暂时性死区』，将该变量看做不存在

- 如果存在暂时性死区，那么无论是调用该变量还是使用`typeof`都会抛出`ReferenceError`

- `let`不允许同一变量的重复声明

- 外层块级作用域的变量可以在内层块级作用域被访问

- 块级作用域可以替代匿名立即执行函数表达式（匿名IIFE）实现不污染外部变量的效果

  ```js
  // IIFE 写法
  (function () {
    var tmp = ...;
    ...
  }());
  
  // 块级作用域写法
  {
    let tmp = ...;
    ...
  }
  ```

- 函数可以在块级作用域内声明，但只能在作用域内被调用（ES6），ES5之前的版本会污染外部同名函数

- 块级作用域必须包含括号

  ```javascript
  // 第一种写法，报错
  if (true) let x = 1;
  
  // 第二种写法，不报错
  if (true) {
    let x = 1;
  }
  ```

- `const`的作用域与`let`基本一致

- `const`只能保证变量所指向的内存地址不被改变，因此如果`const`指向了一个对象，那么对象中的内容依旧可以修改，但对象本身不能被覆盖/删除。

  > 如果真的需要冻结对象，应该使用`Object.freeze`方法
  >
  > ```javascript
  > const foo = Object.freeze({});
  > 
  > // 常规模式时，下面一行不起作用；
  > // 严格模式时，该行会报错
  > foo.prop = 123;
  > 
  > // 彻底冻结一个对象及其所有属性
  > var constantize = (obj) => {
  >   Object.freeze(obj);
  >   Object.keys(obj).forEach( (key, i) => {
  >     if ( typeof obj[key] === 'object' ) {
  >       constantize( obj[key] );
  >     }
  >   });
  > };
  > ```

- `const`和`let`不能被`delete`删除，因为两者均属于不可设置属性

- 顶层对象在浏览器和Node中分别指`window`和`global`，在ES5中顶层对象的属性和全局变量是等价的

- ES2020引入了`globalThis`作为顶层对象，这样就可以在任何环境下拿到全局`this`

## 变量的解构赋值

- 

