C++ 是一种广泛应用的编程语言，具有丰富的特性和功能。以下是一些关键的 C++ 知识点：

### [[C＋＋基本语法]]
1. **变量和数据类型**：C++ 支持多种数据类型，如整数（`int`）、浮点数（`float`、`double`）、字符（`char`）、布尔（`bool`）等。变量必须先声明后使用。
2. **运算符**：包括算术运算符（`+`、`-`、`*`、`/`、`%`）、比较运算符（`==`、`!=`、`>`、`<`、`>=`、`<=`）、逻辑运算符（`&&`、`||`、`!`）等。
3. **控制结构**：包括条件语句（`if`、`else if`、`else`）、循环语句（`for`、`while`、`do-while`）、选择语句（`switch-case`）等。

### [[C＋＋面向对象编程]]
1. **类和对象**：C++ 是面向对象的编程语言，类是对象的蓝图，对象是类的实例。
2. **封装**：使用访问修饰符（`public`、`protected`、`private`）来控制类成员的访问权限。
3. **继承**：通过继承机制，子类可以继承父类的属性和方法，增强代码的重用性。
4. **多态**：通过函数重载和虚函数实现多态性，使得相同的函数名可以表现出不同的行为。

### [[C＋＋高级特性]]
1. **模板**：C++ 支持函数模板和类模板，用于实现泛型编程。
2. **异常处理**：使用 `try`、`catch` 和 `throw` 关键字来处理异常情况。
3. **STL（标准模板库）**：包括常用的容器（如 `vector`、`list`、`map`、`set`）、算法（如排序、搜索）和迭代器。

### [[C＋＋内存管理]]
1. **动态内存分配**：使用 `new` 和 `delete` 操作符在堆上分配和释放内存。
2. **指针和引用**：指针用于存储变量的内存地址，引用是变量的别名，便于直接操作对象。

### [[C＋＋输入输出]]
1. **标准输入输出**：使用 `cin` 和 `cout` 进行输入和输出操作。
2. **文件操作**：通过 `ifstream` 和 `ofstream` 类进行文件的读写操作。

### [[C++11、C++14、C++17 和 C++20 新特性]]
1. **自动类型推导（auto）**：自动推断变量的类型，简化代码。
2. **Lambda 表达式**：用于定义匿名函数。
3. **智能指针**：包括 `shared_ptr`、`unique_ptr` 和 `weak_ptr`，用于自动管理动态内存。
4. **多线程支持**：通过 `thread` 类和相关库提供对多线程编程的支持。

### 示例代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <memory>

class Base {
public:
    virtual void show() {
        std::cout << "Base class" << std::endl;
    }
};

class Derived : public Base {
public:
    void show() override {
        std::cout << "Derived class" << std::endl;
    }
};

int main() {
    // 基本输入输出
    std::cout << "Hello, C++!" << std::endl;

    // 动态内存管理
    int* p = new int(10);
    std::cout << "Value: " << *p << std::endl;
    delete p;

    // 使用智能指针
    std::shared_ptr<int> sp = std::make_shared<int>(20);
    std::cout << "Shared pointer value: " << *sp << std::endl;

    // STL 容器和算法
    std::vector<int> vec = {1, 2, 3, 4, 5};
    std::sort(vec.begin(), vec.end(), std::greater<int>());
    for (int v : vec) {
        std::cout << v << " ";
    }
    std::cout << std::endl;

    // 多态示例
    Base* b = new Derived();
    b->show();
    delete b;

    return 0;
}
```

这些知识点涵盖了 C++ 编程的基础和一些高级特性，掌握这些内容将有助于你编写高效、可靠的 C++ 程序。