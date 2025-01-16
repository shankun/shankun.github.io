+++
title = "访客模式"
date = 2025-01-16T17:09:00+08:00
draft = false

[taxonomies]
categories = ["学而时习之"]
tags = ["设计模式", "C++"]

[extra]
lang = "zh_CN"
toc = false
copy = true
comment = false
math = false
mermaid = false
outdate_alert = false
outdate_alert_days = 120
display_tags = true
truncate_summary = false
+++

<!--more-->
访客模式（Visitor Pattern）是一种常见的技术，它可以在不对大量可能无关的类进行实际修改的情况下，为这些类添加特定的功能。
如果您在网上搜索 C++ 中这种设计模式的示例，您可能会发现一些示例中的抽象 Visitor 基类具有一个重载的纯虚函数，通常称为 accept()。
本文将探讨如何使用编译时多态性和最新的 C++ 语法完成大致相同的任务。

首先，我们需要定义 Visitor 基类和类型推导指南（在 C++20 模式下，MSVC 或 g++ 似乎不需要后者，但在 C++17 模式下需要）：
```c++
template<typename... Base>
struct Visitor : Base... {
    using Base::operator()...;
};
 
template<typename...T> Visitor(T...) -> Visitor<T...>;
```

如果你对模板类的理解还不能解释省略号的用法，也不用担心！省略号在这里的意思是，Visitor 继承自任意数量的基类，并公开它们的（重载）函数调用操作符。
在 C++ 中，拥有重载函数调用操作符的传统含义是 “functor”，但现在，我们通常认为 “callable”等同于“lambda function”。下面是初始化访问者实例的方法：
```c++
constexpr Visitor visitor{
    [](double d){ return d + 3.4; },
    [](int i){ return i / 2.0; }
};
```

如上所示，统一初始化构造函数可以接受任意多个逗号分隔的（纯）lambda 函数，这些函数接受不同类型的参数，从而重载 Visitor 的函数调用操作符。
请注意，每个 lambda 函数的返回类型（显式或隐式）必须相同，否则会产生编译时错误。

使用 std::visit（来自头文件 <variant>）会产生奇妙的效果，它需要两个参数：一个如上定义的访问者和一个与重载调用操作符类型相同的 std::variant。
在下面的示例代码中，我们包含了一个 using 声明，但严格来说，这并不是必须的：
```c++
int main() {
    using Visitable = std::variant<double,int>;
    constexpr auto result1 = std::visit(visitor, Visitable(9.2));
    constexpr auto result2 = std::visit<int>(visitor, Visitable(5));
    std::cout << "result1 = " << result1 << "\nresult2 = " << result2 << '\n';
}
```

std::visit的结果可以应用于一个常量变量，从而证明我们可以在编译时和运行时评估访问者。此外，返回的类型可以作为模板类型参数指定给 std::visit，
因此，即使我们不能返回 i / 2;（作为 int），我们也可以指定应该使用的转置，就像在 std::visit<int>() 中一样。运行这个程序的输出结果是：  
result1 = 12.6  
result2 = 2  

当然，对于用户定义的类型，这种技术的价值更大。下面这个示例程序的灵感来自 Derek Banas 关于访问者模式的 Java 视频教程：
定义了三类货物（酒类、烟草和必需品），并以不同的方式存储和检索它们的成本（double值）。
然后，定义了两个不同的访问者，它们采用不同的征税方案（第一个对酒类征税 50%，对烟草征税 80%，对生活必需品征税为零；第二个对所有物品征税为零）。
请注意，在 TaxFreeVisitor 中使用了一个通用 lambda，作为定义了 double() 运算符的商品的总括。
main()程序将游客依次应用于每种商品，并打印出税后成本：

```c++
#include <iostream>
#include <variant>
 
template<typename... Base>
struct Visitor : Base... {
    using Base::operator()...;
};
 
template<typename...T> Visitor(T...) -> Visitor<T...>;
 
class Liquor {
    double price;
public:
    Liquor(double price) : price{ price } {}
    operator double() const { return price; }
};
 
class Tobacco {
    double item;
public:
    Tobacco(double itemPrice) : item{ itemPrice } {}
    double getItemPrice() const { return item; }
};
 
class Necessity {
    unsigned costInCents;
public:
    Necessity(double cost) : costInCents{ static_cast<unsigned>(cost * 100) } {}
    operator double() const { return costInCents / 100.0; }
};
 
Visitor TaxVisitor{
    [](const Liquor& liquor){ double l = liquor; return l * 1.5; },
    [](const Tobacco& tobacco) { return tobacco.getItemPrice() * 1.8; },
    [](const Necessity& necessity) { double n = necessity; return n * 1.0; }
};
 
Visitor TaxFreeVisitor{
    [](const Tobacco& tobacco) { return tobacco.getItemPrice() * 1.0; },
    [](const auto& any) { double price = any; return price * 1.0; }
};
 
int main() {
    using Visitable = std::variant<Liquor,Tobacco,Necessity>;
    Liquor whiskey{ 25.99 };
    Tobacco cigs{ 13.49 };
    Necessity bread{ 1.29 };
     
    std::cout.precision(2);
    std::cout << "Many Items Taxable\n" << std::fixed;
    std::cout << "  Whiskey:    $" << std::visit(TaxVisitor, Visitable(whiskey)) << '\n';
    std::cout << "  Cigarettes: $" << std::visit(TaxVisitor, Visitable(cigs)) << '\n';
    std::cout << "  Bread:      $" << std::visit(TaxVisitor, Visitable(bread)) << '\n';
 
    std::cout << "All Items Tax-Free\n";
    std::cout << "  Whiskey:    $" << std::visit(TaxFreeVisitor, Visitable(whiskey)) << '\n';
    std::cout << "  Cigarettes: $" << std::visit(TaxFreeVisitor, Visitable(cigs)) << '\n';
    std::cout << "  Bread:      $" << std::visit(TaxFreeVisitor, Visitable(bread)) << '\n';
}
```

运行这个程序的输出结果是：  
```txt
Many Items Taxable
  Whiskey:    $38.98
  Cigarettes: $24.28
  Bread:      $1.29
All Items Tax-Free
  Whiskey:    $25.99
  Cigarettes: $13.49
  Bread:      $1.29
```

上述示例程序试图展示访问者模式在修改类本身不可能或不可取的情况下的使用，例如，在有许多商品和不同税收制度的情况下，需要为每种商品添加一个税收字段并依次修改，
这可能会成为一个令人头疼的维护问题。访问者类的定义与之前的相同，TaxVisitor 和 TaxFreeVisitor 对象是用纯 lambdas 创建的，与我们展示的第一个访问者类相同。
(这里可以使用捕获 lambdas，例如，如果 TaxVisitor 定义在函数内部，则可以避免对税级进行硬编码）。
现在，只需使用少量模板和 C++ 标准库中的 std::visitor，你就可以在自己的 C++ 程序中使用访客模式了。