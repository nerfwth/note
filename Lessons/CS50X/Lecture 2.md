# cryptography 密码学
# debugging
设置断点(短点的这一行还未执行)
(这里使用<cs50.h>头文件)调试buggy.c
$debug50 ./buggy.c

单步跳过


 **在不同操作系统和环境中，EOF的输入方式不同：Windows使用Ctrl+Z，Linux/Unix/Mac使用Ctrl+D。**

# 编译流程
1. preprocessing预处理
2. compiling编译 c语言-> 汇编指令：cpu能理解的最低级别指令
3. assembling 汇编  汇编代码->0和1
4. linking 链接  把你的代码和库里面的代码中的0和1链接起来
## 编译器
source code->compiler（编译器）->machine code
clang编译器
$clang hello.c
会产生./a.out(文件名)
$clang hello.c -lcs50  (链接cs50库)

### command-line argumemts 命令行参数
$clang -o hello hello.c -lcs50  //这里等于可以自定义程序名为hello
# 运算
只要在计算中用到哪怕一个浮点数，整个运算就会自动提升为浮点数运算。
example ：
```c++
int score1，score2,score3；
(score1+score2+score3)/3.0
(score1+score2+score3)/(float)3
```
注意：```
```c++
(float)(71 / 3)  // 23.0  强制类型转换只影响它所在位置之后的运算，所以这样是不行的
```
![[Pasted image 20261101222926.png]]
# 数组 arrays
一块连续排列的内存空间
```c++
int score[3];
score[0]=72;
score[1]=73;
score[2]=33;
```
C 语言中，数组作为函数参数传递时，会退化为指针。传递的不是整个数组，而是数组首元素的地址。
```c++
float average(int length,int numbers[])
{
	int sum =0;
	for(int i=0;i<length;i++)
	{
		sum+=numbers[i];
	}
	return sum/(float)length;
}
```