# sizeof 与逗号运算符(sizeof_and_comma_operator)

## 核心概念

- `sizeof` 用于得到对象或类型所占的字节数。
- `sizeof` 有两种常见形式：`sizeof(expr)` 和 `sizeof(type)`。
- `sizeof` 的结果类型是 `size_t`。
- 对数组使用 `sizeof`，得到的是整个数组大小，不是首元素大小。
- 逗号运算符会先算左边，再算右边，整个表达式结果取右边。

## `sizeof` 的基本用法

```cpp
size_t a = sizeof(int);
int x = 0;
size_t b = sizeof(x);
```

这两种写法都很常见：

- `sizeof(type)`
- `sizeof(expr)`

重点是：它关注的是类型和大小，不是普通运行期计算。

## `sizeof` 作用在数组上

```cpp
int arr[10];
size_t n = sizeof(arr);
```

这里得到的是：

```cpp
10 * sizeof(int)
```

这正是 `sizeof` 在数组上很有代表性的地方。  
它看到的是整个数组类型，而不是只看第一个元素。

## `sizeof` 不会正常求值

```cpp
int i = 0;
size_t n = sizeof(++i);
```

这里 `n` 会得到 `int` 的大小，但 `i` 不会因为这行代码而递增。  
也就是说，`sizeof` 不是在“真的执行 `++i`”。

## 逗号运算符

逗号运算符的形式是：

```cpp
expr1, expr2
```

它会先求值左边，再求值右边，整个表达式结果取右边那个表达式的值。

```cpp
int i = 0;
int j = 1;
int k = (++i, ++j);
```

最后：

- `i` 变成 `1`
- `j` 变成 `2`
- `k` 的值是 `2`

## 逗号运算符知道即可

现代 C++ 里一般不鼓励滥用逗号运算符。  
常见且相对自然的地方主要还是 `for` 循环这类场景。

## 易错点

- 误以为 `sizeof(arr)` 得到的是首元素大小。
- 看到 `sizeof(expr)` 就以为里面的表达式会正常执行。
- 不清楚 `sizeof` 的结果类型是 `size_t`。
- 把逗号运算符和函数参数列表里的逗号混为一谈。
