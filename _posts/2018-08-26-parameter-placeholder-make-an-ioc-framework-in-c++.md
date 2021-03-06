---
title:  "C++-使用C++实现依赖注入框架 一、Parameter Placeholder"
date:   2018-08-26 00:00:00
categories: C++
comments: true
---

在其他语言中，我们常用反射机制来实现依赖注入，而C++没有反射，因此，使用C++实现依赖注入就相对麻烦一些了。幸好，我们可以通过元编程来替代反射的功能。

依赖注入最关键的地方在于，获取要注入的类型的构造函数的参数类型，然后再构造这些参数，最后利用这些参数构造要注入的类型。

C++中显然不能直接的获取某个类型的构造函数的参数类型，但是，我们可以通过类型转换来间接的获取。当构造对象或调用函数的时候，编译器会自动尝试将类型不匹配的参数转换为目标类型。假如调用构造对象时，我们让一个类型可以自动转换到所有需要的构造函数参数的类型，那就实现了依赖注入中获取构造函数参数类型的这一步了，示例代码如下：

```c++

struct A
{
    A(int, bool, double, int)
    {

    }
};

// 调用构造函数
A a(1, true, 1.2, 3); 


struct PlaceHolder
{
    // 使用模板，让PlaceHolder可以转换为任意类型
    template <typename  T>
    operator T()
    {
        // 先暂时通过T类型的默认构造函数构造T类型的对象
        return T();
    }
}

PlaceHolder p;

// 依然可以构造，编译器会自动调用 operator T，将PlaceHolder类型转换为T类型
A a(p, p, p, p);

```

这样一来，依赖注入的第一步，获取构造函数的类型，就实现完成了。当需要注入一个类型的时候，只要给这个类型传入符合参数个数的PlaceHolder类型，就可以完成依赖的注入了。