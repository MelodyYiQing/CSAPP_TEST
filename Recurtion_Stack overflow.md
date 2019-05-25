Recursion is Not always preferable 
---
<br>So in this case we can see that the program sometimes quit before finished.The key problem is about the recursion.
<br>Because every time we go to recursion the computer will automatically move a space to apply for the local variables.
<br>So when the recursion goes deeper ,the stack will be highly possible overflowed.
<br>So programmers should be much careful when using recursion,and be very mean when apply for the local variable.
<br>Sometimes it is not the bigger the better.
<br>And since the time and space expense of recursion is very big ,
<br>it's not a very ideal choose when we solve some prombles requiring a strict limited time and space(often in ACM).

<br>`-d:将代码段反汇编`
<br>`-S:将代码段反汇编的同时，将反汇编代码和源代码交替显示，编译时需要给出-g，即需要调试信息。`
<br>`-l:反汇编代码中插入源代码的文件名和行号。`
<br>`-j section:仅反汇编指定的section。可以有多个-j参数来选择多个section。`

 <br>`objdump -d a.out # 简单反汇编`
 <br>`objdump -S a.out # 反汇编代码中混入对应的源代码`
 <br>`objdump -j .text -l -S a.out # 打印源文件名和行号` 
```cpp
#include <stdio.h>
#include <stdlib.h>

int recurse(int x) {
    int a[1<<15];  /* 4 * 2^15 =  64 KiB */
    printf("x = %d.  a at %p\n", x, a); 
    a[0] = (1<<14)-1; //2^14 - 1< 2^15 no problem/
    a[a[0]] = x-1;
    if (a[a[0]] == 0)
	return -1;
    return recurse(a[a[0]]) - 1;
}

int main(int argc, char *argv[]) {
    int x = 100;
    if (argc > 1)
	x = atoi(argv[1]);
    int v = recurse(x);
    printf("x = %d.  recurse(x) = %d\n", x, v);
    return 0;
}
```
Outcome
--
![run](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/run1.png)
![run](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/run2.png)
![run](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/run3.png)
 

