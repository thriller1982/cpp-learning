# 字面值常量(literals)

## 核心概念

- 字面值是源码里直接写死的值。
- 进制写法、后缀和引号形式都会影响字面值的类型。
- 掌握字面值，就是掌握“程序从一开始拿到的是什么类型的数据”。

## 整数字面值

### 1. 不同进制

```cpp
int dec = 123;
int oct = 0123;
int hex = 0x7B;
int bin = 0b01111011;
```

注意：

- `0123` 是八进制，不是“前面补零的十进制”。
- `0x` / `0X` 表示十六进制。
- `0b` / `0B` 表示二进制（C++14 起）。

### 2. 后缀控制类型

```cpp
auto normal = 42;
auto u_val = 42U;
auto l_val = 42L;
auto ull_val = 42ULL;
```

常见后缀：

- `U` / `u`: `unsigned`
- `L` / `l`: `long`
- `LL`: `long long`
- 可以组合使用，如 `ULL`

## 浮点字面值

```cpp
auto d_val = 3.14;   // double
auto f_val = 3.14f;  // float
auto ld_val = 3.14L; // long double
auto sci = 3.14e2;   // 科学计数法
```

重点：

- 只要写了小数点，默认就是 `double`。
- `f` 后缀才会得到 `float`。

## 字符与字符串字面值

```cpp
char c = 'a';
const char* c_str = "abc";
std::string cpp_str = "abc";
```

关键点：

- 单引号是字符字面值。
- 双引号是字符串字面值。
- `"abc"` 的底层类型不是 `std::string`，而是带结尾 `\0` 的字符数组。

C++14 还支持字符串后缀：

```cpp
using namespace std::string_literals;
auto s = "hello"s; // 直接得到 std::string
```

## 转义序列

```cpp
"\n"  // 换行
"\t"  // 制表符
"\\" // 反斜杠
"\"" // 双引号
```

## 原始字符串字面值

```cpp
std::string path = R"(C:\Program Files\MyGame\Data)";
```

适合：

- Windows 路径
- 正则表达式
- 多行文本
- HTML / JSON / SQL 片段

它的核心价值是：少转义、可读性高。

## 易错点

- 把 `0123` 当十进制看。
- 忘记 `3.14` 默认是 `double`。
- 误以为双引号直接得到 `std::string`。
- 需要原样文本时还在手写大量反斜杠转义。
