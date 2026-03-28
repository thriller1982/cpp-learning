# 条件语句(conditional_statements)

## 核心概念

- 条件语句根据条件真假决定程序走哪条分支。
- `if` 适合范围判断和复杂条件。
- `switch` 适合离散值匹配。
- `else` 总是和最近的未匹配 `if` 配对。
- `switch` 里最常见的错误是漏写 `break`。

## `if` 语句

最基本的 `if` 形式是：

```cpp
if (condition)
    statement;
else
    statement2;
```

它的核心含义很直接：

- 条件为真，执行前一条分支
- 否则执行后一条分支

条件表达式不要求本来就是 `bool`，只要它能转换成 `bool` 就行。

```cpp
if (x) {
    do_something();
}
```

这里的重点不是“`x` 必须是布尔变量”，而是它最终要能按真假来理解。

## `else` 和最近的 `if` 配对

这是条件语句里最著名的坑之一。

```cpp
if (a)
    if (b)
        foo();
    else
        bar();
```

这里的 `else` 会配给内层那个 `if (b)`，不是外层 `if (a)`。

也就是说，这段代码等价于：

```cpp
if (a) {
    if (b)
        foo();
    else
        bar();
}
```

所以只要嵌套 `if` 稍微复杂一点，就应优先加花括号，把配对关系写清楚。

## `else if` 链

多分支判断最常见的写法是：

```cpp
if (score >= 90) {
    grade = 'A';
} else if (score >= 80) {
    grade = 'B';
} else {
    grade = 'C';
}
```

这种结构适合：

- 有先后顺序的条件判断
- 范围型条件
- 不只是“某个离散值等于什么”的情况

## 尽量给 `if` 加花括号

哪怕分支里只有一条语句，也通常更推荐这样写：

```cpp
if (condition) {
    do_something();
}
```

原因不是“语法必须如此”，而是：

- 可读性更稳定
- 以后加第二条语句时不容易出错
- 能减少空语句和配对错误这类低级 bug

## `switch` 语句

`switch` 适合做离散值匹配。

```cpp
switch (ch) {
    case 'a':
        ++aCnt;
        break;
    case 'e':
        ++eCnt;
        break;
    default:
        break;
}
```

它可以先理解成：

- 先计算控制表达式
- 再跳到匹配的 `case`
- 然后从那个位置往下执行

## `switch` 只适合整数类控制表达式

`switch` 的控制表达式结果必须是整数类型，或能转换成整数的类型。  
常见可用的有：

- `int`
- `char`
- `enum`

但像 `string` 这种类型，就不能直接拿来做 `switch`。

## `case` 标签必须是常量表达式

下面这种写法可以：

```cpp
case 1:
case 'a':
```

但把普通变量直接放进 `case`，通常是不行的。  
因为 `switch` 的分支入口必须在编译时就确定下来。

## `break` 和贯穿

`switch` 最容易错的地方就是漏写 `break`。

```cpp
switch (n) {
    case 1:
        cout << "one\n";
    case 2:
        cout << "two\n";
}
```

当 `n == 1` 时，这里会输出两行。  
这不是编译器失误，而是因为程序会从匹配位置继续往下执行，这就叫贯穿。

所以通常要主动写：

```cpp
break;
```

当然，少数情况下你可能就是想贯穿，但那应该是明确设计，而不是疏忽。

## `default` 用来兜底

`default` 不是必须写，但大多数时候值得保留。

```cpp
switch (ch) {
    case 'a':
        ++aCnt;
        break;
    default:
        ++otherCnt;
        break;
}
```

它的价值是：

- 让未覆盖情况有去处
- 让分支逻辑更完整

## `if` 和 `switch` 怎么选

可以先这样记：

- `if`：适合范围条件、复合逻辑条件
- `switch`：适合离散值匹配

例如：

- `score >= 90` 这种更像 `if`
- `ch` 是不是 `'a'`、`'e'`、`'i'` 这种更像 `switch`

## 易错点

- 误以为 `else` 会自动配给自己“想配的那个 if”。
- `if` 只有一条语句时不写花括号，后续改代码时埋坑。
- 拿 `string` 直接做 `switch` 控制表达式。
- 忘记 `case` 标签要是常量表达式。
- 在 `switch` 里漏写 `break`，导致意外贯穿。
