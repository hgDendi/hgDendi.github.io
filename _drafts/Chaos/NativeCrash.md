# Native Crash #

## 注意pid和tid ##
判断问题发生在主线程还是子线程中

## 查看堆栈部分 ##
从上到下按图索骥

使用addr2line解析,找到函数入口

    addr2line -C -f -e [filename] [addr]

反汇编动态链接库文件
    objdump -S -D libc.so > deassmble_libc.txt

打开反汇编后的重定向文件，在查询的时候输入地址。之后需要翻译成对应的C函数去看。
