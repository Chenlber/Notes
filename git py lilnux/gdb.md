ref:https://www.modb.pro/db/1899860365098364928?utm_source=index_ai


```
# yum install gdb gcc -y
# gdb --version
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-120.el7

gcc -g test.c


r
n,next ###执行一条程序，不进入函数内部
p 
s,step ### step s进去某一个具体的函数调试
info b
b main
b 10
bt # Backtrace: display the program stack.
c # Continue running your program (after stopping, e.g. at a breakpoint).
list
```

