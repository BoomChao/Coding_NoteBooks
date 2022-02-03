## 初识 JavaScript

浏览器执行 JS 简介

渲染引擎：用来解析HTML与CSS，俗称内核，比如 chrome 浏览器的 blink，老版本的 webkit

JS 引擎：也称为 JS 解释器；用来读取网页中的 JavaScript 代码，对其进行处理后运行，比如 chrome 浏览器的 VB

浏览器本身不会执行 JS 代码，而是通过内置 JavaScript 引擎（解释器）来执行 JS 代码；JS 引擎执行代码时逐行解释每一句源码（转换成机器语言），然后由计算机去执行，所以 JavaScript 语言归为脚本语言，会逐行解释执行



JavaScript 是一种弱类型或者说动态语言，这意味着不需要提前声明变量的类型，在程序运行过程中会自动推断类型

