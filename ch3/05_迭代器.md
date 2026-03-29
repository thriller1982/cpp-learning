# 迭代器(iterators)

## 核心概念

- 迭代器是访问容器和字符串元素的统一方式。
- `begin()` 指向首元素，`end()` 指向尾后位置。
- `end()` 可以比较、可以参与运算，但不能解引用。
- `string` 和 `vector` 的迭代器支持随机访问运算。

## `begin()` 和 `end()`

```cpp
vector<int> nums{1, 2, 3};
auto b = nums.begin();
auto e = nums.end();
```

这里的 `begin()` 和 `end()` 不是普通全局函数，而是标准库类型提供的成员函数。

- `nums.begin()` 和 `nums.end()` 是 `std::vector<int>` 提供的成员函数
- 写全类型时，`b` 和 `e` 的类型都是 `std::vector<int>::iterator`

这里：

- `b` 指向第一个元素
- `e` 指向最后一个元素后面的那个位置

对 `string` 也一样：

```cpp
string s = "hello";
auto sbeg = s.begin();
auto send = s.end();
```

这里：

- `s.begin()` 和 `s.end()` 是 `std::string` 提供的成员函数
- `sbeg` 和 `send` 的类型都是 `std::string::iterator`

可以顺手和数组那边区分开：

- `nums.begin()` / `s.begin()` 是成员函数调用
- 数组常见的 `begin(arr)` / `end(arr)` 对应的是标准库函数 `std::begin(arr)` 和 `std::end(arr)`

## 基本用法

```cpp
for (auto it = nums.begin();
     it != nums.end();
     ++it) {
    cout << *it;
}
```

要点只有两个：

- `*it` 取出当前位置的元素
- `++it` 把迭代器移动到下一个位置

最需要记住的限制是：

```cpp
// *nums.end();
```

`end()` 不指向一个真实元素，所以不能解引用。

## 只读迭代器

```cpp
const vector<int> nums{1, 2, 3};
auto it = nums.begin();
```

这里的 `begin()` 返回的是：

```cpp
std::vector<int>::const_iterator
```

原因是 `nums` 本身是 `const vector<int>`，所以它只能调用返回只读迭代器的那个 `begin()` 版本。

也就是说：

- `it` 可以读元素
- 但不能通过 `it` 修改元素

例如：

```cpp
// *it = 10;
```

这类写法就不合法。

即使对象不是常量，只要你的意图是“只读遍历”，也应该把“我不打算改它”这个约束保持清楚。

## 迭代器运算

```cpp
auto mid = nums.begin() + (nums.end() - nums.begin()) / 2;
```

这类写法成立，是因为 `vector` 和 `string` 的迭代器支持随机访问。

常见运算包括：

- `it + n`
- `it - n`
- `it1 - it2`
- `it[n]`
- `< <= > >=`

但它们不是“随便两个迭代器都能算”。更准确地说：

- 两个迭代器应来自同一个容器
- 运算结果不能越界
- `end()` 虽可参与比较和减法，但依然不能解引用

## 易错点

- 把 `end()` 误解为“最后一个元素”。
- 直接解引用 `end()`。
- 对不属于同一容器的两个迭代器做减法或比较。
- 只会写下标循环，不理解迭代器才是通用访问模型。
