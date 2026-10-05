C++ 提供了丰富的输入输出（I/O）功能，主要通过标准库中的流（stream）来实现。这些功能包括从控制台读取输入和向控制台输出信息，以及文件I/O等。以下是C++输入输出的详细介绍。

### 1. 标准输入输出

#### `iostream` 库

`iostream` 库提供了基本的输入输出流，包括 `std::cin`、`std::cout`、`std::cerr` 和 `std::clog`。

##### `std::cout`（标准输出）

`std::cout` 用于向控制台输出数据。

```cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl; // 输出: Hello, World!
    return 0;
}
```

##### `std::cin`（标准输入）

`std::cin` 用于从控制台读取数据。

```cpp
#include <iostream>

int main() {
    int age;
    std::cout << "Enter your age: ";
    std::cin >> age;
    std::cout << "You are " << age << " years old." << std::endl;
    return 0;
}
```

##### `std::cerr`（标准错误）

`std::cerr` 用于输出错误信息，通常不进行缓冲。

```cpp
#include <iostream>

int main() {
    std::cerr << "An error occurred!" << std::endl;
    return 0;
}
```

##### `std::clog`（标准日志）

`std::clog` 用于输出日志信息，通常进行缓冲。

```cpp
#include <iostream>

int main() {
    std::clog << "Log message: Application started." << std::endl;
    return 0;
}
```

### 2. 文件输入输出

#### `fstream` 库

`fstream` 库提供了文件输入输出流，包括 `std::ifstream`（输入文件流）、`std::ofstream`（输出文件流）和 `std::fstream`（文件读写流）。

##### 写入文件

使用 `std::ofstream` 向文件写入数据。

```cpp
#include <iostream>
#include <fstream>

int main() {
    std::ofstream outfile("example.txt");
    if (outfile.is_open()) {
        outfile << "Hello, file!" << std::endl;
        outfile.close();
    } else {
        std::cerr << "Unable to open file for writing." << std::endl;
    }
    return 0;
}
```

##### 读取文件

使用 `std::ifstream` 从文件读取数据。

```cpp
#include <iostream>
#include <fstream>
#include <string>

int main() {
    std::ifstream infile("example.txt");
    if (infile.is_open()) {
        std::string line;
        while (std::getline(infile, line)) {
            std::cout << line << std::endl;
        }
        infile.close();
    } else {
        std::cerr << "Unable to open file for reading." << std::endl;
    }
    return 0;
}
```

##### 读写文件

使用 `std::fstream` 进行文件读写操作。

```cpp
#include <iostream>
#include <fstream>
#include <string>

int main() {
    std::fstream file("example.txt", std::ios::in | std::ios::out | std::ios::app);
    if (file.is_open()) {
        // 追加写入
        file << "Appending a line." << std::endl;

        // 移动到文件开头
        file.seekg(0, std::ios::beg);

        // 读取文件内容
        std::string line;
        while (std::getline(file, line)) {
            std::cout << line << std::endl;
        }
        file.close();
    } else {
        std::cerr << "Unable to open file for read/write." << std::endl;
    }
    return 0;
}
```

### 3. 格式化输入输出

#### `iomanip` 库

`iomanip` 库提供了一些用于格式化输入输出的操作。

##### 设置宽度

使用 `std::setw` 设置字段宽度。

```cpp
#include <iostream>
#include <iomanip>

int main() {
    std::cout << std::setw(10) << "Value" << std::endl;
    std::cout << std::setw(10) << 123 << std::endl;
    return 0;
}
```

##### 设置填充字符

使用 `std::setfill` 设置填充字符。

```cpp
#include <iostream>
#include <iomanip>

int main() {
    std::cout << std::setw(10) << std::setfill('*') << "Value" << std::endl;
    std::cout << std::setw(10) << std::setfill('*') << 123 << std::endl;
    return 0;
}
```

##### 设置浮点数精度

使用 `std::setprecision` 设置浮点数的显示精度。

```cpp
#include <iostream>
#include <iomanip>

int main() {
    double pi = 3.141592653589793;
    std::cout << std::setprecision(5) << pi << std::endl; // 输出: 3.1416
    std::cout << std::setprecision(10) << pi << std::endl; // 输出: 3.141592654
    return 0;
}
```

### 4. 字符串流

#### `sstream` 库

`sstream` 库提供了字符串流类 `std::istringstream`、`std::ostringstream` 和 `std::stringstream`，用于在字符串中进行格式化输入输出。

##### 输出到字符串

使用 `std::ostringstream` 向字符串中输出数据。

```cpp
#include <iostream>
#include <sstream>

int main() {
    std::ostringstream oss;
    oss << "Hello, " << "world!";
    std::string result = oss.str();
    std::cout << result << std::endl; // 输出: Hello, world!
    return 0;
}
```

##### 从字符串输入

使用 `std::istringstream` 从字符串中读取数据。

```cpp
#include <iostream>
#include <sstream>

int main() {
    std::string input = "123 456 789";
    std::istringstream iss(input);
    int a, b, c;
    iss >> a >> b >> c;
    std::cout << "a: " << a << ", b: " << b << ", c: " << c << std::endl; // 输出: a: 123, b: 456, c: 789
    return 0;
}
```

##### 字符串流读写

使用 `std::stringstream` 可以同时进行字符串的读写操作。

```cpp
#include <iostream>
#include <sstream>

int main() {
    std::stringstream ss;
    ss << "123 456 789";
    int a, b, c;
    ss >> a >> b >> c;
    std::cout << "a: " << a << ", b: " << b << ", c: " << c << std::endl; // 输出: a: 123, b: 456, c: 789

    ss.clear(); // 清除状态标志
    ss.str(""); // 清空字符串流
    ss << "New content";
    std::string result = ss.str();
    std::cout << result << std::endl; // 输出: New content
    return 0;
}
```

通过理解和应用这些输入输出功能，可以在 C++ 中实现丰富和灵活的数据处理操作。无论是基本的控制台 I/O 还是文件 I/O，C++ 都提供了强大的工具和库来满足各种需求。