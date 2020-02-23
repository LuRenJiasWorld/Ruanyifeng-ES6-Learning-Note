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

## 字符串的新增方法

- `includes()`：是否包含参数字符串

- `startsWith()`：是否在原字符串的头部

- `endsWith()`：是否在原字符串的尾部

  > 这三个方法都支持使用第二个参数表示开始搜索的位置，其中endsWith是针对前n个字符，而其他两个方法针对从n到结束

  ```javascript
  let s = 'Hello world!';
  
  s.startsWith('world', 6) // true
  s.endsWith('Hello', 5) // true
  s.includes('Hello', 6) // false
  ```

- `repeat()`表示将原字符串重复n次

- `padStart()`和`padEnd()`表示在头部或尾部使用指定字符串补全

  ```javascript
  'x'.padStart(5, 'ab') // 'ababx'
  'x'.padStart(4, 'ab') // 'abax'
  
  'x'.padEnd(5, 'ab') // 'xabab'
  'x'.padEnd(4, 'ab') // 'xaba'
  ```

  > 最常见的用途是为指定的数值补全指定位数，或是提示字符串格式
  >
  > ```javascript
  > '1'.padStart(10, '0') // "0000000001"
  > '12'.padStart(10, '0') // "0000000012"
  > '123456'.padStart(10, '0') // "0000123456"
  > 
  > '12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
  > '09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
  > ```

- `ES2019`新增了`trimStart()`和`trimEnd()`两个方法，和`trim()`一致，消除头部和尾部的空格，不会修改原始字符串

## 正则的扩展

- 正则表达式的初始化

  ```javascript
  var regex = new RegExp('xyz', 'i');
  var regex = /xyz/i;
  var regex = new RegExp(/xyz/i);
  new RegExp(/abc/ig, 'i') // ES6引入，ig被覆盖为i
  ```

- 字符串的正则方法

  - `match()`
  - `replace()`
  - `search()`
  - `split()`

- 正则表达式的`u`修饰符（ES6引入），用于扩展到所有Unicode字符

  - 包含双Unicode码字符（如Emoji）
  - 包含\u{0xFFFF}以上的Unicode码
  - 包含同字但不同Unicode码的情况（如K可以使用`\u{004B}`或`\u{212A}`表示）

  ```javascript
  /^.$/u.test("😂")
  true
  /^.$/.test("😂")
  false
  ```

- 正则表达式的`y`修饰符（ES6引入），要求这一次匹配必须从上一次匹配的头部开始（粘连/sticky）

- ES6引入了正则表达式的`flags`属性，可以返回当前所设置的所有修饰符

- ES2018引入了`s`修饰符，使得点号可以匹配任意单个字符，可以使用`dotAll`属性进行访问

  ```javascript
  const re = /foo.bar/s;
  // 另一种写法
  // const re = new RegExp('foo.bar', 's');
  
  re.test('foo\nbar') // true
  re.dotAll // true
  re.flags // 's'
  ```

- 先行断言和后行断言

  ```javascript
  // 先行断言
  /\d+(?=%)/.exec('100% of US presidents have been male')  // ["100"]
  /\d+(?!%)/.exec('that’s all 44 of them')                 // ["44"]
  
  // 后行断言
  /(?<=\$)\d+/.exec('Benjamin Franklin is on the $100 bill')  // ["100"]
  /(?<!\$)\d+/.exec('it’s is worth about €90')                // ["90"]
  ```

- Unicode属性类（匹配某一类的所有Unicode字符）

  ```javascript
  const regexGreekSymbol = /\p{Script=Greek}/u;
  regexGreekSymbol.test('π') // true
  
  const regex = /^\p{Decimal_Number}+$/u;
  regex.test('𝟏𝟐𝟑𝟜𝟝𝟞𝟩𝟪𝟫𝟬𝟭𝟮𝟯𝟺𝟻𝟼') // true
  
  const regex = /^\p{Number}+$/u;
  regex.test('²³¹¼½¾') // true
  regex.test('㉛㉜㉝') // true
  regex.test('ⅠⅡⅢⅣⅤⅥⅦⅧⅨⅩⅪⅫ') // true
  
  // 匹配所有空格
  \p{White_Space}
  
  // 匹配各种文字的所有字母，等同于 Unicode 版的 \w
  [\p{Alphabetic}\p{Mark}\p{Decimal_Number}\p{Connector_Punctuation}\p{Join_Control}]
  
  // 匹配各种文字的所有非字母的字符，等同于 Unicode 版的 \W
  [^\p{Alphabetic}\p{Mark}\p{Decimal_Number}\p{Connector_Punctuation}\p{Join_Control}]
  
  // 匹配 Emoji
  /\p{Emoji_Modifier_Base}\p{Emoji_Modifier}?|\p{Emoji_Presentation}|\p{Emoji}\uFE0F/gu
  
  // 匹配所有的箭头字符
  const regexArrows = /^\p{Block=Arrows}+$/u;
  regexArrows.test('←↑→↓↔↕↖↗↘↙⇏⇐⇑⇒⇓⇔⇕⇖⇗⇘⇙⇧⇩') // true
  ```

- 组匹配和具名组匹配

  - 使用括号注明捕获组

    ```javascript
    const RE_DATE = /(\d{4})-(\d{2})-(\d{2})/;
    
    const matchObj = RE_DATE.exec('1999-12-31');
    const year = matchObj[1]; // 1999
    const month = matchObj[2]; // 12
    const day = matchObj[3]; // 31
    ```

  - ES2018引入具名组匹配，允许给匹配组命名

    ```javascript
    const RE_DATE = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;
    
    const matchObj = RE_DATE.exec('1999-12-31');
    const year = matchObj.groups.year; // 1999
    const month = matchObj.groups.month; // 12
    const day = matchObj.groups.day; // 31
    ```

  - 可以将具名组匹配和解构赋值相结合

    ```javascript
    let {groups: {one, two}} = /^(?<one>.*):(?<two>.*)$/u.exec('foo:bar');
    one  // foo
    two  // bar
    ```

  - 还可以与字符串替换结合

    ```javascript
    '2015-01-02'.replace(re, '$<day>/$<month>/$<year>')
    // '02/01/2015'
    ```

- 可以引用前面的某个普通组/具名组

  ```javascript
  const RE_TWICE = /^(?<word>[a-z]+)!\k<word>$/;
  RE_TWICE.test('abc!abc') // true
  RE_TWICE.test('abc!ab') // false
  
  // 数字引用也是有效的
  const RE_TWICE = /^(?<word>[a-z]+)!\k<word>!\1$/;
  RE_TWICE.test('abc!abc!abc') // true
  RE_TWICE.test('abc!abc!ab') // false
  ```

- 可以通过`exec()`返回后的`indices`属性拿到每个匹配的开始和结束位置

  ```javascript
  const text = 'zabbcdef';
  const re = /ab+(cd)/;
  const result = re.exec(text);
  
  result.indices // [ [ 1, 6 ], [ 4, 6 ] ]
  ```

- 还可以通过`indices`属性的`groups`属性拿到一个包含具名组信息和开始/结束位置的对象

  ```javascript
  const text = 'zabbcdef';
  const re = /ab+(?<Z>cd)/;
  const result = re.exec(text);
  
  result.indices.groups // { Z: [ 4, 6 ] }
  ```

- ES2020引入了`String.prototype.matchAll()`方法，可以一次性取出所有匹配，但会一个遍历器

  ```javascript
  const string = 'test1test2test3';
  
  // g 修饰符加不加都可以
  const regex = /t(e)(st(\d?))/g;
  
  for (const match of string.matchAll(regex)) {
    console.log(match);
  }
  // ["test1", "e", "st1", "1", index: 0, input: "test1test2test3"]
  // ["test2", "e", "st2", "2", index: 5, input: "test1test2test3"]
  // ["test3", "e", "st3", "3", index: 10, input: "test1test2test3"]
  ```

- 遍历器转为数组的方式

  ```javascript
  [...string.matchAll(regex)]
  Array.from(string.matchAll(regex))
  ```

## 数值的扩展

- ES6规范了二进制/八进制分别用`0b`和`0o`表示

- 使用`Number()`构造函数将不同进制的数字规范为十进制

- ES6在Number对象上新增了`Number.isFinite()`和`Number.isNaN()`两个方法，相比较全局的同名方法，Number的这两个方法

- ES6将`parseInt()`和`parseFloat()`移植到了Number对象中，使得语言逐步模块化

  ```javascript
  Number.parseInt === parseInt // true
  Number.parseFloat === parseFloat // true
  ```

- `Number.isInteger()`可以用来判断一个数值是否为整数，但是对于`20.0`也会返回True（因为JavaScript内对浮点数和整数的存储方法相同）

  > 因为JavaScript内部使用64位双精度存储数字，因此该方法可能会存在误判：
  >
  > ```javascript
  > Number.parseInt === parseInt // true
  > Number.parseFloat === parseFloat // true
  > ```

- `Number.EPSILON`表示最小精度的数字，用于为浮点数计算设置一个误差范围

  ```javascript
  0.1 + 0.2
  // 0.30000000000000004
  
  0.1 + 0.2 - 0.3
  // 5.551115123125783e-17
  
  5.551115123125783e-17.toFixed(20)
  // '0.00000000000000005551'
  
  0.1 + 0.2 === 0.3 // false
  
  function withinErrorMargin (left, right) {
    return Math.abs(left - right) < Number.EPSILON * Math.pow(2, 2);
  }
  
  0.1 + 0.2 === 0.3 // false
  withinErrorMargin(0.1 + 0.2, 0.3) // true
  
  1.1 + 1.3 === 2.4 // false
  withinErrorMargin(1.1 + 1.3, 2.4) // true
  ```

- `Number.isSafeInteger()`属性用于判断是否存在溢出情况

- `Math.trunc()`方法用于去除小数部分，返回整数部分，是`Math.ceil()`和`Math.floor()`的结合

  > ```javascript
  > Math.trunc = Math.trunc || function(x) {
  >   return x < 0 ? Math.ceil(x) : Math.floor(x);
  > };
  > ```

- `Math.sign()`可以用于判断一个数到底是正数、负数还是0

- `Math.cbrt()`可以用于计算一个数的立方根

- `Math.clz32()`可以用于计算一个32位无符号**整数**内存在多少个前导0

  > ```javascript
  > Math.clz32(0) // 32
  > Math.clz32(1) // 31
  > Math.clz32(1000) // 22
  > Math.clz32(0b01000000000000000000000000000000) // 1
  > Math.clz32(0b00100000000000000000000000000000) // 2
  > ```

- `Math.log10()`可以返回10为底的x的对数

- `Math.log2()`可以返回以2为底的x的对数

- ES2016引入了指数运算符（`**`），但是和其他语言不一样，它是右结合的

  > ```javascript
  > // 相当于 2 ** (3 ** 2)
  > 2 ** 3 ** 2
  > // 512
  > 
  > a **= 2;
  > // 相当于a = a * a
  > ```

- ES2020引入了一个新的数据类型`BigInt`来解决这个问题，可以存储任何位数的整数，在声明字面量时添加后缀n来声明，也可以通过`BitInt()`构造方法将其他类型的值转换为`BitInt`

## 函数的扩展

- ES6之后函数参数可以引入默认值：

  ```javascript
  function log(x, y = 'World') {
    console.log(x, y);
  }
  
  log('Hello') // Hello World
  log('Hello', 'China') // Hello China
  log('Hello', '') // Hello
  ```

- 使用参数默认值时，函数不能有同名参数

- 可以与解构赋值的默认值结合使用

  ```javascript
  function foo({x, y = 5} = {}) {
    console.log(x, y);
  }
  
  foo() // undefined 5
  ```

- 区分默认值两种写法：

  ```javascript
  // 写法一
  function m1({x = 0, y = 0} = {}) {
    return [x, y];
  }
  
  // 写法二
  function m2({x, y} = { x: 0, y: 0 }) {
    return [x, y];
  }
  
  // 函数没有参数的情况
  m1() // [0, 0]
  m2() // [0, 0]
  
  // x 和 y 都有值的情况
  m1({x: 3, y: 8}) // [3, 8]
  m2({x: 3, y: 8}) // [3, 8]
  
  // x 有值，y 无值的情况
  m1({x: 3}) // [3, 0]
  m2({x: 3}) // [3, undefined]
  
  // x 和 y 都无值的情况
  m1({}) // [0, 0];
  m2({}) // [undefined, undefined]
  
  m1({z: 3}) // [0, 0]
  m2({z: 3}) // [undefined, undefined]
  ```

- 只有尾部的参数才能有默认值

- 函数的`length`参数指定的是期望传入的参数个数，不计包含默认值的参数（因为不期望能传入）

- ES6引入rest参数替代之前的arguments变量实现可变参数

  > ```javascript
  > function add(...values) {
  >   let sum = 0;
  > 
  >   for (var val of values) {
  >     sum += val;
  >   }
  > 
  >   return sum;
  > }
  > 
  > add(2, 5, 3) // 10
  > 
  > // arguments变量的写法
  > function sortNumbers() {
  >   // 使用Array.prototype.slice.call将参数转换为数组
  >   return Array.prototype.slice.call(arguments).sort();
  > }
  > 
  > // rest参数的写法
  > const sortNumbers = (...numbers) => numbers.sort();
  > ```

- 函数的`name`属性可以返回函数名
- 箭头函数的四个注意点：
  - `this`指向定义时所在的对象，而不是使用时所在的对象
  - 不可以当做构造函数使用（不能使用new命令）
  - 没有`arguments`对象，但是可以使用rest参数实现可变参数
  - 没有`yield`命令，不能当做`Generator`函数

- 不适合使用箭头函数的地方

  - 定义对象的方法，这里的this会指向全局对象，因为对象不属于作用域
  - this的值动态变化（例如响应事件时，因为事件的this一般是触发事件的对象，不是某个固定的对象）

- 尾调用优化（目前只有Safari支持，可以节省内存，直达内层函数的调用帧，但是要求严格模式开启）

- 尾递归优化（存储递归的结果，不会发生栈溢出，所有满足ES6规范的客户端都要支持）

- 递归函数的改写

  - 对于需要状态参数的递归函数，可以用另一个caller将其包裹，保持API的友好

- ES2019要求函数的`toString()`方法必须返回函数本身的所有代码（包括注释、空格）

- ES2019允许`catch`语句省略参数

  ```javascript
  try {
    
  } catch { // 如果用不上err不用再写
    
  }
  ```

### 关于this/apply/call/bind

#### this

- 默认指向最后**调用**它的那个对象

  > ```javascript
  > var name = "windowsName";
  > 
  > function fn() {
  >   var name = 'Cherry';
  >   innerFunction();
  >   function innerFunction() {
  >     console.log(this.name);      // windowsName
  >   }
  > }
  > 
  > fn()
  > // 这时候fn()的this是window
  > ```

- 箭头函数中永远指向定义时候的this，不受调用干扰

  > ```javascript
  > var name = "windowsName";
  > 
  > var fn = {
  >   name: 'Cherry',
  >   // innerFunction的this为fn
  >   innerFunction: function() {
  >     // 此处继承innerFunction的this
  >     setTimeout(() => {
  >       console.log(this.name)
  >     })
  >   }
  > }
  > 
  > fn.innerFunction()
  > ```
  >
  > 箭头函数本身没有this，如果箭头函数被非箭头函数包含，则 this 绑定的是最近一层非箭头函数的 this，否则，this 为 undefined

- 使用`that = this`来指定this

  > ```javascript
  > var name = "windowsName";
  > 
  > var a = {
  > 
  >   name : "Cherry",
  > 
  >   func1: function () {
  >     console.log(this.name)     
  >   },
  > 
  >   func2: function () {
  >     var that = this;
  >     setTimeout( function() {
  >     	// 指定后that就变成了a而不是window
  >       that.func1()
  >     },100);
  >   }
  > 
  > };
  > 
  > a.func2()       // Cherry
  > ```

### apply/call/bind

- 使用这三个函数也可以来改变`this`的指向

  > ```javascript
  > func2: function () {
  >   setTimeout(  function () {
  >     this.func1()
  >   }.apply(a),100);
  > }
  > 
  > func2: function () {
  >   setTimeout(  function () {
  >     this.func1()
  >   }.call(a),100);
  > }
  > 
  > func2: function () {
  >   setTimeout(  function () {
  >     this.func1()
  >   }.bind(a)(),100);
  > }
  > ```

- `apply`调用一个函数，传入this值和数组包裹的参数列表
- `call`调用一个函数，传入this值和单独的若干个参数
- `bind`创建一个新的函数，传入this和预置参数，被调用的时候可以传入新的参数，附加在预置参数之后

## 数组的扩展

- 扩展运算符，可以理解为`rest`参数的逆运算，将一个数组转换为逗号分割的参数序列

  ```javascript
  console.log(...[1, 2, 3]) // 1, 2, 3
  ```

- 扩展运算符可以代替`apply`方法：

  ```javascript
  // ES5 的写法
  function f(x, y, z) {
    // ...
  }
  var args = [0, 1, 2];
  f.apply(null, args);
  
  // ES6的写法
  function f(x, y, z) {
    // ...
  }
  let args = [0, 1, 2];
  f(...args);
  
  ---
  
  // ES5 的写法
  Math.max.apply(null, [14, 3, 77])
  
  // ES6 的写法
  Math.max(...[14, 3, 77])
  
  // 等同于
  Math.max(14, 3, 77);
  
  ---
    
  // ES5的 写法
  var arr1 = [0, 1, 2];
  var arr2 = [3, 4, 5];
  Array.prototype.push.apply(arr1, arr2);
  
  // ES6 的写法
  let arr1 = [0, 1, 2];
  let arr2 = [3, 4, 5];
  arr1.push(...arr2);
  ```

- 扩展运算符还可以用来复制数组（深拷贝）

  ```javascript
  const a1 = [1, 2];
  // 写法一
  const a2 = [...a1];
  // 写法二
  const [...a2] = a1;
  ```

- 扩展运算符还可以合并数组（深拷贝）

  ```javascript
  const arr1 = ['a', 'b'];
  const arr2 = ['c'];
  const arr3 = ['d', 'e'];
  
  // ES5 的合并数组
  arr1.concat(arr2, arr3);
  // [ 'a', 'b', 'c', 'd', 'e' ]
  
  // ES6 的合并数组
  [...arr1, ...arr2, ...arr3]
  // [ 'a', 'b', 'c', 'd', 'e' ]
  ```

- 扩展运算符还可以生成数组

  ```javascript
  const [first, ...rest] = [1, 2, 3, 4, 5];
  first // 1
  rest  // [2, 3, 4, 5]
  
  const [first, ...rest] = [];
  first // undefined
  rest  // []
  
  const [first, ...rest] = ["foo"];
  first  // "foo"
  rest   // []
  ```

- 扩展运算符可以打散所有可迭代对象，比如ES6的字符串（可以实现按Unicode字符打散）

  ```javascript
  [..."😂😂"] // (2) ["😂", "😂"]
  ```

- `Array.from()`方法可以将可遍历对象转换为真正的数组，效果和扩展运算符类似，但是`Array.from()`方法只要求有`length`属性，扩展运算符要求满足`Symbol.iterator`接口

  ```javascript
  let arrayLike = {
      '0': 'a',
      '1': 'b',
      '2': 'c',
      length: 3
  };
  
  // ES5的写法
  var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']
  
  // ES6的写法
  let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
  ```

- `Array.of()`用于将一堆值转换为一个数组，为了弥补`Array()`函数构造函数的不足

  ```javascript
  Array.of(3, 11, 8) // [3,11,8]
  Array.of(3) // [3]
  Array.of(3).length // 1
  
  Array() // []
  Array(3) // [, , ,]
  Array(3, 11, 8) // [3, 11, 8]
  ```

- `Array.copyWithin()`方法可以原地覆盖成员，三个参数分别为替换开始位置，读取开始位置（可以为负数），结束读取位置（可以为负数）

  ```javascript
  // 将3号位复制到0号位
  [1, 2, 3, 4, 5].copyWithin(0, 3, 4)
  // [4, 2, 3, 4, 5]
  
  // -2相当于3号位，-1相当于4号位
  [1, 2, 3, 4, 5].copyWithin(0, -2, -1)
  // [4, 2, 3, 4, 5]
  
  // 将3号位复制到0号位
  [].copyWithin.call({length: 5, 3: 1}, 0, 3)
  // {0: 1, 3: 1, length: 5}
  
  // 将2号位到数组结束，复制到0号位
  let i32a = new Int32Array([1, 2, 3, 4, 5]);
  i32a.copyWithin(0, 2);
  // Int32Array [3, 4, 5, 4, 5]
  
  // 对于没有部署 TypedArray 的 copyWithin 方法的平台
  // 需要采用下面的写法
  [].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
  // Int32Array [4, 2, 3, 4, 5]
  ```

- `find()`和`findIndex()`，接收回调函数，直到找到一个返回值为True的成员/成员位置，如果没有则返回undefined

  ```javascript
  // 回调函数可以有三个参数，分别为当前值，当前位置，原完整数组
  // 两个方法都可以接受第二个参数，用于绑定回调函数的this对象
  [1, 5, 10, 15].findIndex(function(value, index, arr) {
    return value > 9;
  }) // 2
  
  function f(v){
    return v > this.age;
  }
  let person = {name: 'John', age: 20};
  [10, 12, 26, 15].find(f, person);    // 26
  ```

  > 这两个方法可以识别NaN，但`indexOf()`方法不行

- `fill()`方法可以填充数组，有三个参数分别为填充的值、填充的起始位置、填充的结束位置，但是如果填充的类型是对象，那么所有对象都指向同一内存地址

  ```javascript
  let arr = new Array(3).fill({name: "Mike"});
  arr[0].name = "Ben";
  arr
  // [{name: "Ben"}, {name: "Ben"}, {name: "Ben"}]
  
  let arr = new Array(3).fill([]);
  arr[0].push(5);
  arr
  // [[5], [5], [5]]
  ```

- 数组实例的`keys()` `values()` `entries()`分别生成三个迭代器对象，用于迭代键、值、键值对，如果不使用`for of`循环，可以手动调用遍历器对象的`next()`方法进行遍历       

- ES2016引入了`includes()`方法，相比较`indexOf()`方法更直观，第一个参数是搜索值，第二个参数是搜索的起始位置，使用的是严格相等运算符进行判断，不会出现`[NaN].indexOf(NaN)`返回-1的情况

  > 注意和Map&&Set的has方法区分开来

### 展平数组的若干方法

- 常规递归方法

  ```javascript
  function flattenMd(arr){
      var result=[]
      function flatten(arr){
          for (var i = 0; i < arr.length; i++) {
              if (Array.isArray(arr[i])) {
                  flatten(arr[i]);
              }else{
                  result.push(arr[i]);
              }        
          }
      }
      flatten(arr);
      return result;
  }
  var arr=[1, [2, 3, [4, 5], 6], 7, 8]
  console.log(flattenMd(arr));[ 1, 2, 3, 4, 5, 6, 7, 8 ];
  ```

- 数组concat+递归

  ```javascript
  flatten = function (arr) {
      return arr.reduce((plane, toBeFlatten) => (plane.concat(Array.isArray(toBeFlatten) ? flatten(toBeFlatten) : toBeFlatten)), []);
  }
  
  ```

- 利用ES6的展开符号递归

  ```javascript
  function deepFlatten(arr) {
      flatten = (arr)=> [].concat(...arr);
      return flatten(arr.map(x=>Array.isArray(x)? deepFlatten(x): x));
  }
  ```

- 数组的join和split（只适用于数字数组）

  ```javascript
  function flattenMd(arr) {
     return arr.join().split(',');   
  }
  var arr=['1', [null, 3, [4, 5], {K:1}], undefined, 8]
  console.log(flattenMd(arr));//[ '1', '', '3', '4', '5', '[object Object]', '', '8' ]
  ```

- ES6的`Array.prototype.flat()`方法

  ```javascript
  [1, 2, [3, [4, 5]]].flat()
  // [1, 2, 3, [4, 5]]
  
  [1, 2, [3, [4, 5]]].flat(2)
  // [1, 2, 3, 4, 5]
  
  [1, [2, [3]]].flat(Infinity)
  // [1, 2, 3]
  ```

- ES6的`Array.prototype.flatMap()`方法（结合了`flat()`和`map()`，但是只能展开一层数组，而且返回的还是一个嵌套数组）

  ```javascript
  // 相当于 [[[2]], [[4]], [[6]], [[8]]].flat()
  [1, 2, 3, 4].flatMap(x => [[x * 2]])
  // [[2], [4], [6], [8]]
  ```

## 对象的扩展

- 属性可以简写（使用大括号包围）

  ```javascript
  function f(x, y) {
    return {x, y};
  }
  
  // 等同于
  
  function f(x, y) {
    return {x: x, y: y};
  }
  
  f(1, 2) // Object {x: 1, y: 2}
  ```

- 方法也可以简写（在Vue.js里面有用到）

  ```javascript
  const o = {
    method() {
      return "Hello!";
    }
  };
  
  // 等同于
  
  const o = {
    method: function() {
      return "Hello!";
    }
  };
  ```

- JS模块也使用到了简写

  ```javascript
  let ms = {};
  
  function getItem (key) {
    return key in ms ? ms[key] : null;
  }
  
  function setItem (key, value) {
    ms[key] = value;
  }
  
  function clear () {
    ms = {};
  }
  
  module.exports = { getItem, setItem, clear };
  // 等同于
  module.exports = {
    getItem: getItem,
    setItem: setItem,
    clear: clear
  };
  ```

- 但是构造函数不能简写

- JavaScript定义对象的属性有两种方法，但是如果用大括号定义对象只能用方法1

  ```javascript
  // 方法一
  obj.foo = true;
  
  // 方法二
  obj['a' + 'bc'] = 123;
  ```

- 方法也可以拥有`name`属性，返回方法的名字，但是匿名函数返回`anonymous`，`bind`方法创造的函数，名字前会加一个`bound `

- ES6引入了`super`关键字，指向原型对象，但是只能用在方法里

  ```javascript
  // 报错
  const obj = {
    foo: super.foo
  }
  
  // 报错
  const obj = {
    foo: () => super.foo
  }
  
  // 报错
  const obj = {
    foo: function () {
      return super.foo
    }
  }
  ```

- `super`关键字等同于`Object.getPrototypeOf(this).foo`（属性）或`Object.getPrototypeOf(this).foo.call(this)`（方法）

- ES2020引入链式判断运算符（和Swift类似），如果条件都不满足，返回`undefined`

  ```javascript
  const firstName = message?.body?.user?.firstName || 'default';
  const fooValue = myForm.querySelector('input[name=foo]')?.value
  ```

- 链式判断运算符可以用于调用一个可能不存在的对象，利用它如果不为真就不会求值的特性

- ES2020引入了一个新的Null判断运算符`??`，对比`||`，只有左侧的值为`null`和`undefined`才能返回右侧的值，而不像`||`对`false`和`0`也生效

### 对象的各种方法

- 初始化方法
  - `Object.assign()`合并两个对象，返回目标对象，同名对象后来者后覆盖，可以用于深拷贝
  - `Object.create()`创建一个新对象，可以用于继承
- 配置属性
  - `Object.defineProperty()`给对象添加一个属性，并指定该属性的配置
  - `Object.defineProperties()`给对象添加多个属性，并分别指定它们的配置
- 获取属性
  - `Object.getOwnPropertyDescriptor(obj, prop)`可以获取到方法中某个属性的描述对象
  - `Object.getOwnPropertyNames(obj)`可以获取到对象自身所有属性（不含`Symbol`，但是包含不可枚举属性）的键名
  - `Object.getOwnPropertySymbols(obj)`可以获取到自身所有`Symbol`属性的键名
  - `for...in`循环遍历对象自身的可枚举属性（不含`Symbol`属性）
  - `Object.entries()`返回对象自身可枚举属性的键值对数组（二维数组），和`for...in`返回内容相同
  - `Object.keys()`获取对象自身所有属性（不含`Symbol`和不可枚举属性）
  - `Reflect.ownKeys(obj)`获取所有键名（包含`Symbol`和不可枚举属性）
  - `Object.prototype.toString()`返回对象的字符串表示
- 配置状态
  - `Object.preventExtensions()`阻止对象的任何扩展
  - `Object.freeze()`阻止对象被修改
  - `Object.seal()`阻止其他代码删除对象的属性（使用`delete`关键字）
  - `Object.setPrototypeOf()`设置对象的原型
- 获取状态
  - `Object.prototype.hasOwnProperty()`某个对象是否有非继承的指定属性
  - `Object.prototype.isPrototypeOf()`指定对象是否在本对象的原型链中
  - `Object.is()`判断两个值是否相同（内存/字面量相同）

