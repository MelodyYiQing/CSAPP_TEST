<br>Executable and Linkable Format
===
ELF有两种视图，对应不同的目标文件
* ELF的链接视图
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/ELF链接视图.png)
<br>对应可重定位的目标文件
* ELF的执行视图
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/ELF执行视图.png)
<br>对应可执行的目标文件
<br>main.c程序
```cpp
/* main.c */
/* $begin main */
int sum(int *a, int n);

int array[2] = {1, 2};

int main() 
{
    int val = sum(array, 2);
    return val;
}
/* $end main */
/* sum.c */
/* $begin sum */
int sum(int *a, int n)
{
    int i, s = 0;
    
    for (i = 0; i < n; i++) { 
        s += a[i];
    }
    return s;
}        
/* $end sum */

```
* 通过readelf -h 我们可以查看ELF的头信息（相关运行程序的文件将会传到博客上）
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/链接视图readelf-h.png)
<br>可以在Magic里看到 7f 45 4c 46就是ELF的魔数（对应a.out的魔数是01H 07H，PE格式下为4DH 5AH)。魔数就是文件开头几个字节通常用来确定文件的类型或格式）
<br>加载或读取文件时，可用魔数确认文件类型是否正确
<br>此外我们还可以看到，所给的main.o的格式类别是64位版本的，数据采用补码（带符号数）和小端形式。版本为1，操作系统是UNIX-System V,版本类型为REL可重定位文件，机器为x86-64架构
<br>入口点地址为0，呼应了是可重定位的目标文件，所以装入的起始地址为0，不会执行,只用于链接
<br>程序头表偏移量为0，大小为0，表项个数为0，说明程序头表不存在，也对应了可重定位文件是只能用于链接，不应加载的
<br>Start of section headers给出了节头表的起始位置是704字节，位于ELF头0处开始往下数704个字节，节头表项有12个，每个是64个字节（一共有12* 64B)
<br>ELF头占64B=0x40B
<br>最后一行指出第11项是字符串表
* 用readelf -S命令来读节头表信息
<br>![struct](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/readelf-S1.png)
<br>首先会说明(这里)一共有12个节头信息，节头表在0x2c0的位置。
<br>![struct](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/readelf-S2.png)
<br>然后可以看到描述了0-11总共12个节的情况，第0个节设为空。每一个表象就是64个字节的数据结构
<br>数据结构
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/节头表象数据结构.png)
<br>Addr一栏可以看到地址都是0，因为是可重定位文件，不会执行，所以地址都为0
<br>Off指出了从ELF头开始偏移多少个字节开始这个节，Size指出了该节有多大，接下来指出是否可装入，可执行，对齐方式等等
<br>有四个节将会分配存储空间.text:可执行，.data和.bss:可读可写，.rodata：可读，此外其他的节在运行时都不会加载到寄存器中，只作为辅助链接
<br>在这里我们可以看到.text起始位置off是40正好呼应了我们上面所讲ELF头占64B=0x40B
* 用readelf -s可以看到符号表信息
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/readelf-s.png)
<br>从中间可以看到main函数放在第一节对应.text,array放在第三节的.data中，而sum是一个未定义的符号
<br>用objdump-d指令可以看到可以看到反汇编的结果（由于没有链接，所以跳转到sum的时候是没有准确的地址的）
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/objdumpmain.o.png)
<br>那么如果
