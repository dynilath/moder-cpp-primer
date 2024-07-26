---
title: 3.3 const 与 volatile 限定符
---

在 C++ 中，有两个类型限定符，`const` 和 `volatile`，它们可以被用来对类型做出修饰限定，并成为限定的对象类型的一部分。这两个限定符可以被用于任何类型。

限定符的使用方式如下：
```cpp
const int a = 5; // a 的类型是 const int
volatile int b = 10; // b 的类型是 volatile int
```

限定符和类型的顺序没有限制。例如：
```cpp
int const a = 5; // a 的类型是 const int
int volatile b = 10; // b 的类型是 volatile int
```

两种限定符可以同时使用，例如：
```cpp
const volatile int c = 15; // c 的类型是 const volatile int
```

限定符必须和类型一起使用，不能单独使用。例如：
```cpp
const d = 20; // [!code error] // 错误，缺少类型
```

## const 限定符

`const` 限定的类型的对象不能被修改。例如：
```cpp
const int a = 5;
a = 10; // [!code error] // 错误，a 是 const 类型，不能被修改
```

但是，只要不修改 `const` 限定的对象，可以在任何表达式里使用这个对象。例如：
```cpp
const int a = 5;
int b = (a + 5) * 2; // b 被初始化为 20 
```

前面介绍[声明](../02-program-structure/declaration.md)的部分中提到过，`int` 这样的对象，如果声明时没有提供初始化器，那么这个对象的值是不确定的，但是之后可以修改这个对象的值。而 `const int` 类型的对象，声明后就无法被修改了。

于是，C++ 规定，`const` 限定的对象必顺提供初始化器，否则会导致编译错误。例如：
```cpp
const int b; // [!code error] // 错误，缺少初始化器
```

通过 `auto` 从 `const` 限定的对象中推导类型时，`auto` 会忽略 `const` 限定符。例如：
```cpp
const int c = 5;
auto d = c; // d 的类型是 int，而不是 const int
```

可以给 `auto` 额外添加 `const` 限定符，以推导出有 `const` 限定的对象。例如：
```cpp
int e = 5;
const auto f = e; // f 的类型是 const int
```

::: info 常量和变量
因为 `const` 带来的不能被修改的性质，类型有 `const` 限定的对象往往被称为常量。相对地，没有被 `const` 限定的对象往往被称为变量。但技术性地说，`const` 限定的对象也是变量，只是不能被修改。当看到一个对象声明为 `const int a = 1;` 时，我们可以说 `a` 是一个常量，也可以说 `a` 是一个变量，或者说 `a` 是一个常变量，这都是正确的，这些说法并不冲突。
:::

## volatile 限定符

对于 `volatile` 限定的类型的对象，编译器认为其值可能在任何时候被修改。具体而言，类型有 `volatile` 限定的对象的读写都被视作有副作用（普通的对象只有写视为副作用），因此当访问 `volatile` 限定的对象值时，程序总是会从对象的内存位置得到值，而不能简化。

这个限定符通常用于硬件编程场景。典型情况例如：

- 在电路板级别的开发中，经常会将传感器等设备的输出数据映射到内存中——例如某个内存区域是测量的电压值转换来的。此时，数据的值可能会在任何时候被修改。这时，就需要使用 `volatile` 限定符来声明这些数据。
- 在单片机开发中，常常需要利用处理器提供的硬中断机制来处理外部事件（例如按下按键）。这种中断事件中被修改的对象就需要使用 `volatile` 限定符。
- 除了上面的处理器提供的硬中断机制以外，操作系统常常也会提供软中断。软中断的处理函数也可能会修改数据，这时需要使用 `volatile` 限定符。

上面提到的这些情况的应用需要一些目前尚未介绍的知识，这里是为了介绍 `volatile` 限定符，帮助读者对此有一些基本概念。

::: info volatile 与多线程
在编程漫长的发展中，各种多线程技术涌现又被淘汰，程序员们对 `volatile` 的理解逐渐产生了一种奇妙的差异。在 `x86` 架构下，对一些足够小且满足对齐的对象，添加 `volatile` 就可以令这样的对象得到一定程度上的多线程保证。而在 `java` 中，`volatile` 则直接写明了用来保证多线程之间的可见性。由于这两个语境广泛地出现在各种编程交流中，程序员们便逐渐开始将 `volatile` 与多线程直接联系起来。

实际上，C/C++ 中的 `volatile` 本身不具有多线程的语义，这要等到我们介绍 C++ 的多线程机制时再详细讨论。在这里，读者只需要记住，使用 `volatile` 来进行多线程编程是不足够的。
:::
