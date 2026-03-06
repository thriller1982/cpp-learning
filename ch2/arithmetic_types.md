# 算术类型(arithmetic_types)

## 核心概念

- C++ 的算术类型包括整型和浮点型。
- 标准更强调“最小能力”和“相对关系”，不承诺所有平台都长得一样。
- `char` 既能表示字符，也是一种整数类型。
- `unsigned` 溢出是定义良好的回绕，`signed` 溢出则是未定义行为。

## 整型家族

### 1. 布尔型

```cpp
bool ok = true;
```

只有 `true` 和 `false` 两种值，常用于条件判断。

### 2. 字符型

```cpp
char c = 'a';
signed char sc = -10;
unsigned char uc = 255;
```

要点：

- `char` 是否带符号取决于平台。
- 只要你想把字符类型当作“小整数”使用，就不要偷懒写 `char`，而要明确写 `signed char` 或 `unsigned char`。

### 3. 扩展字符型

```cpp
wchar_t wc = L'宽';
char16_t c16 = u'语';
char32_t c32 = U'言';
```

它们主要用于更大的字符集和 Unicode 表达。

### 4. 标准整型

```cpp
short s = 10;
int i = 1000;
long l = 100000L;
long long ll = 10000000LL;
unsigned int ui = 42;
```

需要记住的是相对关系：

```cpp
sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)
```

标准保证的是“能力不下降”，不是“每种类型一定比前一种大一截”。

## 浮点型家族

```cpp
float f = 3.14f;
double d = 3.14159;
long double ld = 3.14159L;
```

经验规则：

- 默认优先用 `double`。
- `float` 常见于内存敏感、图形或接口指定场景。
- `long double` 是否比 `double` 更大，也与实现有关。

## 最重要的三个结论

### 1. 平台相关性

C++ 标准不会保证 `int` 一定是 4 字节，只保证它至少能满足规定的取值能力。

### 2. `char` 的符号性不固定

这是面试和实际移植里都很容易踩的坑。

### 3. 溢出语义不同

`unsigned` 类型的运算是按模进行的。也就是说，当结果超过该类型能表示的最大值时，程序不会出错，而是回到范围起点继续计算。

```cpp
unsigned int u_max = std::numeric_limits<unsigned int>::max();
unsigned int u_wrap = u_max + 1; // 回绕到 0
```

`signed` 整型则不同。对有符号整数做上溢，结果不是“通常会变成最小值”，而是未定义行为。未定义行为的意思是：编译器不保证任何固定结果，你不能依赖它。

```cpp
int s_max = std::numeric_limits<int>::max();
// int s_overflow = s_max + 1; // 未定义行为
```

## 易错点

- 把 `char` 默认当成有符号或无符号来写逻辑。
- 误以为 `int` / `long` / `long long` 的字节数所有平台都一样。
- 依赖有符号溢出的结果。
