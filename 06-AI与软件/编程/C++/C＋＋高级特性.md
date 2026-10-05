C++ 是一种功能丰富的编程语言，除了基本语法和面向对象编程的特性外，还包括许多高级特性，这些特性使得 C++ 在编写高效、灵活和可维护的代码方面具有强大的能力。以下是一些 C++ 的高级特性详细解释：

### 1. 模板

#### 函数模板
函数模板使函数可以处理不同的数据类型。

```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

int main() {
    std::cout << "Int: " << add(3, 4) << std::endl;       // 输出: Int: 7
    std::cout << "Double: " << add(3.5, 4.5) << std::endl; // 输出: Double: 8
    return 0;
}
```

#### 类模板
类模板使类可以处理不同的数据类型。

```cpp
template <typename T>
class Box {
private:
    T value;

public:
    Box(T v) : value(v) {}
    T getValue() { return value; }
};

int main() {
    Box<int> intBox(123);
    Box<double> doubleBox(456.78);
    std::cout << "Int Box: " << intBox.getValue() << std::endl;         // 输出: Int Box: 123
    std::cout << "Double Box: " << doubleBox.getValue() << std::endl;   // 输出: Double Box: 456.78
    return 0;
}
```

### 2. 异常处理

异常处理机制用于捕获和处理运行时错误。

```cpp
#include <iostream>
#include <stdexcept>

int divide(int a, int b) {
    if (b == 0) {
        throw std::runtime_error("Division by zero");
    }
    return a / b;
}

int main() {
    try {
        std::cout << divide(10, 2) << std::endl; // 输出: 5
        std::cout << divide(10, 0) << std::endl; // 抛出异常
    } catch (const std::runtime_error& e) {
        std::cerr << "Error: " << e.what() << std::endl; // 输出: Error: Division by zero
    }
    return 0;
}
```

### 3. 智能指针

智能指针是 C++ 标准库的一部分，用于自动管理动态内存，防止内存泄漏。常用的智能指针包括 `std::unique_ptr`、`std::shared_ptr` 和 `std::weak_ptr`。

#### `std::unique_ptr`
一个指针只能有一个所有者。

```cpp
#include <memory>
#include <iostream>

int main() {
    std::unique_ptr<int> p1 = std::make_unique<int>(42);
    std::cout << *p1 << std::endl; // 输出: 42

    // std::unique_ptr<int> p2 = p1; // 错误：不能复制 unique_ptr
    std::unique_ptr<int> p2 = std::move(p1); // 通过 move 转移所有权
    std::cout << *p2 << std::endl; // 输出: 42
    return 0;
}
```

#### `std::shared_ptr`
一个指针可以有多个所有者，引用计数自动管理内存。

```cpp
#include <memory>
#include <iostream>

int main() {
    std::shared_ptr<int> p1 = std::make_shared<int>(42);
    std::cout << *p1 << std::endl; // 输出: 42

    std::shared_ptr<int> p2 = p1; // 复制 shared_ptr，共享所有权
    std::cout << *p2 << std::endl; // 输出: 42
    std::cout << "Use count: " << p1.use_count() << std::endl; // 输出: Use count: 2
    return 0;
}
```

#### `std::weak_ptr`
用于解决 shared_ptr 循环引用问题，不增加引用计数。

```cpp
#include <memory>
#include <iostream>

class Node {
public:
    std::shared_ptr<Node> next;
    std::weak_ptr<Node> prev; // 使用 weak_ptr 打破循环引用
    Node() { std::cout << "Node created" << std::endl; }
    ~Node() { std::cout << "Node destroyed" << std::endl; }
};

int main() {
    std::shared_ptr<Node> node1 = std::make_shared<Node>();
    std::shared_ptr<Node> node2 = std::make_shared<Node>();
    node1->next = node2;
    node2->prev = node1; // 使用 weak_ptr
    return 0;
}
```

### 4. Lambda 表达式

Lambda 表达式是匿名函数，常用于简化代码。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};

    // 使用 Lambda 表达式打印元素
    std::for_each(vec.begin(), vec.end(), [](int x) {
        std::cout << x << " ";
    });
    std::cout << std::endl; // 输出: 1 2 3 4 5

    // 使用 Lambda 表达式计算和
    int sum = 0;
    std::for_each(vec.begin(), vec.end(), [&sum](int x) {
        sum += x;
    });
    std::cout << "Sum: " << sum << std::endl; // 输出: Sum: 15

    return 0;
}
```

### 5. 标准库（STL）

#### 容器
STL 包括多种容器，如 `vector`、`list`、`map`、`set` 等。

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <set>

int main() {
    // vector 示例
    std::vector<int> vec = {1, 2, 3, 4, 5};
    vec.push_back(6);
    for (int v : vec) {
        std::cout << v << " "; // 输出: 1 2 3 4 5 6
    }
    std::cout << std::endl;

    // map 示例
    std::map<std::string, int> ages;
    ages["Alice"] = 30;
    ages["Bob"] = 25;
    for (const auto& pair : ages) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }
    // 输出:
    // Alice: 30
    // Bob: 25

    // set 示例
    std::set<int> s = {1, 2, 3, 4, 5};
    s.insert(6);
    for (int v : s) {
        std::cout << v << " "; // 输出: 1 2 3 4 5 6
    }
    std::cout << std::endl;

    return 0;
}
```

#### 算法
STL 提供了丰富的算法，如排序、查找、复制、替换等。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {5, 2, 8, 1, 3};

    // 排序
    std::sort(vec.begin(), vec.end());
    for (int v : vec) {
        std::cout << v << " "; // 输出: 1 2 3 5 8
    }
    std::cout << std::endl;

    // 查找
    auto it = std::find(vec.begin(), vec.end(), 3);
    if (it != vec.end()) {
        std::cout << "Found: " << *it << std::endl; // 输出: Found: 3
    } else {
        std::cout << "Not Found" << std::endl;
    }

    return 0;
}
```

### 6. 多线程支持

C++11 引入了多线程支持，可以使用 `std::thread` 类创建和管理线程。

```cpp
#include <iostream>
#include <thread>

void printMessage(const std::string& message) {
    std::cout << message << std::endl;
}

int main() {
    // 创建线程
    std::thread t1(printMessage, "Hello from thread 1");
    std::thread t2(printMessage, "Hello from thread 2");

    // 等待线程完成
    t1.join();
    t2.join();

    return 0;
}
```

### 7. 右值引用和移动语义

C++11 引入了右值引用和移动语义，以提高性能和资源管理效率。

#### 右值引用
右值引用用于绑定临时对象，使用 `&&` 表示。

```cpp
#include <iostream>
#include <vector>

void process(std::vector<int>& vec) {
    std::cout << "Lvalue reference" << std::endl;
}

void process(std::vector<int>&& vec) {
    std::cout << "Rvalue reference" << std::endl;
}

int main() {
    std::vector<int> v = {1, 2, 3,

 4, 5};
    process(v);               // 输出: Lvalue reference
    process(std::move(v));    // 输出: Rvalue reference
    return 0;
}
```

#### 移动构造函数和移动赋值运算符
通过实现移动构造函数和移动赋值运算符，可以有效地转移资源。

```cpp
#include <iostream>
#include <vector>

class Example {
private:
    std::vector<int> data;

public:
    Example(const std::vector<int>& d) : data(d) {}

    // 移动构造函数
    Example(std::vector<int>&& d) : data(std::move(d)) {
        std::cout << "Move constructor" << std::endl;
    }

    // 移动赋值运算符
    Example& operator=(std::vector<int>&& d) {
        if (this != &d) {
            data = std::move(d);
            std::cout << "Move assignment operator" << std::endl;
        }
        return *this;
    }
};

int main() {
    std::vector<int> v = {1, 2, 3, 4, 5};
    Example e1(v);             // 使用拷贝构造函数
    Example e2(std::move(v));  // 输出: Move constructor
    e1 = std::move(v);         // 输出: Move assignment operator
    return 0;
}
```

这些高级特性使得 C++ 成为一门功能强大且灵活的编程语言，适用于各种应用场景。通过掌握这些特性，你可以编写高效、可靠和可维护的 C++ 程序。