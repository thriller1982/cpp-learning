# 类型转换(type_conversions)

## 核心概念

- 类型转换分为隐式转换和显式转换。
- 不同算术类型一起运算时，编译器常常会先统一类型。
- 小整型经常会先提升成 `int`。
- `signed` 和 `unsigned` 混用是高频陷阱。
- 显式转换优先使用 C++ 的命名强制类型转换，而不是老式 C 风格强转。

## 算术转换

不同算术类型一起参与运算时，编译器通常会先做统一。

```cpp
int i = 3;
double d = 2.5;
auto result = i + d;
```

这里：

- `i` 会先转换
- `result` 的类型是 `double`

这类规则的核心不是背术语，而是知道：表达式结果类型可能和原变量类型不同。

## 整型提升

比较小的整型在运算前，往往会先提升成 `int`。

```cpp
short s1 = 10;
short s2 = 20;
auto sum = s1 + s2;
```

这里 `sum` 的类型通常不是 `short`，而是 `int`。

所以不要想当然地认为“小类型加小类型，结果还会是小类型”。

## `signed` 和 `unsigned` 混用

这是最值得警惕的一类转换。

```cpp
unsigned int u = 10;
int i = -1;
auto result = i + u;
```

直觉上很多人会把这看成 `-1 + 10`，但实际计算前往往会先发生无符号转换。  
这会让结果和人的第一反应不一致。

所以只要看到有符号和无符号混在一起，就应当立刻提高警惕。

## 其他常见隐式转换

### 条件中的布尔转换

```cpp
if (x) {
    // 只要 x 能转换成 bool，这种写法就成立
}
```

### 数组转指针

```cpp
int arr[10];
int* p = arr;
```

这里 `arr` 在表达式里常常会退化成指向首元素的指针。

### 空指针字面值

现代 C++ 更推荐：

```cpp
int* p = nullptr;
```

而不是用 `0` 或 `NULL` 表示空指针。

## 显式转换

当你不想把转换交给编译器默默完成时，可以显式写出来。  
《C++ Primer》更强调：如果必须强制类型转换，应优先使用 C++ 自己的命名强制类型转换。

它们的通用形式是：

```cpp
cast_name<target_type>(expr)
```

这种写法的价值不只是“语法不同”，更重要的是：  
看到转换名字时，你能更快判断这次转换到底属于哪一类。

C++ 里还会见到 `dynamic_cast`，但它依赖继承体系和运行时类型识别，通常放到类层次相关章节再系统理解。  
这一节先把 `static_cast`、`const_cast` 和 `reinterpret_cast` 分清楚。

## `static_cast`

`static_cast` 用来处理那些语义上比较清楚、编译器也知道怎么做的转换。  
这一类是最常见的显式转换。

例如：

```cpp
double d = 3.14;
int i = static_cast<int>(d);
```

这里 `i` 会变成 `3`。  
这个转换并不神奇，它同样会丢失小数部分，只是你的意图更明确了。

`static_cast` 还常用来让算术运算按你想要的类型进行：

```cpp
int i = 5;
int j = 2;
double result = static_cast<double>(j) / i;
```

如果不做转换，这里会先做整数除法；写了 `static_cast<double>(j)` 之后，表达式就会按浮点运算来处理。

可以先把它记成一句话：

- `static_cast` 适合“我知道自己在做什么，而且这种转换本来就有明确规则”

## `const_cast`

`const_cast` 用来改变对象的底层 `const` 性质。  
它通常只用于指针或引用。

例如：

```cpp
int i = 0;
const int* p1 = &i;
int* p2 = const_cast<int*>(p1);
```

这里 `p2` 去掉了 `const`，因此可以通过它修改 `i`。

但要非常小心：  
如果对象本身一开始就是 `const`，那么即使你用 `const_cast` 去掉了 `const`，也不能安全修改它。

```cpp
const int ci = 0;
const int* p1 = &ci;
int* p2 = const_cast<int*>(p1);

// *p2 = 42;   // 危险：对象本身是 const
```

所以 `const_cast` 不是“万能去 const 工具”，它只能改变你手里这个指针或引用的类型表现，不能改变对象本身原来的常量属性。

## `reinterpret_cast`

`reinterpret_cast` 用来按完全不同的底层方式解释一个对象或地址。  
这是最危险、也最不该滥用的显式转换。

例如：

```cpp
int i = 42;
int* p = &i;
char* cp = reinterpret_cast<char*>(p);
```

这里不是把 `int` “变成了 `char`”，而是把 `i` 所在内存按 `char*` 的方式去看。  
这种转换已经非常靠近底层实现，程序结果经常依赖机器、数据布局和具体上下文。

可以先把它记成一句话：

- `reinterpret_cast` 不是普通业务代码里的常规工具
- 如果你一眼说不清它为什么必须存在，那通常就不该写它

## 老式强制类型转换

C++ 还允许老式写法：

```cpp
double d = 3.14;
int i = (int)d;
int j = int(d);
```

这些写法能工作，但不推荐。  
问题不只是“风格老”，而是它把不同性质的转换都糊在一起了，读代码的人很难一眼看出：这到底是普通算术转换、去掉 `const`，还是底层重新解释。

所以这一节最该记住的是：

- 能不用强转就不用
- 必须用时，优先用命名强制类型转换
- 初学阶段最该掌握的是 `static_cast`
- `const_cast` 和 `reinterpret_cast` 要带着明显的警惕去看

## 易错点

- 低估隐式转换对结果的影响。
- 忘记小整型运算前常常会先提升成 `int`。
- 把 `signed` 和 `unsigned` 放进同一个表达式里，却没意识到结果可能失真。
- 继续用 `0` 或 `NULL` 表示空指针，而不是 `nullptr`。
- 觉得“写了强转”就等于“没有风险”。
- 用老式 C 风格强转，把转换意图写得含混不清。
- 误以为 `const_cast` 能让本来是 `const` 的对象安全地变可修改。
- 把 `reinterpret_cast` 当成普通类型转换工具使用。
