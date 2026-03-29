# const 限定符(const)

## 核心概念

- `const` 表示“通过这个名字，不允许修改值”。
- 对引用和指针来说，`const` 放的位置决定它锁住的是谁。
- 顶层 `const` 和底层 `const` 是后续类型推导、拷贝规则的重要基础。

## `const` 引用

```cpp
int i = 42;
const int& r = i;
```

它的含义是：

- 我承诺不通过 `r` 修改对象
- 但并不代表对象本身绝对不能变

所以：

```cpp
i = 100; // 合法
```

此时 `r` 看到的值也会变成 `100`。

## 指针与 `const`

### 1. 指向 `const` 的指针

```cpp
const int* p1 = &x;
```

含义：

- 不能通过 `p1` 改目标值
- 但 `p1` 自己可以改指向

### 2. `const` 指针

```cpp
int* const p2 = &x;
```

含义：

- `p2` 自己不能改指向
- 但可以通过 `p2` 改目标值

### 3. 两边都 `const`

```cpp
const int* const p3 = &x;
```

含义：

- 指向不能改
- 目标也不能经它改

## 顶层 const 与底层 const

### 顶层 const

对象自己不能变。

示例：

```cpp
const int c_num = 10;
int* const top_ptr = &num;
```

### 底层 const

通过这个引用或指针看到的目标不能变。

示例：

```cpp
const int* low_ptr = &num;
const int& low_ref = num;
```

## 拷贝时的规则

### 1. 顶层 const 通常会被忽略

```cpp
const int c_num = 10;
int copy = c_num;
```

### 2. 底层 const 必须严格匹配或只能做权限收缩

```cpp
const int* low_ptr = &num;
const int* ok = low_ptr; // 合法
// int* bad = low_ptr;   // 错误
```

因为你不能把“只读承诺”偷偷放宽成“可写权限”。

## 易错点

- 只背语法，不理解 `const` 实际锁住的是谁。
- 混淆“对象不能改”和“不能通过这个名字改”。
- 在指针拷贝时错误丢掉底层 `const`。
