# ESMAScript6 学习笔记
@(前端知识)[javascript, Markdown]

[TOC]

### 一.let和const命令
1. let 、const 块级作用域 { } ，不存在变量提升
2. const 声明的是常量，声明时必须赋值
3. 暂时性死区:在声明之前赋值会报错；在let/const命令声明变量tmp之前，都属于变量tmp的“死区”, **在声明之前不能使用**
	> if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError
  let tmp; // TDZ结束
  console.log(tmp); // undefined
  tmp = 123;
  console.log(tmp); // 123
	}
}

4. 不允许在相同的作用域，重复声明同一个变量
5. 允许在块级作用域之中声明函数
6. const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动
### 二.变量的解构赋值
```javascript
let [a, b, c] = [1, 2, 3];
----------------------------------
let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]
----------------------------------
let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
----------------------------------
解构不成功
let [foo] = [];
let [bar, foo] = [1];
----------------------------------
不完全解构
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
----------------------------------
默认值：解构赋值允许指定默认值
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
----------------------------------
如果一个数组成员不严格等于undefined，默认值是不会生效的
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null
----------------------------------
默认值可以引用解构赋值的其他变量，但该变量必须已经声明
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError
----------------------------------
对象的解构赋值
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
----------------------------------
let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
----------------------------------
字符串的解构赋值
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
----------------------------------
数值和布尔值的解构赋值
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
----------------------------------
解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。
let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError
----------------------------------
函数参数的解构赋值
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3
```
#### 解构赋值的作用
1. 交换变量的值
```javascript
let x = 1;
let y = 2;

[x, y] = [y, x];
```
2. 从函数返回多个值
```javascript
// 返回一个数组

function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```
3. 函数参数的定义
```javascript
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```
4. 提取JSON数据
```javascript
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```
5. 函数参数的默认值
```javascript
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};
```
6. 遍历Map结构
```javascript
var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world
```
7. 输入模块的指定方法
```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```
### 三.字符串的扩展
1. at()  改进charAt()
2. repeat()  
```javascript
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```
3. padStart()、padEnd()
```javascript
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```
4. 模板字符串
```javascript
如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中
模板字符串中嵌入变量，需要将变量名写在${}之中
大括号内部可以放入任意的JavaScript表达式，可以进行运算，以及引用对象属性。
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```
### 函数、数组的扩展
####箭头函数有几个使用注意点

（1）函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。

（2）不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。

（3）不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

（4）不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

#### 扩展运算符（spread）是三个点（...）
1. 合并数组
```javascript
扩展运算符提供了数组合并的新写法。

// ES5
[1, 2].concat(more)
// ES6
[1, 2, ...more]

var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];

// ES5的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```
2. 与解构赋值结合
```javascript
扩展运算符可以与解构赋值结合起来，用于生成数组。

// ES5
a = list[0], rest = list.slice(1)
// ES6
[a, ...rest] = list
下面是另外一些例子。

const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []
如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。

const [...butLast, last] = [1, 2, 3, 4, 5];
// 报错

const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错
```
3. 函数的返回值
```javascript
JavaScript 的函数只能返回一个值，如果需要返回多个值，只能返回数组或对象。扩展运算符提供了解决这个问题的一种变通方法。

var dateFields = readDateFields(database);
var d = new Date(...dateFields);
上面代码从数据库取出一行数据，通过扩展运算符，直接将其传入构造函数Date。
```
4. 字符串
```javascript
扩展运算符还可以将字符串转为真正的数组。

[...'hello']
// [ "h", "e", "l", "l", "o" ]
上面的写法，有一个重要的好处，那就是能够正确识别32位的Unicode字符。

'x\uD83D\uDE80y'.length // 4
[...'x\uD83D\uDE80y'].length // 3
上面代码的第一种写法，JavaScript会将32位Unicode字符，识别为2个字符，采用扩展运算符就没有这个问题。因此，正确返回字符串长度的函数，可以像下面这样写。

function length(str) {
  return [...str].length;
}

length('x\uD83D\uDE80y') // 3
凡是涉及到操作32位 Unicode 字符的函数，都有这个问题。因此，最好都用扩展运算符改写。

let str = 'x\uD83D\uDE80y';

str.split('').reverse().join('')
// 'y\uDE80\uD83Dx'

[...str].reverse().join('')
// 'y\uD83D\uDE80x'
上面代码中，如果不用扩展运算符，字符串的reverse操作就不正确。
```
5. 实现了 Iterator 接口的对象
```javascript
任何 Iterator 接口的对象（参阅 Iterator 一章），都可以用扩展运算符转为真正的数组。

var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
上面代码中，querySelectorAll方法返回的是一个nodeList对象。它不是数组，而是一个类似数组的对象。这时，扩展运算符可以将其转为真正的数组，原因就在于NodeList对象实现了 Iterator 。

对于那些没有部署 Iterator 接口的类似数组的对象，扩展运算符就无法将其转为真正的数组。

let arrayLike = {
  '0': 'a',
  '1': 'b',
  '2': 'c',
  length: 3
};

// TypeError: Cannot spread non-iterable object.
let arr = [...arrayLike];
上面代码中，arrayLike是一个类似数组的对象，但是没有部署 Iterator 接口，扩展运算符就会报错。这时，可以改为使用Array.from方法将arrayLike转为真正的数组。
```
6. Map 和 Set 结构，Generator 函数
```javascript
扩展运算符内部调用的是数据结构的 Iterator 接口，因此只要具有 Iterator 接口的对象，都可以使用扩展运算符，比如 Map 结构。

let map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

let arr = [...map.keys()]; // [1, 2, 3]
Generator 函数运行后，返回一个遍历器对象，因此也可以使用扩展运算符。

var go = function*(){
  yield 1;
  yield 2;
  yield 3;
};

[...go()] // [1, 2, 3]
上面代码中，变量go是一个 Generator 函数，执行后返回的是一个遍历器对象，对这个遍历器对象执行扩展运算符，就会将内部遍历得到的值，转为一个数组。

如果对没有 Iterator 接口的对象，使用扩展运算符，将会报错。

var obj = {a: 1, b: 2};
let arr = [...obj]; // TypeError: Cannot spread non-iterable object
```
7. Array.from() 
Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括ES6新增的数据结构Set和Map）。
8. Array.of()
Array.of方法用于将一组值，转换为数组。
9. 数组实例的fill()
```javascript
fill方法使用给定值，填充一个数组。

['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
上面代码表明，fill方法用于空数组的初始化非常方便。数组中已有的元素，会被全部抹去。

fill方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。

['a', 'b', 'c'].fill(7, 1, 2)
// ['a', 7, 'c']
上面代码表示，fill方法从1号位开始，向原数组填充7，到2号位之前结束。
```
10. 数组实例的 entries()，keys() 和 values() 
```javascript
ES6 提供三个新的方法——entries()，keys()和values()——用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用for...of循环进行遍历，唯一的区别是keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历。

for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
如果不使用for...of循环，可以手动调用遍历器对象的next方法，进行遍历。

let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next().value); // [0, 'a']
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```
11. Object.assign()
Object.assign方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
```javascript
（1）为对象添加属性

class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}
上面方法通过Object.assign方法，将x属性和y属性添加到Point类的对象实例。

（2）为对象添加方法

Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同于下面的写法
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};
上面代码使用了对象属性的简洁表示法，直接将两个函数放在大括号中，再使用assign方法添加到SomeClass.prototype之中。

（3）克隆对象

function clone(origin) {
  return Object.assign({}, origin);
}
上面代码将原始对象拷贝到一个空对象，就得到了原始对象的克隆。

不过，采用这种方法克隆，只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码。

function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
（4）合并多个对象

将多个对象合并到某个对象。

const merge =
  (target, ...sources) => Object.assign(target, ...sources);
如果希望合并后返回一个新对象，可以改写上面函数，对一个空对象合并。

const merge =
  (...sources) => Object.assign({}, ...sources);
（5）为属性指定默认值

const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

function processContent(options) {
  options = Object.assign({}, DEFAULTS, options);
  console.log(options);
  // ...
}
上面代码中，DEFAULTS对象是默认值，options对象是用户提供的参数。Object.assign方法将DEFAULTS和options合并成一个新对象，如果两者有同名属性，则option的属性值会覆盖DEFAULTS的属性值。

注意，由于存在浅拷贝的问题，DEFAULTS对象和options对象的所有属性的值，最好都是简单类型，不要指向另一个对象。否则，DEFAULTS对象的该属性很可能不起作用。

const DEFAULTS = {
  url: {
    host: 'example.com',
    port: 7070
  },
};

processContent({ url: {port: 8000} })
// {
//   url: {port: 8000}
// }
上面代码的原意是将url.port改成8000，url.host不变。实际结果却是options.url覆盖掉DEFAULTS.url，所以url.host就不存在了。
```
