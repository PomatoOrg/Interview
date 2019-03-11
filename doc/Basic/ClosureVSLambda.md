# 闭包(Closure) 与 匿名函数(Lambda、Anonymous function)

## 闭包(Closure)

wiki

> 在计算机科学中，闭包（英语：Closure），又称词法闭包（Lexical Closure）或函数闭包（function closures），是引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。所以，有另一种说法认为闭包是由函数和与其相关的引用环境组合而成的实体。闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例。

stackoverflow

> A closure is any function which closes over the environment in which it was defined. This means that it can access variables not in its parameter list.

## 匿名函数(Lambda、Anonymous function)

wiki

> 在计算机编程中，匿名函数（函数文字，lambda 抽象或 lambda 表达式）是未绑定到标识符的函数定义。匿名函数通常是传递给高阶函数的参数，或者用于构造需要返回函数的高阶函数的结果。如果函数仅使用一次或有限次数，则匿名函数在语法上可能比使用命名函数轻。匿名函数在函数式编程语言中无处不在和其他具有第一类函数的语言，它们对函数类型的作用与文字对其他数据类型的作用相同。

stackoverflow

> A lambda is just an anonymous function - a function defined with no name.

## 总结

闭包: 引用(捕获)了外部变量的函数.
该外部变量的生命周期脱离了创造它的环境(外部函数、作用域).因为闭包内部强引用了此变量,导致内存延迟被销毁

匿名函数: 没有函数名的函数.匿名函数一般作为参数传递.

它们都是函数,但是前面的定语不一样.

**假设有一个函数没有函数名,并且引用了外部变量,所以它是闭包、又是匿名函数**

## 炫技时间

### Objective-C(Block)

在 Objective-C 统一称之为 Block

```objective-C
void(^block1)(void)=^{
    // 匿名函数,赋值给一个叫block1的变量
};

block1();


NSString *message = @"一个外部字符串";

void(^block2)(void)=^{
    // 闭包,引用了外部的变量
    NSLog(@"%@",message);
};

block2();
```
### Java

JDK 8 开始支持 Lambda

```java
Runnable runnable = ()->{
  // 没有入参和返回值的 Lambda,也没有引用外部变量
};
runnable.run();
```

### Go

```go
foo := func(x int) int {
  return x * x
}
fmt.Println(foo(10))
```

### Python

```python
foo = lambda x: x*x
print(foo(10))
```

## 参考资料

[en-wiki-闭包-closure]

[cn-wiki-闭包-closure]

[What is the difference between a 'closure' and a 'lambda'?](https://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)

[En-wifi-匿名函数](https://en.wikipedia.org/wiki/Anonymous_function)

[阮一峰-学习 Javascript 闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)

[en-wiki-闭包-closure]: https://en.wikipedia.org/wiki/Closure_(computer_programming)
[cn-wiki-闭包-closure]: https://zh.wikipedia.org/wiki/%E9%97%AD%E5%8C%85_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)
