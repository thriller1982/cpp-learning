# 命名空间的 using 声明(using_declarations)

## 核心概念

- `using` 声明可以把命名空间里的某个名字引入当前作用域。
- 标准库名字大多位于 `std` 中，比如 `std::string` 和 `std::vector`。
- `using std::name;` 比 `using namespace std;` 更克制，也更安全。
- 头文件里通常不应该随手写大范围的 `using`。

## 为什么会看到 `std::`

```cpp
std::string s = "hello";
std::vector<int> nums{1, 2, 3};
```

这里的 `std::` 不是语法噪音，而是在告诉编译器：

- `string` 这个名字来自 `std`
- `vector` 这个名字也来自 `std`

如果不写作用域，编译器就要去当前作用域里找同名标识符。

## `using` 声明的写法

```cpp
using std::cin;
using std::cout;
using std::string;

string s;
cin >> s;
cout << s;
```

这种写法的重点是“只引入你要用的那个名字”，而不是把整个命名空间一口气倒进来。

## `using namespace` 为什么更危险

```cpp
using namespace std;
```

这行代码会把 `std` 里的很多名字都暴露到当前作用域。

短程序里它看起来方便，但一旦代码变大，就更容易出现：

- 名字冲突
- 来源不清楚
- 头文件污染调用方作用域

更稳妥的做法通常是：

```cpp
using std::string;
using std::vector;
```

## 头文件里为什么要更谨慎

如果你在头文件里写：

```cpp
using namespace std;
```

那所有包含这个头文件的源文件，都会被迫接收这批名字。

这不是“我自己图方便”，而是把名字污染扩散给整个工程。所以头文件里一般应避免这种写法。

## 易错点

- 误以为 `using std::string;` 和 `using namespace std;` 风险一样。
- 在头文件里直接写 `using namespace std;`。
- 只记住“少写 `std::`”，却忽略了名字冲突和作用域污染。
