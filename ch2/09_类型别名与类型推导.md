# 类型别名与类型推导(type_deduction_and_aliases)

## 核心概念

- `using` 和 `typedef` 都能起类型别名。
- `auto` 负责“根据初始化表达式推断类型”。
- `decltype` 负责“按规则提取表达式类型”。
- 这三者在现代 C++ 里会频繁同时出现。

## 类型别名

### 1. 旧写法 `typedef`

```cpp
typedef unsigned long long ull2;
```

### 2. 现代写法 `using`

```cpp
using ull = unsigned long long;
```

更推荐 `using` 的原因：

- 可读性更直接
- 模板别名场景更自然
- 与现代 C++ 习惯一致

## `auto` 类型推断

```cpp
const int ci = 1;
int normal_int = 0;
int& ref_int = normal_int;

auto a = ci;
auto b = ref_int;
```

这段代码最重要的结论：

- `auto` 默认会剥离引用
- `auto` 默认会剥离顶层 `const`

所以：

- `a` 是 `int`
- `b` 也是 `int`

这背后的直觉是：`auto x = expr;` 往往是在“用 `expr` 的值来创建一个新变量 `x`”。既然是新变量，引用这个绑定关系通常不会保留下来，而顶层 `const` 也通常不会继续附着在这个新副本上。

### 怎么解决

如果你不想让 `auto` 把这些信息丢掉，就不要只写裸 `auto`，而要把你想保留的部分显式写出来。

1. 保留引用：写 `auto&`

```cpp
int i = 0;
int& r = i;

auto a = r;   // int
auto& b = r;  // int&
```

2. 保留顶层 `const`：写 `const auto`

```cpp
const int ci = 1;

auto a = ci;        // int
const auto b = ci;  // const int
```

3. 同时保留“引用 + const”：写 `const auto&`

```cpp
const int ci = 1;

const auto& r = ci; // const int&
```

这是最常见也最稳妥的写法，尤其适合“只读地绑定到一个已有对象”。

4. 如果你就是想按原样推导返回类型，可以考虑 `decltype(auto)`

```cpp
int i = 0;
int& r = i;

decltype(auto) x = r; // int&
```

`decltype(auto)` 不是日常变量声明的主力工具，但在你确实需要“不要丢掉引用和修饰”的场景里，比裸 `auto` 更忠实。

可以把它记成一句话：

- 裸 `auto` 适合“我要一个新副本”
- `auto&` / `const auto&` 适合“我要保留绑定关系”
- `decltype(auto)` 适合“我要尽量按原类型规则保留”

## `decltype`

`decltype` 不像 `auto` 那样“按值推一个新类型”，它的规则更机械。直接记下面这组判断规则就行。

### 判断规则

1. 如果参数是一个**不带括号的变量名**，结果就是这个变量声明时的类型。

```cpp
const int ci = 1;
int normal_int = 0;
int& ref_int = normal_int;

decltype(ci) x = 0;         // const int
decltype(normal_int) y = 0; // int
decltype(ref_int) z = normal_int; // int&
```

这里的关键是：`decltype` 会把声明里的类型原样拿出来，所以引用和顶层 `const` 都会保留。

2. 如果参数是一个**表达式**，就看这个表达式的值类别。

- 左值，得到 `T&`
- 将亡值，得到 `T&&`
- 纯右值，得到 `T`

```cpp
int i = 0;

decltype((i)) a = i; // int&，因为 (i) 是左值表达式
decltype(i + 1) b = 0; // int，因为 i + 1 是纯右值
```

3. 只要多套了一层括号，很多时候它就不再是“变量名规则”，而会转入“表达式规则”。

```cpp
int i = 0;
decltype(i) a = 1;   // int
decltype((i)) b = i; // int&
```

总结：

- `decltype(name)`：看声明时的类型
- `decltype((expr))`：看表达式的值类别

`decltype` 的核心是按规则映射类型，不是按“像不像副本”去猜。

## 易错点

- 误以为 `auto` 会自动保留所有修饰。
- 不理解为什么 `decltype((x))` 会变成引用。
- 只记结论，不结合“名字 vs 表达式”来理解推导规则。
