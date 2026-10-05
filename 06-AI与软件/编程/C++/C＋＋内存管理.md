C++ 的内存管理是一个关键的概念，对于编写高效和稳定的程序至关重要。以下是一些内存管理的实例，涵盖了动态内存分配、智能指针、内存泄漏检测等方面。

### 1. 动态内存分配和释放

#### 使用 `new` 和 `delete`

`new` 操作符用于在堆上分配内存，`delete` 操作符用于释放内存。

```cpp
#include <iostream>

int main() {
    // 分配单个整数
    int* p = new int(5);
    std::cout << "Value: " << *p << std::endl; // 输出: Value: 5
    delete p; // 释放内存

    // 分配数组
    int* arr = new int[5]{1, 2, 3, 4, 5};
    for (int i = 0; i < 5; ++i) {
        std::cout << arr[i] << " "; // 输出: 1 2 3 4 5
    }
    std::cout << std::endl;
    delete[] arr; // 释放数组内存

    return 0;
}
```

#### 使用 `malloc` 和 `free`

C 风格的动态内存分配函数 `malloc` 和 `free` 也可以在 C++ 中使用。

```cpp
#include <iostream>
#include <cstdlib> // 包含 malloc 和 free

int main() {
    // 分配单个整数
    int* p = (int*)malloc(sizeof(int));
    if (p == nullptr) {
        std::cerr << "Memory allocation failed" << std::endl;
        return 1;
    }
    *p = 5;
    std::cout << "Value: " << *p << std::endl; // 输出: Value: 5
    free(p); // 释放内存

    // 分配数组
    int* arr = (int*)malloc(5 * sizeof(int));
    if (arr == nullptr) {
        std::cerr << "Memory allocation failed" << std::endl;
        return 1;
    }
    for (int i = 0; i < 5; ++i) {
        arr[i] = i + 1;
        std::cout << arr[i] << " "; // 输出: 1 2 3 4 5
    }
    std::cout << std::endl;
    free(arr); // 释放数组内存

    return 0;
}
```

### 2. 智能指针

智能指针自动管理动态内存，防止内存泄漏。常用的智能指针包括 `std::unique_ptr`、`std::shared_ptr` 和 `std::weak_ptr`。

#### `std::unique_ptr`

`std::unique_ptr` 是一种独占所有权的智能指针，不能复制，但可以移动。

```cpp
#include <iostream>
#include <memory>

int main() {
    // 创建 unique_ptr
    std::unique_ptr<int> p1 = std::make_unique<int>(5);
    std::cout << "Value: " << *p1 << std::endl; // 输出: Value: 5

    // std::unique_ptr<int> p2 = p1; // 错误：不能复制 unique_ptr
    std::unique_ptr<int> p2 = std::move(p1); // 转移所有权
    if (!p1) {
        std::cout << "p1 is now nullptr" << std::endl; // 输出: p1 is now nullptr
    }
    std::cout << "Value: " << *p2 << std::endl; // 输出: Value: 5

    return 0;
}
```

#### `std::shared_ptr`

`std::shared_ptr` 是一种共享所有权的智能指针，多个 `shared_ptr` 可以共享同一块内存。

```cpp
#include <iostream>
#include <memory>

int main() {
    // 创建 shared_ptr
    std::shared_ptr<int> p1 = std::make_shared<int>(5);
    std::cout << "Value: " << *p1 << std::endl; // 输出: Value: 5

    std::shared_ptr<int> p2 = p1; // 共享所有权
    std::cout << "Use count: " << p1.use_count() << std::endl; // 输出: Use count: 2

    return 0;
}
```

#### `std::weak_ptr`

`std::weak_ptr` 用于解决 `shared_ptr` 循环引用问题，不增加引用计数。

```cpp
#include <iostream>
#include <memory>

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
    node2->prev = node1;

    return 0; // 在此处两个 Node 对象都将被正确销毁
}
```

### 3. 内存泄漏检测

#### 使用工具检测内存泄漏

有多种工具可以用于检测内存泄漏，例如 Valgrind（用于 Linux）和 Visual Studio 内存分析器（用于 Windows）。

##### Valgrind 示例

安装 Valgrind 并使用以下命令运行程序：

```sh
valgrind --leak-check=full ./my_program
```

##### Visual Studio 内存分析器

在 Visual Studio 中，可以使用内置的内存分析工具。点击 “Debug” > “Performance Profiler” > “Memory Usage”，然后启动分析。

#### 手动检测内存泄漏

也可以通过代码手动检测内存泄漏，例如使用 `_CrtDumpMemoryLeaks`（仅限 Windows）。

```cpp
#define _CRTDBG_MAP_ALLOC
#include <cstdlib>
#include <crtdbg.h>
#include <iostream>

int main() {
    // 启用内存泄漏检测
    _CrtSetDbgFlag(_CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF);

    // 故意的内存泄漏
    int* p = new int(5);

    // 程序结束时，_CrtDumpMemoryLeaks 会输出未释放的内存
    return 0;
}
```

### 4. 自定义内存管理器

可以通过重载 `new` 和 `delete` 操作符实现自定义内存管理器。

```cpp
#include <iostream>
#include <cstdlib>

void* operator new(size_t size) {
    void* ptr = std::malloc(size);
    if (!ptr) {
        throw std::bad_alloc();
    }
    std::cout << "Allocated " << size << " bytes" << std::endl;
    return ptr;
}

void operator delete(void* ptr) noexcept {
    std::cout << "Deallocated memory" << std::endl;
    std::free(ptr);
}

int main() {
    int* p = new int(5); // 输出: Allocated 4 bytes
    delete p;            // 输出: Deallocated memory

    return 0;
}
```

通过理解和应用这些内存管理技术，可以编写更高效、稳定的 C++ 程序，避免常见的内存问题如内存泄漏、悬挂指针等。