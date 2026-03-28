# string 的定义与操作(string_definition_and_operations)

## 核心概念

- `string` 是标准库提供的可变长字符序列。
- 现代 C++ 处理文本时，通常优先选 `string`，而不是 C 风格字符串。
- `>>` 读到空白就停，`getline` 读取整行。
- `size()` 返回的不是普通 `int`，而是无符号类型。

## 定义与初始化

```cpp
string s1;          // 默认初始化，空串
string s2 = s1;     // 拷贝初始化
string s3 = "hiya"; // 用字面值初始化
string s4("hiya");  // 直接初始化
string s5(10, 'c'); // cccccccccc
```

这里最值得先抓住两点：

- 空字符串是合法对象
- `string s = "abc";` 是拷贝初始化，`string s("abc");` 是直接初始化；对 `string` 来说这两种写法通常都可以

如果想判断是不是空串，可以直接写：

```cpp
if (s1.empty()) {
    // s1 里没有字符
}
```

## 输入输出

```cpp
string word;
cin >> word;
```

这会读取一个“单词”，遇到空格、制表符或换行就结束。

如果你要读整行内容，应当改用：

```cpp
string line;
getline(cin, line);
```

`getline` 的重点不是“另一种输入方式”，而是它保留行内空格。

## 常用操作

```cpp
string s = "hello";

auto len = s.size();
char first = s[0];
string full = s + " world";
bool same = (s == "hello");
```

这些操作里最容易忽略的是 `size()` 的类型。它返回的是：

```cpp
string::size_type
```

这是无符号类型，所以写循环或比较时，不要轻易和负数混在一起。

例如：

```cpp
for (string::size_type i = 0; i != s.size(); ++i) {
    // 逐个访问字符
}
```

## 字符串拼接的限制

```cpp
string s1 = "hello";
string s2 = "world";
string result = s1 + ", " + s2;
```

这段代码成立，是因为表达式里从一开始就有 `string` 参与运算。

下面这种写法不行：

```cpp
// auto bad = "hello" + ", ";
```

原因不是“编译器不支持两个字符串相加”，而是这里的两个运算对象其实都是字符串字面值，不是 `string` 对象。

## 易错点

- 误把 `cin >> s` 当成“读取整行”。
- 忘记 `size()` 返回无符号类型，拿它和负数比较。
- 以为两个字符串字面值可以直接用 `+` 拼接。
- 用下标访问字符时，不先确认字符串是否为空。
