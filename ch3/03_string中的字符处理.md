# string 中的字符处理(string_character_processing)

## 核心概念

- 范围 `for` 是处理字符串字符最直接的方式。
- `auto c` 拿到的是副本，`auto& c` 拿到的才是原字符。
- 字符分类和大小写转换常配合 `<cctype>` 使用。
- “遍历”和“修改”在写法上只差一个引用，但语义完全不同。

## 范围 for 读取字符

```cpp
string s = "Hello!";

for (auto c : s) {
    // c 是逐个字符的副本
}
```

这适合做只读处理，比如统计字符种类。它的关键点是：你改的是副本，不是原字符串。

## 需要修改时要用引用

```cpp
string s = "Hello!";

for (auto& c : s) {
    c = toupper(c);
}
```

这时 `c` 绑定到字符串里的真实字符，所以修改会落到原对象上。

如果你写成：

```cpp
for (auto c : s) {
    c = toupper(c);
}
```

那只是改了局部副本，循环结束后原字符串不会变。

## `<cctype>` 里的常用函数

```cpp
#include <cctype>
```

最常见的一组是：

- `isalnum(c)`：字母或数字
- `isalpha(c)`：字母
- `isdigit(c)`：数字
- `isspace(c)`：空白字符
- `ispunct(c)`：标点
- `toupper(c)`：转大写
- `tolower(c)`：转小写

这些函数本质上是在回答“这个字符是什么”或者“把它变成另一种大小写形式”。

## 两个高频例子

全部转大写：

```cpp
for (auto& c : s) {
    c = toupper(c);
}
```

统计标点个数：

```cpp
decltype(s.size()) punct_cnt = 0;

for (auto c : s) {
    if (ispunct(c)) {
        ++punct_cnt;
    }
}
```

这里用 `decltype(s.size())`，是为了和字符串长度相关类型保持一致，不额外引入有符号和无符号混用的问题。

## 易错点

- 写 `for (auto c : s)` 却以为自己改到了原字符串。
- 忘记包含 `<cctype>` 就直接使用 `toupper` 或 `ispunct`。
- 统计字符数量时，随手用 `int`，忽略了和 `size()` 类型不一致的问题。
