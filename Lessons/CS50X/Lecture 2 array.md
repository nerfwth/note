# cryptography 密码学
# debugging
设置断点(短点的这一行还未执行)
(这里使用<cs50.h>头文件)调试buggy.c
$debug50 ./buggy.c

单步跳过

# 编译器
source code->compiler（编译器）->machine code
clang编译器
$clang hello.c
会产生./a.out(文件名)
$clang hello.c -lcs50  (链接cs50库)

## command-line argumemts 命令行参数
$clang -o hello hello.c -lcs50  //这里等于可以自定义程序名为hello