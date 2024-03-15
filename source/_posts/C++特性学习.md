---
title: C++特性学习
tags: 
- C++

---

## const与constexpr

### const

如果 `const` 变量的初始化值是常量表达式，那么它就是**编译时常量**。编译时常量使编译器能够执行非编译时常量无法提供的优化。

```c++
#include <iostream>

int main()
{
    const int x { 3 };  // x 是编译时常量
    const int y { 4 };  // y 是编译时常量

    std::cout << x + y; // x + y 是编译时常量

    return 0;
}
```

当编译器编译时，它可以计算x+y的值，并将其替换为7。

任何用非常量表达式初始化的 `const` 变量都是**运行时常量**。运行时常量是在运行时才知道其初始化值的常量。

```c++
#include <iostream>

int sum(int x, int y) {
    return x + y;
}

int main() {
    const int x{3};
    const int y{4};
    const int z{sum(x, y)}; //运行时常量
    std::cout << x << " " << y << " " << z << "\n";
}
```

### constexpr

当使用 `const` 时，变量最终可能是编译时的 `const` 或运行时的 `const`，这取决于初始化式是否是常量表达式。

`constexpr`变量**只能是编译时常量**。如果 `constexpr` 变量的初始化值不是常量表达式，编译器将出错。

```c++
#include <iostream>

int sum(int x, int y) {
    return x + y;
}

int main() {
    const int x{3};
    const int y{4};
    constexpr int z{sum(x, y)};
    std::cout << x << " " << y << " " << z << "\n";
}
```

![image-20240312171839834](C:\Users\23882\OneDrive\笔记\assets\image-20240312171839834.png)