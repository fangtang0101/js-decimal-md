![decimal.js](https://raw.githubusercontent.com/MikeMcl/decimal.js/gh-pages/decimaljs.png)

JavaScript中一款高精度小数类型(Decimal)

## 特点

	- 整数型和浮点型
	- 简单并且全面的 API
	- 复制了很多 JavaScript 中 `Number.prototype`(Number 类型属性) 和`Math`类
	- 能够处理 十进制 二进制 八进制的值
	- 相比较java 中的 BigDecimal，decimal.js 更加快 、更小、易于使用
	- 无依赖
	- 兼容性好:仅仅使用了  JavaScript 1.5 (ECMAScript 3)的一些功能与特性
	- 全面的[文档](http://mikemcl.github.io/decimal.js/)和测试用例
	- 包含一个 TypeScript 的声明文件： decimal.d.ts

![API图片](https://raw.githubusercontent.com/MikeMcl/decimal.js/gh-pages/API.png)

此库 与 [bignumber.js](https://github.com/MikeMcl/bignumber.js/)相似，但是注意此库的精度是以 有效数字 来规定的，而不是小数点的位置. 此库中所有的运算都是四舍五入到 对应的精度(类似于python中的 decimal 模块)，而不是仅仅只是除法运算四舍五入

另外，此库还添加了 三角函数的 函数方法，此外还支持非整数幂，这也使得它明显的比 bignumber.js 大，但仍然小于 [big.js](https://github.com/MikeMcl/big.js/).

为此我们特地提供了不包含三角函数的版本，详见[decimal.js-light](https://github.com/MikeMcl/decimal.js-light/).

## 如何加载库

此库是一个单一的JavaScript 文件*decimal.js* (或则压缩版, *decimal.min.js*).


浏览器加载:


```html
<script src='path/to/decimal.js'></script>
```

[Node.js](http://nodejs.org):

```bash
$ npm install --save decimal.js
```

```js
var Decimal = require('decimal.js');
```

ES6 module(*decimal.mjs*):

```js
import {Decimal} from 'decimal.js';
```

AMD 加载 例如 [requireJS](http://requirejs.org/):

```js
require(['decimal'],function(Decimal){
	// 此处使用 Decimal 只是本作用域，而不是 全局Decimal
	})
```

## 如何使用

下面是所有的例子，例子中 所有的 `var` 分号 `toString` 都没有显示. 如果注释里面的值在 引号里面，那么说明在此之前已经调用过 `toString`

此库导出单个函数，`Decimal`,也就是 Decimal 的构造函数

它可以传入 一个 number，string 或则 Decimal 类型的值

```js
x = new Decimal(123.4567)
y = new Decimal('123456.7e-3')
z = new Decimal(x)
x.equals(y) && y.equals(z) && x.equals(z)        // true
```

十六进制 八进制 的值 如果加了前缀 也是可以传入

```js
x = new Decimal('0xff.f')            // '255.9375'
y = new Decimal('0b10101100')        // '172'
z = x.plus(y)                        // '427.9375'

z.toBinary()                         // '0b110101011.1111'
z.toBinary(13)                       // '0b1.101010111111p+8'

```

用指数创建一个Decimal 最大值(Number.MAX_VALUE MAX_VALUE 属性是 JavaScript 中可表示的最大的数。它的近似值为 1.7976931348623157 x 10308)

```js
x = new Decimal('0b1.1111111111111111111111111111111111111111111111111111p+1023')
```

Decimal 的方法是不会改变自身的值，运算的值 是返回回来，而不是本身

```js
0.3 - 0.1                     // 0.19999999999999998
x = new Decimal(0.3)
x.minus(0.1)                  // '0.2'
x                             // '0.3'
```

库里面的方法支持链式调用

```js
x.dividedBy(y).plus(z).times(9).floor()
x.times('1.23456780123456789e+9').plus(9876.5432321).dividedBy('4444562598.111772').ceil()
```

很多方法都有相应的别名

```js
x.squareRoot().dividedBy(y).toPower(3).equals(x.sqrt().div(y).pow(3))         // true
x.cmp(y.mod(z).neg()) == 1 && x.comparedTo(y.modulo(z).negated()) == 1        // true
```

类似于JavaScript 中Number 类型，此处也是 有 `toExponential` `toFixed ` `toPrecision ` 方法

```js
x = new Decimal(255.5)
x.toExponential(5)              // '2.55500e+2'
x.toFixed(5)                    // '255.50000'
x.toPrecision(5)                // '255.50'
```

许多的方法也是从 JavaScript 的Math 中借鉴过来的

```js
Decimal.sqrt('6.98372465832e+9823')      // '8.3568682281821340204e+4911'
Decimal.pow(2, 0.0979843)                // '1.0702770511687781839'
```

由于`NaN` `Infinity` 在 `Decimal` 是合法的值， 所以此处有对应的 `isNaN` `isFinite` 方法

```js
x = new Decimal(NaN)                                           // 'NaN'
y = new Decimal(Infinity)                                      // 'Infinity'
x.isNaN
```

`toFraction`(分数化，将对应的值转化为分数),此方法可以 设置最大的分母

```
z = new Decimal(355)
pi = z.dividedBy(113)        // '3.1415929204'
pi.toFraction()              // [ '7853982301', '2500000000' ]
pi.toFraction(1000)          // [ '355', '113' ]
```

所有运算 都是根据有效数字的值 来舎入 最后的值，舎入模式 由 `precision ` 和 `rounding `决定，这两个值在构造函数时候，可以传入对应的值

进阶用法，可以同时 创建 多个 Decimal 的构造函数，所有的Decimal的值 可以根据 每个不同配置来创建

```js
// Set the precision and rounding of the default Decimal constructor
Decimal.set({ precision: 5, rounding: 4 })

// Create another Decimal constructor, optionally passing in a configuration object
Decimal9 = Decimal.clone({ precision: 9, rounding: 1 })

x = new Decimal(5)
y = new Decimal9(5)

x.div(3)                           // '1.6667'
y.div(3)                           // '1.66666666'
```
warning ... 待翻译 ...

```js
x = new Decimal(-12345.67);
x.d                            // [ 12345, 6700000 ]    digits (base 10000000)
x.e                            // 4                     exponent (base 10)
x.s                            // -1                    sign
```

更多详细介绍请参看[API](http://mikemcl.github.io/decimal.js/)文档

## 测试

此库可以用 Node.js 或则浏览器 测试

测试目录包含一个 *test.js* 文件，当用Node 执行此文件的时候，它会 跑所有的 测试案列。或则当 *test.html*在浏览器打开的时候，也是会跑所有的测试案列

在用npm的时候直接执行下面的 命令来 跑所有的测试案列

```bash
$ npm test
```

在测试目录用Node 执行

```bash
$ node test
```

每个测试用例可以单独的执行 进行测试，比如 在 *test/modeles*目录 运行

```bash
$ node toFraction
```

## 构建

Node环境，安装uglify-js 

```bash
npm install uglify-js -g
```
然后

```
npm run build
```

将会创建一个 decimal.min.js 文件。 并且在 文档中 添加一份 map资源文件













### 词汇

arbitrary - 任意的
precision - 精度
Decimal   - 小数
Replicates- 复制


## 参考文档
https://blog.csdn.net/a372048518/article/details/7492301

