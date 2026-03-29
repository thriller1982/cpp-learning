# 类型转换(type_conversions)

## 核心概念

- 类型转换有隐式和显式两类。
- 隐式转换很方便，但也最容易悄悄改变程序含义。
- `signed` 和 `unsigned` 混算是高频陷阱。
- 列表初始化可以帮助你在编译期挡住很多危险窄化。

## 隐式转换的常见来源

### 1. 赋值和初始化

```cpp
double d = 10; // int -> double
int i = 3;     // 表面没问题，但本质上依然发生了类型匹配
```

如果写成：

```cpp
int i = 3.14;
```

那就会丢掉小数部分。教学上必须知道这种写法存在，但实际写代码时应该避免把这种“丢信息”的行为写得悄无声息。

### 2. 整型提升

```cpp
short s1 = 10;
short s2 = 20;
auto result = s1 + s2;
```

这里 `result` 的类型通常是 `int`，因为 `short` 在参与算术运算前会先提升为 `int`。

## `signed` 与 `unsigned` 混算

```cpp
int signed_val = -1;
unsigned int unsigned_val = 1;

bool result = signed_val < unsigned_val;
```

直觉上你可能会觉得这是 `true`，但实际上经常是 `false`。原因是：

- 混算时，`signed_val` 往往会被先转成 `unsigned int`
- `-1` 的位模式一旦按无符号解释，就会变成一个非常大的数

这就是为什么循环、容器下标、`size()` 比较经常会出现诡异 bug。

## 窄化转换

窄化的本质：把“大范围 / 高精度”的值塞进“小范围 / 低精度”的类型里。

危险示例：

```cpp
int a = 3.14;    // 小数被截掉
char c = 1000;   // 超出范围
```

更推荐的写法：

```cpp
int safe{42};
int explicit_value = static_cast<int>(3.14);
```

这里的 `static_cast<int>(3.14)` 不是说它“更安全”，而是它把“我就是要做这个转换”明确写出来了。

## 列表初始化的价值

```cpp
int a{42};
// int bad{3.14};   // 编译器直接拒绝
```

列表初始化最大的好处不是语法好看，而是它会帮助你尽早暴露不合理的隐式窄化。

## 易错点

- 低估隐式转换带来的信息丢失。
- 忘记小整型运算前会先做整型提升。
- 把 `signed` 和 `unsigned` 放进同一个比较表达式里。
- 误以为“能编译”就代表“转换合理”。
