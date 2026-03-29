# try 语句块与异常处理(try_blocks_and_exception_handling)

## 核心概念

- 异常机制用来把正常逻辑和错误处理逻辑分开。
- `throw` 负责抛出异常，`try` 负责包住可能出错的代码，`catch` 负责处理异常。
- 一旦抛出异常，当前正常流程会被打断，程序会去找匹配的处理代码。
- 标准库已经提供了一批常见异常类型。
- 异常不应替代普通分支判断。

## 异常机制的作用

程序执行过程中，有些问题不是简单换一条普通分支就能解决的，例如：

- 输入数据不合法
- 对象状态不匹配
- 某个操作无法继续

如果把这些错误处理都揉进正常逻辑里，代码会变得混乱。  
异常机制的作用，就是把正常路径和错误路径分开。

## `throw` 表达式

发现异常情况时，可以用 `throw` 抛出异常对象：

```cpp
throw runtime_error("input error");
```

这表示：

- 当前正常流程不再继续
- 把错误对象抛出去
- 交给后续匹配的处理代码

它不是返回一个特殊值，而是直接中断当前普通执行路径。

## `try` 与 `catch`

异常处理的基本结构如下：

```cpp
try {
    // 可能出错的代码
} catch (const runtime_error& err) {
    // 处理这种异常
}
```

它的流程是：

- `try` 中放可能失败的代码
- 一旦抛出异常，就停止当前正常流程
- 程序寻找后续类型匹配的 `catch`
- 找到后进入对应处理逻辑

例如：

```cpp
try {
    if (item1.isbn() != item2.isbn())
        throw runtime_error("Data must refer to same ISBN");
    cout << item1 + item2 << endl;
} catch (const runtime_error& err) {
    cout << err.what() << endl;
}
```

如果当前地方没有匹配的 `catch`，异常会继续向外层传播。

## 标准异常类型

标准库已经提供了一些常见异常类型，例如：

- `exception`
- `runtime_error`
- `logic_error`
- `out_of_range`

很多异常对象都支持通过 `what()` 读取错误信息：

```cpp
catch (const runtime_error& err) {
    cout << err.what() << endl;
}
```

复习时至少要知道 `runtime_error` 很常见，适合表示运行时才发现的问题。

## 异常与普通控制流

异常确实能改变程序执行路径，但它不适合代替日常的 `if` / `else` 判断。

- 正常、可预期的分支，用条件语句处理
- 真正的异常情况，再考虑使用异常机制

这样代码的职责边界会更清楚。

## 易错点

- 把 `throw` 当成另一种“返回错误码”。
- 以为抛出异常后，`try` 里后面的语句还会继续执行。
- `catch` 住异常后，不知道可以用 `what()` 查看错误信息。
- 把异常机制当成普通分支结构来替代 `if` / `else`。
- 只记住 `throw`、`try`、`catch` 三个关键词，却没理清它们的流程关系。
