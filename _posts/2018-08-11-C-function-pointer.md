---
layout: post
title: 'C语言函数指针小记'
date: 2018-08-05
author: naykun
cover: ''
tags: C/C++
---

好久不使用C语言写底层代码，最近学习OS又要捡起这个古老而神奇的工具了。

这里温习一下C语言的函数指针的用法

例子引自[Basics of Function Pointers in C](https://denniskubes.com/2013/03/22/basics-of-function-pointers-in-c/)

## 函数指针的一般用法

```c
#include <stdio.h>

//函数原型
void sayHello();

//函数实现
void sayHello(){
    printf('hello world');
}

int main(){
    //普通调用
    sayHello();
    //函数指针调用
    void (*sayHelloPtr)() = sayHello;
    //或： void (*sayHelloPtr)() = &sayHello;
    
    (*sayHelloPtr)();
    //或： sayHelloPrt();
}
```
注意函数指针调用部分

`void (*sayHelloPtr)()` 这部分比较难以理解，有以下几点分析


- 关键字void创建了一个函数指针，并让它指向一个返回void的函数。
- sayHelloPtr被当作这个函数指针的名称
- 用*符号来表示这是一个指针，与声明一个指向整数或者字符的指针没有区别
- `*sayHelloPtr`两端的括号是必须的，否则，上述声明变成`void *sayHelloPtr()`，`*`会优先跟`void`结合，变成了一个返回指向`void指针`的普通函数的声明。因此，**函数指针声明的时候不要忘记加上括号**，这非常关键。
- 参数列表紧跟在指针名之后，这个例子中由于没有参数，所以是一对空括号()。
- 将上述要点结合起来，void (*syaHelloPtr)()的意义就非常清楚了，这是一个函数指针，它指向一个不接收参数且没有返回值的函数。

`void (*sayHelloPtr)() = sayHello`将`sayHello`这个函数名赋给了新建的函数指针。函数名代表函数的地址，并且可以赋值给函数指针。这与语句`int *x = &myint;`中把myint的地址赋给一个指向整数的指针一样。只是当考虑函数的时候，不需要加上取地址符&（似乎加上也可以）。简而言之，函数名就是它的地址。接着用代码`(*sayHelloPtr)();`解引用并调用了函数指针。

- 在函数指针被声明之后，sayHelloPtr作为函数指针的名称，跟其他任何指针没有差别，能够储值和赋值。
- 对sayHelloPtr解引用的方式也与其他任何指针一样，即在指针之前使用解引用符`*`，也就是代码中的`*sayHelloPtr`。
- 同样的，需要在其两端加上括号，即`(*sayHelloPtr)`，否则它就不被当做一个函数指针。**声明和解引用的时候都要在两端加上括号**。
- 括号操作符用于C语言中的函数调用，如果有参数参与，就将其放入括号中。这对于函数指针也是相似的，即代码中的`(*sayHelloPtr)()`。
- 函数如果有返回值，可以直接将它赋值给变量。单独来说，这个调用跟直接使用函数名或指针名调用相同。

## 将函数指针作为参数传递

```c
#include <stdio.h>
 
// 函数原型
int add(int x, int y);
int subtract(int x, int y);
int domath(int (*mathop)(int, int), int x, int y);
 
// 加法 x + y
int add(int x, init y) {
    return x + y;
}
 
// 减法 x - y
int subtract(int x, int y) {
    return x - y;
}
 
// 根据输入执行函数指针
int domath(int (*mathop)(int, int), int x, int y) {
    return (*mathop)(x, y);
}
 
// main函数调用
int main() {
 
    // 用加法调用domath
    int a = domath(add, 10, 2);
    printf("Add gives: %d\n", a);
    
    // 用减法调用domath
    int b = domath(subtract, 10, 2);
    printf("Subtract gives: %d\n", b);
}
```

- 有两个特征相同的函数，`add`和`subtract`，它们都返回一个整数并接受两个整数作为参数。
- 在第六行，定义了函数`int domath(int (*mathop)(int, int), int x, int y)`。第一个参数`int (*mathop)(int, int)`是一个函数指针，指向返回一个整数并接受两个整数作为参数的函数。这是一个接受一个函数指针和两个整数作为参数的函数。
- 19到21行，`domath`函数将自己的后两个整数参数传递给函数指针并调用它。也可以像这么调用。`mathop(x, y)`;
- 27到31行。用函数名作为参数调用了domath函数。

## 函数名和地址

```c
#include <stdio.h>
 
// 函数原型
void add(char *name, int x, int y);
 
// 加法 x + y
void add(char *name, int x, int y) {
    printf("%s gives: %d\n", name, x + y);
}
 
// main函数调用
int main() {
 
    // 一些糟糕的函数指针赋值
    void (*add1Ptr)(char*, int, int) = add;
    void (*add2Ptr)(char*, int, int) = *add;
    void (*add3Ptr)(char*, int, int) = &add;
    void (*add4Ptr)(char*, int, int) = **add;
    void (*add5Ptr)(char*, int, int) = ***add;
 
    // 仍然能够正常运行
    (*add1Ptr)("add1Ptr", 10, 2);
    (*add2Ptr)("add2Ptr", 10, 2);
    (*add3Ptr)("add3Ptr", 10, 2);
    (*add4Ptr)("add4Ptr", 10, 2);
    (*add5Ptr)("add5Ptr", 10, 2);
 
    // 当然，这也能运行
    add1Ptr("add1PtrFunc", 10, 2);
    add2Ptr("add2PtrFunc", 10, 2);
    add3Ptr("add3PtrFunc", 10, 2);
    add4Ptr("add4PtrFunc", 10, 2);
    add5Ptr("add5PtrFunc", 10, 2);
}
```

运行这段代码，会看到每个函数指针都会执行，只是会收到一些关于字符转换的警告。但是，这些函数指针都能正常工作。

- 在第15行，add作为函数名，返回这个函数的地址，它被隐式的转换为一个函数指针。在函数指针被要求当作输入的地方，能够直接使用函数名。
- 在第16行，解引用符作用于add之前，即*add，在返回在这个地址的函数。之后跟函数名一样，它被隐式的转换为一个函数指针。
- 在第17行，取地址符作用于add之前，即&add，返回这个函数的地址，之后又得到一个函数指针。
- 18到19行，add不断地解引用自身，不断返回函数名，并被转换为函数指针。到最后，它们的结果都和函数名没有区别。

显然，这段代码不是优秀的实例代码。我们从中收获到了如下知识：
- 其一，函数名会被隐式的转换为函数指针，就像作为参数传递的时候，数组名被隐式的转换为指针一样。在函数指针被要求当作输入的任何地方，都能够使用函数名。
- 其二，解引用符*和取地址符&用在函数名之前基本上都是多余的。

## 小结
函数指针是C语言中的强大工具，基于函数指针，可以实现回调和基本的面向对象功能，后者也是本次回顾函数指针的原因。

## Reference
- [C语言函数指针基础](http://blog.jobbole.com/44639/)
- [Basics of Function Pointers in C](https://denniskubes.com/2013/03/22/basics-of-function-pointers-in-c/)
