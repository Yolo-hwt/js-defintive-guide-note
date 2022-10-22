# js-defintive-guide-note
A few things about learing javascript-defintive-guide

# 犀牛书learing

本书的书名包含**“权威”**二字是认真的

## 第一章

javascript是一门**高级、动态、解释型**编程语言，所有现代web浏览器都包含javascript解释器，几乎所有浏览器都支持ECMAScript

**@ECMAScript**：由于商标问题而重命名的这门语言的标准版本名称，从ES6开始每年发布一次

## 第二章

javascript区分大小写

- **标识符**

以字母、下划线（_）或美元符号（$）开头

- **Unicode**

**javascript是使用Unicode字符集编写的**，在字符串和注释中可以使用任意Unicode字符

（注释会被忽略，所以注释中的转义序列会被作为ASCII字符处理，而不会被解释为Unicode）

考虑到**可移植性和易于编辑**，建议标识符中只使用ASCII字母和数字，但这只是一个编程惯例

- **Unicode归一化**

**问题根源：**Unicode允许使用多种编码方式来表示同一个字符

而javascript在解释执行过程中是假定自己所执行的源代码已经归一化，所以相同Unicode的表示可能会被识别为两个不同的部分，常常出现在以Unicode作为变量名所带来的问题

**解决：**应该保证使用自己的编辑器或其他工具对自己的源代码执行Unicode归一化

- **可选的分号**

javascript的分号是可选的，通常会把换行符当作分号，但是并非任何时候都把换行符当作分号

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

1. 涉及`return\throw\yield\break\continue`语句

这几个语句后面的换行符会被解释为`;`

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

