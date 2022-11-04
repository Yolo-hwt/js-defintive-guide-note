# js-defintive-guide-note
A few things about learing javascript-defintive-guide

# 犀牛书learing

本书的书名包含**“权威”**二字是认真的

## 第一章（javascript简介）

javascript是一门**高级、动态、解释型**编程语言，所有现代web浏览器都包含javascript解释器，几乎所有浏览器都支持ECMAScript

**@ECMAScript**：由于商标问题而重命名的这门语言的标准版本名称，从ES6开始每年发布一次

- **一个hello world**

```js
console.log('Hello World!')
```

## 第二章（词法结构）

**javascript区分大小写**

### 字面量

字面量（literal）是一种直接出现在程序中的数据值

```ini
12
1.2
"hello world"
'Hi'
true
```

上述均为字面量

### 标识符

- 以字母、下划线（`_`）或美元符号（`$`）开头
- 不能以数字开头（以便区分标识符和数值）
- 保留字不能作为标识符（如 if\while\for......）

合法的标识符示例

```ini
i
my_val
v13
_dummy
$str
```

### Unicode

**！！！javascript是使用Unicode字符集编写的**

在字符串和注释中可以使用任意Unicode字符

（注释会被忽略，所以注释中的转义序列会被作为ASCII字符处理，而不会被解释为Unicode）

考虑到**可移植性和易于编辑**，建议标识符中只使用ASCII字母和数字，但这只是一个编程惯例

- **Unicode转义序列**

为方便程序员编码和支持使用老技术的系统，javascript定义了转义序列，从而可以仅仅使用ASCII字符来表示Unicode字符

1. 转义字符以`\u`开头，后跟4位十六进制数字（包括大写或小写的字母A~F），或包含在一对花括号内的1-6位十六进制数字

```ini
cafe'(e头上带一个第二声的音标，暂时不知道怎么打出来)
//可表示为
caf\u00e9
caf\u{E9}
```

！！！带花括号版本的十六进制目的是为了更好的支持大于16位的Unicode码点，比如符号表情

- **Unicode归一化**

**问题根源：**Unicode允许使用多种编码方式来表示同一个字符

而javascript在解释执行过程中是假定自己所执行的源代码已经归一化，所以相同Unicode的表示可能会被识别为两个不同的部分，常常出现在以Unicode作为变量名所带来的问题

**解决：**应该保证使用自己的编辑器或其他工具对自己的源代码执行Unicode归一化

### 可选的分号

javascript的分号是**可选的**，通常会把换行符当作分号，但是并非任何时候都把换行符当作分号

遵循这么一句话：**javascript只在下一个非空格字符无法被解释为当前语句的一部分时才把换行符当作分号**

```js
let a
a
=
3
console.log(a);
//js将以上代码解释为
let a;a=3;console.log(a);
```

因为`let a a `无法被正确解析，所以添加了分号，而`a=3`是连续的且合法的，所以被组合到了一起

**特殊的情况**

1. 涉及`return\throw\yield\break\continue`语句，这几个语句后面的换行符会被解释为`;`

```js
return
true;//vscode内会变灰，即return之后了解释不到
//解析为
return; true;
```

2. 涉及`++或--`操作符

```js
let a=0;
a
++//此处一般在编辑器内会报错
//js解析为
let a=0;a;++;
```

3. 涉及`=>`

规定箭头语法定义的函数，**箭头必须跟参数列表在同一行**

```js
const fn = ()
=> {}//编辑器内报错
```

4. **其他不报错的情况，但是存在语义问题**

```js
let y=x+f
(a+b).toString()
//会被解析成
let y=x+f(a+b).toString();
```

第二行的括号，可以认为是f的一个函数调用，所以被组合到了一起，**但是这可能并不是代码作者的真实意图**

必要的情况下还是需要我们**显示的使用`;`**来让js可以正确理解我们的代码

常见的方式有`;防御`

```js
let x=0
;[x,x+1,x+2].forEach(console.log)
```

## 第三章（类型、值和变量）

计算机程序通过操作值或文本来工作，编程语言中这些可以表示和操作的值被称为类型

**javascript类型分为**：原始数据类型和对象类型，**原始类型的值是不可修改的**

### null和undefined

- null和undefined是原始值

- null 和 undefined不能调用方法

### 数值字面量

Number是javascript的主要数值类型，用于表示整数和近似实数

js使用 `IEEE754` 标准

### IEEE-754标准

JavaScript 的数字是 IEEE-754 标准存储的双精度浮点数类型。

双精度浮点数总共有 **64 位（bit）**，第一位用于表示符号，接着十一位用于表示阶码，剩余的五十二位用于表示尾数。

![image.png](README.assets/453cabd88adc477a9f6548fca7e35268tplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

- 浮点数的二进制科学计数法存储，公式表示如下

  - ![image.png](README.assets/e446a5d6d9784281b1dfd43096510c7ftplv-k3u1fbpfcp-zoom-in-crop-mark3024000.webp)

- **符号位：**0表示正数，1表示负数

- **阶码：**表示是2的多少次方（-1023~1024）

  - 阶码使用移码方式存储，偏移值为+1023，也就是需要在二进制运算的基础上，手动减去 1023 才是真正表达的值

  - ```js
    01111111111 // 移码表示，减去1023后为，0
    10000000000 // 1
    11111111110 // 1023
    00000000000 // -1023
    ```

- **尾数：**尾码表示的是小数点后面的部分，以二进制表示的非零数字，第一位有效数字肯定是1，所以**标准规定第一个1不需要存储**

  - 这样设计引出的问题，由于永远都有一个隐藏的1，**如何表示0？**

    标准0的二进制形式为

  - ```js
    0 00000000000 0000000000000000000000000000000000000000000000000000
    //阶码部分是移码表示，减去1023之后，为-1023
    ```

    换算为十进制为 `2^-1023*1.0` ，是一个小到可以忽略不计的极小值

### 二进制浮点数与舍入错误

- **Number.MAX_SAFE_INTEGER**

  - js最大安全整数，这里的安全 指的是**能够 one-by-one 表示的整数**，也就是双精度数表示和整数是一对一的，
    反过来说，在某个范围以内，所有的整数都有唯一的浮点数表示，那这个范围内的数字就叫做这就叫做安全整数

  - **尾数位数决定了最大的整数范围**，若超过尾数最大范围，也就意味着有的数无法表示，在做数值运算时，我们会要求数值以及运算结果必须不能超出 `-Number.MAX_SAFE_INTEGER` ~ `Number.MAX_SAFE_INTEGER` 的范围。

  - 尾数在结构上总共52位，加上隐藏的1总共53位，所以**最大表示为53个1**（`2^53-1`）

    ![image-20221101152945724](README.assets/image-20221101152945724.png)

  - 超出范围，会**带来预料之外的结果**

    - `9007199254740992` （即2^53）的二进制形式为：

      ```js
      0 10000110100 0000000000000000000000000000000000000000000000000000
      //中间阶码部分
      ```

      而 `9007199254740993` 的二进制形式**也是！！！**：

      ```js
      0 10000110100 0000000000000000000000000000000000000000000000000000
      ```

      所以如果拿这两个数字判断是否相等，结果当然是 `true！！！`

  - 同样由于数据溢出引起的**0.1+0.2!==0.3**问题

    - 十进制**整数**转二进制，**除二取余法**（例如十进制15转二进制为1111）

      ```js
      15 % 2 === 1, 15 => 7
      7 % 2 === 1, 15 => 3
      3 % 2 === 1, 3 => 1
      1 % 2 === 1, 1 => 0
      //除二直到余数为0
      //1111
      ```

    - 十进制**小数**转二进制，**乘法取整**（十进制0.125为0.001）

      ```js
      //整数部分为0
      //小数部分
      0.125*2=0.25，0
      0.25*2=0.5，0
      0.5*2=1，1
      //小数部分为001
      ```

    十进制 `0.1` 用二进制表示

    ```js
    0.1 * 2 => 0.2, 0
    0.2 * 2 => 0.4, 0
    0.4 * 2 => 0.8, 0
    0.8 * 2 => 1.6, 1
    0.6 * 2 => 1.2, 1
    0.2 * 2 => 0.4, 0
    ...
    ```

    可以看到，0.1 用二进制表示为 `0.00011001100110011...`， 出现了 `0011` 的无限循环，第一个有效数字`1`出现在小数点后第四位，把它往前移动四位，阶码为 `-4`，符号位为 `0`，合在一起就得到 `0.1` 的二进制形式：

    ```js
    0 01111111011 1001100110011001100110011001100110011001100110011010
    ```

    `0.2` 是 `0.1` 的两倍，尾码保持不动，阶码 + 1，得到 `0.2` 的二进制形式：

    ```js
    0 01111111100 1001100110011001100110011001100110011001100110011010
    ```

    到这里，我们知道了，数字 `0.1`，`0.2` 在计算机里表示的时候，本身就存在**精度丢失**（本来无限循环的数字被截断了）。

    0.2 的阶码比 0.1 的阶码大一，我们把 0.1 的尾码右移一位，阶码加 1，让两个数的**阶码保持一致**。

    ```js
    0 01111111100 0.1100110011001100110011001100110011001100110011001101
    0 01111111100 1.1001100110011001100110011001100110011001100110011010
    ```

    现在阶码相同了，尾数相加得到：

    ```js
      0 01111111100  0.1100110011001100110011001100110011001100110011001101
    + 0 01111111100  1.1001100110011001100110011001100110011001100110011010
    = 0 01111111100 10.0110011001100110011001100110011001100110011001100111
    ```

    把运算结果按照 `IEEE-754` 标准格式化，需要向右移动一位，阶码加一。但这时候发现，最后一个 `1` 放不下了，需要舍弃，根据标准当要舍弃一位数时，需要进行`0舍1入`。如果被舍弃的是 `0` 什么都不用做，如果被舍弃的是`1`，则需要补回来。

    ```js
    0 01111111101 0011001100110011001100110011001100110011001100110011 1（1 多出，需要舍弃）
    0 01111111101 0011001100110011001100110011001100110011001100110100  （补 1）
    ```

    于是，我们得到了 0.1 + 0.2 的运算结果。

    ```js
    0 01111111101 0011001100110011001100110011001100110011001100110100
    ```

    再来，使用**乘2取整**的方法，算一下 `0.3` 的二进制是怎么表示的。

    ```js
    0.3 * 2 => 0.6, 0
    0.6 * 2 => 1.2, 1
    0.2 * 2 => 0.4, 0
    0.4 * 2 => 0.8, 0
    0.8 * 2 => 1.6, 1
    0.6 * 2 => 1.2, 1
    ...
    ```
    
    一样出现了循环 `0011`，第一个数字 1 出现在第二位，尾码往前移动两位，阶码为 `-2`。所以 `0.3` 的二进制形式如下：
    
    ```js
    0 01111111101 0011001100110011001100110011001100110011001100110011
    ```
    
    和 `0.1 + 0.2` 的运算结果确实不相等


### js算数运算

运算符：`+`，`-`，`*`，`/`，`%`，`&`，`|`，`**`

Math类提供的函数

```
pow,round,ceil,floor,abs,random,...
```

**算术运算的注意点**

- javascript中的算数在遇到**上溢出、下溢出或被0除**时候不会发生错误
- 数值操作结果超过最大可表示数值（**上溢出**）为`infinity`，负无穷为`-infinity`
- **下溢出**发生在数值操作结果比最小可表示数值更接近0，此时js返回0，如果下溢出来自负数，则返回`-0`

```js
console.log(0 / 14);//0
console.log(14 / 0);//Infinity
console.log(-14 / 0);//-Infinity
console.log(0 / 0);//NaN
console.log(Number.isFinite(Infinity));//false
//Number.isFinite 判断数据是数值还是无穷，数值：true，无穷：false
```

**！！！+0与-0几乎无法区分**

```
0==-0//true
0===-0//true
```

可以使用`Object.is()`来判断

```
Object.is(0,-0)//false
```

### BigInt

ES2020新增，主要是为了表示**64位整数**

**BigInt字面量**（默认10为基数，可添加前缀）

```js
1234n		//一个普通BigInt字面量
0b111111n	//二进制BigInt
0o111n		//八进制BigInt
0x800000n	//64位整数
```

可以使用`BigInt()`函数把常规Javascript数值或字符串转为BigInt值

```js
BigInt(Number.MAX_SAFE_INTEGER)//900719925474099
let str="1"+"0".repeat(100)		//1后跟100个0
BigInt(str)						//10n**100n：一个64位整数
```

**！！！标准的操作符可以用于BigInt，但不能混用BigInt操作数和常规数值操作数**

**！！！Math对象的任何函数都不接收BigInt操作数**

```js
2/2n	//Uncaught TypeError TypeError: Cannot mix BigInt and other types, use explicit conversions
Math.floor(3n)	//Uncaught TypeError TypeError: Cannot convert a BigInt value to a number
```

**但是允许比较操作符混合操作数类型**

```js
1<2n	//true
```

BigInt值的算术运算与常规JavaScript数值算数运算类似，只不过**除法会丢弃余数并且会向下（向0）舍入**

```js
3000n / 997n	//3n，商是3
//3000/997=3.009027...
```

### 日期和时间

主要就是Date对象，其数值表示形式叫时间戳，即1970年1月1日起至今的毫秒数

```js
let timestamp=Date.now();	//当前的时间戳
let now=new Date();			//当前时间的日期对象
let ms=now.getTime();		//转换为毫秒时间戳
let iso=now.toISOString();	//转换为标准格式字符串
```

### 字符码点与字符串

- js使用unicode字符集的UTF-16编码，故js字符串是无符号16位值的序列

- 一个码点（codepoint）是16位的

**码点超出16位问题**

码点超出16位的Unicode字符集使用UTF-16规则编码为两个16位值的序列

**！！！这意味着一个长度为2的js字符串可能表示的是一个unicode字符**

```js
let love="❤"	//❤的UTF-16编码是"\ud83d\udc99"
```

- js的字符串操作方法一般操作的是**16位值（一个码点），而不是字符**
  - 也就是说这些js方法不会对超过一个码点的特殊字符进行特殊处理（归一化）

解决：

- 在es6中，字符串是可迭代的，可以对字符串使用 `for...of` 循环或者 `...` 扩展运算符得到字符的迭代而不是16位码点

### 字符串字面量与转义字符

- js中可以把字符串放在一对匹配的单引号，双引号或者反引号中（支持嵌套）

```js
'test'
"name"
`hwt`
'yes my name is "hwt"'
```

- 早期版本要求字符串字面量必须写在一行，使用+来拼接

```js
let str = 'one '+
'two '+
'three';
console.log(str);
```

- es5之后可以在每行末尾添加一个反斜杠（\）

```js
let str = 'one \
two \
three';
console.log(str);
```

- 反斜杠还可以和后面的既定字符组合，形成转义序列

```js
\0		//NUL字符
\t		//水平制表符
\n		//换行符
\\n		//反斜杠+n
\'		//单引号
\"		//双引号
\r		//回车符
...
e.g.
'you\'re right!'
//you're right! 
```

### 标签化模板字面量

如果在开头的反引号前有一个函数名（标签），那么模板字面量中的文本和表达式值将作为参数传递给这个函数

```js
const tag = function (str) {
    console.log(str[0]);//传递过来是一个对象如下图
};
tag`'one two three\n'`;
//'one two three
//'
```

![image-20221104155705949](README.assets/image-20221104155705949.png)

ES6内置一个标签函数：String.row()

返回反引号中未经处理的文本，即不会进行任何转义

```js
console.log(String.raw`'one two three\n'`);
//'one two three\n'
```

### 布尔值

布尔值表示真或假：**true / false**
