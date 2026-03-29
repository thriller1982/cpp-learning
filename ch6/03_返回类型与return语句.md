# 返回类型与 return 语句(return_types_and_return_statement)

## 核心概念

- `return` 负责结束函数并交回结果。
- `void` 函数可以提前 `return;`，但不能返回普通值。
- 有返回值函数的每条可能执行路径都应返回结果。
- 返回局部对象的值通常安全，但不要返回局部对象的引用或指针。
- 函数不能直接返回数组本身，但可以返回数组的指针或引用。

## `void` 函数与有返回值函数

返回类型为 `void` 的函数不返回普通值：

```cpp
void printHello() {
    cout << "hello";
}
```

如果想提前结束这类函数，可以写：

```cpp
return;
```

有返回值函数则需要通过 `return` 交回结果：

```cpp
int sum(int a, int b) {
    return a + b;
}
```

`return` 不只是把结果送出去，也意味着函数在这里立即结束。

## `return` 与返回类型

`return` 后面的表达式类型应与函数返回类型一致，或者至少能转换过去：

```cpp
double d = 3.14;

int f() {
    return d;
}
```

这类写法虽然可能成立，但会发生类型转换，甚至可能带来信息丢失。  
因此写返回语句时，不能只看“能不能编译”，还要看返回值是否符合原本意图。

## 返回路径

如果函数声明说自己会返回一个值，那么每条可能执行到的路径都应该给出结果：

```cpp
int f(bool flag) {
    if (flag)
        return 1;
    // 危险：这里少了 return
}
```

这是有返回值函数中的高频错误之一。

## 返回局部对象的值

返回局部对象的值通常是安全的：

```cpp
string copyWord(const string& word) {
    string ret = word;
    return ret;
}
```

这里返回的是 `ret` 的值，而不是把一个悬空引用交出去。  
因此这种写法是正常且常见的。

## 返回引用、指针与数组相关类型

不要返回局部对象的引用或指针：

```cpp
const string& bad() {
    string s = "hello";
    return s;
}
```

```cpp
const string* bad_ptr() {
    string s = "hello";
    return &s;
}
```

函数结束后，局部对象已经销毁，返回出去的引用或指针会悬空。

函数也不能直接返回数组本身，但可以返回数组的指针或引用。例如：

```cpp
int (*func(int i))[10];
```

这表示 `func` 接收一个 `int`，返回“指向含 10 个 `int` 的数组的指针”。

如果直接手写太难读，可以用类型别名或尾置返回类型化简：

```cpp
using arrT = int[10];
arrT* func(int i);
```

```cpp
auto func(int i) -> int (*)[10];
```

## 易错点

- 在 `void` 函数里试图返回一个普通值。
- 写有返回值函数时，没有覆盖所有可能执行路径。
- 低估返回语句里的类型转换和信息丢失。
- 误以为返回局部对象的引用或指针是安全的。
- 把“返回数组”理解成能直接返回数组本体。
