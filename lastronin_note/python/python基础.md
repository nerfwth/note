

# 1. C 语言和 Python 的核心区别

## 1.1 基本语法对比

| C 语言               | Python             |     |     |
| ------------------ | ------------------ | --- | --- |
| `int a = 10;`      | `a = 10`           |     |     |
| `printf("%d", a);` | `print(a)`         |     |     |
| `scanf("%d", &a);` | `a = int(input())` |     |     |
| `if (...) {}`      | `if ...:`          |     |     |
| `else if`          | `elif`             |     |     |
| `for (...) {}`     | `for ...:`         |     |     |
| `while (...) {}`   | `while ...:`       |     |     |
| `{}` 表示代码块         | 缩进表示代码块            |     |     |
| `&&`               | `and`              |     |     |
| \| \|              | `or`               |     |     |
| `!`                | `not`              |     |     |

Python 最明显的特点：

- 不需要写分号 `;`
- 不使用 `{ }`
- 使用缩进区分代码块
- 通常不需要提前声明变量类型

---

# 2. Python 的变量

C 语言：

```c
int a = 10;
double b = 3.14;
char c = 'x';
```

Python：

```python
a = 10
b = 3.14
c = "x"
```

Python 会自动判断变量类型。

```python
a = 10
print(type(a))
```

结果：

```text
<class 'int'>
```

## 2.1 常见数据类型

```python
a = 10          # int 整数
b = 3.14        # float 浮点数
c = "hello"     # str 字符串
d = True        # bool 布尔值
```

可以通过：

```python
type(a)
```

查看类型。

---

# 3. 输入与输出

## 3.1 输出

C：

```c
printf("hello");
```

Python：

```python
print("hello")
```
会自动换行

```python
print(i,end=' ')
```
输出后不换行，在末尾加一个空格

输出变量：

```python
a = 10
print(a)
```

同时输出多个内容：

```python
name = "Tom"
age = 20
print(name, age)
```

---

# 4. input() 输入

最基础写法：

```python
a = input()
```

需要注意：

> `input()` 读进来的数据默认是字符串。

例如：

```python
a = input()
print(type(a))
```

即使输入：

```text
123
```

`a` 仍然是字符串。

## 4.1 输入整数

```python
a = int(input())
```

相当于 C：

```c
int a;
scanf("%d", &a);
```

## 4.2 输入浮点数

```python
x = float(input())
```

---

# 5. 一行输入多个整数

ACM 和数据处理中非常常见。

输入：

```text
10 20
```

Python：

```python
a, b = map(int, input().split())
```

这行代码可以拆开理解。

第一步：

```python
s = input()
```

得到：

```text
"10 20"
```

第二步：

```python
s.split()
```

得到：

```python
["10", "20"]
```

第三步：

```python
map(int, ...)
```

把字符串转换为整数。

因此：

```python
a, b = map(int, input().split())
```

是一个需要重点记住的写法。

---

# 6. Python 运算符

## 6.1 基础运算

```python
a + b
a - b
a * b
```

## 6.2 除法

Python：

```python
5 / 2
```

结果：

```text
2.5
```

如果想整数除法：

```python
5 // 2
```

结果：

```text
2
```

所以：

| 运算 | 含义 |
|---|---|
| `/` | 普通除法 |
| `//` | 整除 |
| `%` | 取余 |
| `**` | 幂 |

例如：

```python
2 ** 3
```

结果：

```text
8
```

---

# 7. 比较运算

```python
a > b
a < b
a >= b
a <= b
a == b
a != b
```

注意：

```python
=
```

表示赋值。

```python
==
```

表示判断是否相等。

---

# 8. 逻辑运算

C：

```c
a > 0 && b > 0
```

Python：

```python
a > 0 and b > 0
```

C：

```c
a > 0 || b > 0
```

Python：

```python
a > 0 or b > 0
```

C：

```c
!flag
```

Python：

```python
not flag
```

| C     | Python |
| ----- | ------ |
| `&&`  | `and`  |
| \| \| | `or`   |
| `!`   | `not`  |

---

# 9. if 条件判断

C：

```c
if (a > 0) {
    printf("positive");
}
```

Python：

```python
if a > 0:
    print("positive")
```

注意：

1. 条件外面通常不用括号
2. 最后有一个 `:`

---

# 10. if / elif / else

C：

```c
if (score >= 90) {
    printf("A");
}
else if (score >= 80) {
    printf("B");
}
else {
    printf("C");
}
```

Python：

```python
if score >= 90:
    print("A")
elif score >= 80:
    print("B")
else:
    print("C")
```

Python 使用：

```python
elif
```

不是：

```text
else if
```

---

# 11. 缩进

Python 不使用 `{}` 表示代码块，而是使用缩进。

正确：

```python
if a > 0:
    print("positive")
    print("a > 0")
```

错误：

```python
if a > 0:
print("positive")
```

所以：

> Python 中缩进属于语法的一部分。

一般使用 4 个空格。

---

# 12. for 循环

C：

```c
for (int i = 0; i < 5; i++) {
    printf("%d\n", i);
}
```

Python：

```python
for i in range(5):
    print(i)
```

结果：

```text
0
1
2
3
4
```

---

# 13. range()

## range(5)

```python
range(5)
```

表示：

```text
0 1 2 3 4
```

也就是：

\[
[0,5)
\]

## range(1, 6)

```python
for i in range(1, 6):
    print(i)
```

结果：

```text
1
2
3
4
5
```

所以：

```python
range(start, end)
```

表示：

\[
[start,end)
\]

**左闭右开。**

## range(start, end, step)

```python
for i in range(0, 10, 2):
    print(i)
```

结果：

```text
0
2
4
6
8
```

---

# 14. while 循环

C：

```c
while (n > 0) {
    n--;
}
```

Python：

```python
while n > 0:
    n -= 1
```

---

# 15. Python 没有 ++ 和 --

C 可以：

```c
i++;
i--;
```

Python 不可以写：

```python
i++
```

正确方式：

```python
i += 1
i -= 1
```

---

# 16. break 和 continue

## break

直接结束循环。

```python
for i in range(10):
    if i == 5:
        break
    print(i)
```

输出：

```text
0
1
2
3
4
```

## continue

跳过当前循环。

```python
for i in range(5):
    if i == 2:
        continue
    print(i)
```

结果：

```text
0
1
3
4
```

---

# 17. Python 的 list

可以暂时把 `list` 理解为比 C 数组更灵活的数据结构。

C：

```c
int a[5] = {1, 2, 3, 4, 5};
```

Python：

```python
a = [1, 2, 3, 4, 5]
```

## 17.1 访问元素

```python
a[0]
a[1]
a[2]
```

下标从 0 开始。

## 17.2 获取长度

```python
len(a)
```

## 17.3 append()

```python
a = [1, 2, 3]
a.append(4)
print(a)
```

得到：

```text
[1, 2, 3, 4]
```

## 17.4 pop()

```python
a.pop()
```

## 17.5 排序

```python
a.sort()
```

---

# 18. 遍历 list

C 的思维：

```c
for (int i = 0; i < n; i++) {
    printf("%d", a[i]);
}
```

Python 可以写：

```python
for i in range(len(a)):
    print(a[i])
```

但 Python 更推荐：

```python
for x in a:
    print(x)
```

---

# 19. 字符串 str

Python 字符串：

```python
s = "hello"
```

可以像数组一样访问：

```python
s[0]
s[1]
```

---

# 20. 负数下标

```python
s[-1]
```

表示最后一个字符。

例如：

```python
s = "python"
print(s[-1])
```

结果：

```text
n
```

---

# 21. 字符串切片

格式：

```python
s[start:end]
```

例如：

```python
s = "abcdef"
print(s[1:4])
```

结果：

```text
bcd
```

常见写法：

```python
s[:3]
s[3:]
s[:]//从头取到尾
```

---

# 22. 字符串反转


```python
s[start:end:step]
s[::-1]//步长为-1
```

例如：

```python
s = "abcdef"
print(s[::-1])
```

结果：

```text
fedcba
```

---

# 23. 回文判断

```python
s = input()

if s == s[::-1]:
    print("Yes")
else:
    print("No")
```

---

# 24. 常见字符串函数

假设：

```python
s = "Hello World"
```

## len()求长度

```python
len(s)
```

## lower()转小写

```python
s.lower()
```

## upper()转大写

```python
s.upper()
```

## strip()删除字符串**开头和结尾的空白字符**

```python
s.strip()
```

## split()按照某个分隔符切开

```python
s = "I love Python"
words = s.split()
```

得到：

```python
["I", "love", "Python"]

```

```python
print(s.split(","))//指定了逗号 `,` 作为分隔符
```
## replace()把字符串中的某一部分内容**替换成另一部分内容**

```python
s.replace("Python", "AI")
```
`replace()` 不会直接改变原字符串。
```python
s = s.replace("World", "Python")
print(s)//才能保存修改
```
## find()

```python
s.find("World")
```

- 找到：返回第一次出现位置的下标
- 找不到：返回 `-1`

---

# 25. Python dict

可以暂时理解成：

> 键 → 值

类似 C++ 的 `map`。

例如：

```python
score = {
    "Alice": 90,
    "Bob": 85
}
```

访问：

```python
print(score["Alice"])
```

---

# 26. 使用 dict 统计次数

```python
s = "banana"

cnt = {}

for ch in s:
    cnt[ch] = cnt.get(ch, 0) + 1
```

```python
cnt.get(ch, 0)//在字典 `cnt` 里查找键 `ch` 对应的值；如果没有这个键，就返回 `0`。
```
结果大致：
b: 1
a: 3
n: 2
```python
//遍历
for key in cnt.keys():
    print(key)
for value in cnt.values():
    print(value)
for key, value in cnt.items():
    print(key, value)
```


# 27. 函数

C：

```c
int add(int a, int b) {
    return a + b;
}
```

Python：

```python
def add(a, b):
    return a + b
```

调用：

```python
x = add(1, 2)
print(x)
```

## 回文函数

```python
def is_palindrome(s):
    return s == s[::-1]
```

---

# 28. Python 与 C 的思维差异

Python 不只是“C 语言少写了一些代码”。

很多时候 Python 更倾向于：

> 直接操作数据，而不是手动管理下标。

例如 C：

```c
for (int i = 0; i < n; i++) {
    printf("%d", a[i]);
}
```

Python：

```python
for x in a:
    print(x)
```

所以学习 Python 的过程中要逐渐从：

```text
下标思维
```

转向：

```text
元素思维
```

---

# 29. 第一阶段必须掌握的内容

- [x] 变量
- [x] `input()`
- [x] `print()`
- [x] `int()`
- [x] `float()`
- [x] `if / elif / else`
- [x] `for`
- [x] `range`
- [x] `while`
- [x] `list`
- [x] `string`
- [x] `dict`
- [x] 函数

暂时不用急着学习：

- class
- 面向对象
- 装饰器
- 生成器
- 高级 Python 技巧

---

# 30. 第一组练习

## 练习 1：两数相加

输入：

```text
10 20
```

输出：

```text
30
```

提示：

```python
a, b = map(int, input().split())
```

## 练习 2：判断奇偶

输入：

```text
10
```

输出：

```text
even
```

## 练习 3：三个数最大值

输入：

```text
10 50 30
```

输出：

```text
50
```

## 练习 4：计算 1~100 的和

要求输出：

\[
1+2+\cdots+100
\]

先使用 `for` 实现。

## 练习 5：输出 1~n

输入：

```text
5
```

输出：

```text
1
2
3
4
5
```

---

# 31. 第二组练习：字符串

## 练习 1

输入字符串并倒序输出。

输入：

```text
hello
```

输出：

```text
olleh
```

## 练习 2

判断一个字符串是否是回文。

输入：

```text
level
```

输出：

```text
Yes
```

## 练习 3

统计字符串中每个字符出现次数。

输入：

```text
banana
```

重点使用 `dict`。

---

# 32. 当前 Python 学习路线

```text
Python 基础语法
        ↓
string / list / dict
        ↓
function
        ↓
NumPy
        ↓
Matplotlib
        ↓
PyTorch Tensor
        ↓
神经网络基础
        ↓
CNN
        ↓
Transformer
```

---

# 33. 第一阶段验收标准

如果下面代码基本都能看懂并独立写出来，就可以开始进入 NumPy。

```python
a, b = map(int, input().split())

if a > b:
    print(a)
else:
    print(b)
```

```python
for i in range(1, 11):
    print(i)
```

```python
a = [1, 2, 3, 4, 5]

for x in a:
    print(x)
```

```python
s = input()

if s == s[::-1]:
    print("Yes")
else:
    print("No")
```

```python
cnt = {}

for ch in s:
    cnt[ch] = cnt.get(ch, 0) + 1
```

---


