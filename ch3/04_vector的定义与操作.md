# vector 的定义与操作(vector_definition_and_operations)

## 核心概念

- `vector` 是同类型对象的可变长序列。
- 现代 C++ 里，需要“可增长数组”时通常优先使用 `vector`。
- `()` 初始化和 `{}` 初始化在 `vector` 上差异很大。
- 下标用于访问已有元素，不用于新增元素。

## 定义与初始化

```cpp
vector<int> v1;          // 空 vector
vector<int> v2(v1);      // 直接初始化：拷贝构造
vector<int> v3 = v1;     // 拷贝初始化：拷贝构造
vector<int> v4(10);      // 10 个值初始化的 int
vector<int> v5(10, 42);  // 10 个 42
vector<string> v6(10);   // 10 个空 string
vector<int> v7{10};      // 1 个元素，值是 10
vector<int> v8{10, 42};  // 2 个元素
```

这里最值得反复看的是：

```cpp
vector<int> a(10);
vector<int> b{10};
```

它们长得很像，但语义完全不同：

- `a` 有 10 个元素
- `b` 只有 1 个元素，值为 `10`

这里要把两层规则分开看：

- `a(10)` 用的是 `()`，属于直接初始化
- `b{10}` 用的是 `{}`，属于列表初始化

对 `vector` 这类类类型，列表初始化会先尝试匹配 `initializer_list` 构造函数；只有这一轮不可行时，才会再去匹配普通构造函数。

例如：

```cpp
vector<string> c{10};       // 10 个空 string
vector<string> d{10, "hi"}; // 10 个 "hi"
```

这几行可以这样理解：

- `vector<int> b{10}` 能匹配 `initializer_list<int>`，所以得到 1 个元素 `10`
- `vector<string> c{10}` 不能组成 `initializer_list<string>`，于是转去普通构造函数，得到 10 个空 string
- `vector<string> d{10, "hi"}` 同理，得到 10 个 `"hi"`

## 追加元素要用 `push_back`

```cpp
vector<int> nums;
nums.push_back(1);
nums.push_back(2);
nums.push_back(3);
```

这才是“往空 `vector` 里逐步塞元素”的正确方式。

下面这种写法不对：

```cpp
vector<int> nums;
// nums[0] = 1;
```

原因不是语法问题，而是 `nums` 里此时根本没有第 `0` 个元素。

## 常用操作与遍历

```cpp
vector<int> nums{1, 2, 3};

bool is_empty = nums.empty();
auto count = nums.size();
int first = nums[0];
```

遍历时常见两种写法。

下标遍历：

```cpp
for (vector<int>::size_type i = 0; i != nums.size(); ++i) {
    // 使用 nums[i]
}
```

范围 `for`：

```cpp
for (auto n : nums) {
    // 只读
}

for (auto& n : nums) {
    // 可修改原元素
}
```

如果只是顺序处理元素，范围 `for` 往往更直接。

## 什么时候优先选 `vector`

```cpp
vector<string> words;
string word;

while (cin >> word) {
    words.push_back(word);
}
```

这种“事先不知道会读到多少个对象”的场景，正是 `vector` 的主场。

它比内置数组更灵活，也更符合现代 C++ 的默认选择。

## 易错点

- 把 `vector<int> v(10)` 和 `vector<int> v{10}` 当成一回事。
- 用下标给空 `vector` “新增”元素。
- 在只需要顺序遍历时，仍然手写容易出错的下标循环。
- 忘记 `vector` 中所有元素类型必须一致。
