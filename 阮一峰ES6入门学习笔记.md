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

