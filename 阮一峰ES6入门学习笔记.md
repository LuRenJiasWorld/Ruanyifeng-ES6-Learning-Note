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

- ES6允许对变量进行解构赋值，只要左右两边模式相同就可以一一对应，如果解构失败，失败的值等于`undefined`：

  ```javascript
  let [foo, [[bar], baz]] = [1, [[2], 3]];
  foo // 1
  bar // 2
  baz // 3
  
  let [ , , third] = ["foo", "bar", "baz"];
  third // "baz"
  
  let [x, , y] = [1, 2, 3];
  x // 1
  y // 3
  
  let [head, ...tail] = [1, 2, 3, 4];
  head // 1
  tail // [2, 3, 4]
  
  let [x, y, ...z] = ['a'];
  x // "a"
  y // undefined
  z // []
  ```

- 只要数据结构拥有`Iterator`接口，就都可以使用数组形式的解构赋值

- 解构赋值允许指定默认值，但默认值只有当对应位置为`undefined`（严格等于）才能生效

  ```javascript
  let [foo = true] = [];
  foo // true
  
  let [x, y = 'b'] = ['a']; // x='a', y='b'
  let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
  ```

- 解构赋值的默认值是惰性求值的，只有在用到的时候才会被求值

- 解构赋值的默认值可以引用解构赋值的其他变量（但必须是已经声明的变量）

  ```javascript
  let [x = 1, y = x] = [];     // x=1; y=1
  let [x = 1, y = x] = [2];    // x=2; y=2
  let [x = 1, y = x] = [1, 2]; // x=1; y=2
  let [x = y, y = 1] = [];     // ReferenceError: y is not defined
  ```

- 解构赋值也可以用于对象，但是变量必须与属性同名才能取到相应的值，否则为`undefined`

- 对象解构可以方便的提取现有对象的方法：

  ```javascript
  // 例一
  let { log, sin, cos } = Math;
  
  // 例二
  const { log } = console;
  log('hello') // hello
  ```

- 如果变量名和属性名不一致，需要指定属性名

  ```javascript
  let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
  baz // "aaa"
  
  let obj = { first: 'hello', last: 'world' };
  let { first: f, last: l } = obj;
  f // 'hello'
  l // 'world'
  ```

- 解构也可以用于嵌套的对象

  ```javascript
  let obj = {
    p: [
      'Hello',
      { y: 'World' }
    ]
  };
  
  // 这里的p是模式名，不是变量，不会被赋值
  let { p: [x, { y }] } = obj;
  // 如果要赋值，需要额外加一个p
  let { p, p: [x, { y }] } = obj;
  x // "Hello"
  y // "World"
  ```

- 对象的解构赋值可以取到继承的属性

  ```javascript
  const obj1 = {};
  const obj2 = { foo: 'bar' };
  Object.setPrototypeOf(obj1, obj2);
  
  const { foo } = obj1;
  foo // "bar"
  ```

- 对象的解构也可以指定默认值

- 字符串也可以被解构赋值（因为字符串属于可迭代对象）

- 只要等号右边的值不是对象/数组，就会先将其转换为对象，因此`undefined`和`null`是不能被解构赋值的

- 函数参数也可以被解构赋值

  ```javascript
  function add([x, y]){
    return x + y;
  }
  
  add([1, 2]); // 3
  ```

- 函数参数也可以使用默认值

  ```javascript
  // 情况1（为第一个参数中的x/y指定默认值）
  function move({x = 0, y = 0} = {}) {
    return [x, y];
  }
  
  move({x: 3, y: 8}); // [3, 8]
  move({x: 3}); // [3, 0]
  move({}); // [0, 0]
  move(); // [0, 0]
  
  // 情况2（为move的参数指定默认值，只要参数为undefined，就会触发默认值）
  function move({x, y} = { x: 0, y: 0 }) {
    return [x, y];
  }
  
  move({x: 3, y: 8}); // [3, 8]
  move({x: 3}); // [3, undefined]
  move({}); // [undefined, undefined]
  move(); // [0, 0]
  ```

- 用途

  - 交换变量的值（无需新增变量）
  - 从函数返回多个值
  - 为函数调用增加参数名称
  - 提取JSON中的数据
  - 作为函数参数的默认值
  - 遍历Map结构
  - 导入模块中的指定方法（最常用）

## 字符串的扩展

- 字符串拥有遍历器接口，可以被循环遍历（支持大于`0xFFFF`的码点比如Emoji）

- 模板字符串

  - 使用反引号代替单双引号，可以嵌入模板字符串（可以当做普通字符串、也可以当做多行字符串、还可以插入变量）

    ```javascript
    // 普通字符串
    `In JavaScript '\n' is a line-feed.`
    
    // 多行字符串
    `In JavaScript this is
     not legal.`
    
    console.log(`string text line 1
    string text line 2`);
    
    // 字符串中嵌入变量
    let name = "Bob", time = "today";
    `Hello ${name}, how are you ${time}?`
    ```

  - 如果需要过滤换行符号，可以加一个`trim()`

    ```javascript
    $('#list').html(`
    <ul>
      <li>first</li>
      <li>second</li>
    </ul>
    `.trim());
    ```

  - 嵌入变量需要把变量名加入`${}`里面，括号里面可以放入任意的JavaScript表达式，甚至可以调用函数，但不能调用没声明的变量

  - 模板字符串可以嵌套使用

    ```javascript
    const tmpl = addrs => `
      <table>
      ${addrs.map(addr => `
        <tr><td>${addr.first}</td></tr>
        <tr><td>${addr.last}</td></tr>
      `).join('')}
      </table>
    `;
    ```

  - 模板字符串可以作为函数被调用

    ```javascript
    let func = (name) => `Hello ${name}!`;
    func('Jack') // "Hello Jack!"
    ```

  