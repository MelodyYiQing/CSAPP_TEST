<br>Executable and Linkable Format
===
ELF有两种视图，对应不同的目标文件
* ELF的链接视图
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/ELF链接视图.png)
<br>对应可重定位的目标文件
* ELF的执行视图
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/ELF执行视图.png)
<br>对应可执行的目标文件
* 通过readelf -h 我们可以查看ELF的头信息（相关运行程序的文件将会传到博客上）
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/readelf-h.png)
<br>可以在Magic里看到 7f 45 4c 46就是ELF的魔数（对应a.out的魔数是01H 07H，PE格式下为4DH 5AH)。魔数就是文件开头几个字节通常用来确定文件的类型或格式）
<br>加载或读取文件时，可用魔数确认文件类型是否正确
<br>此外我们还可以看到，所给的main.o的格式类别是32位版本的，数据采用补码（带符号数）和小端形式。版本为1，操作系统是UNIX-System V,版本类型为可重定位文件
<br>入口点地址为0，呼应了是可重定位的目标文件，所以装入的起始地址为0，不会执行。
<br>程序头表偏移量为0，大小为0，表象个数为0，说明程序头表是不存在的
<br>Start if section headers给出了节头表的起始位置是276字节，位于ELF头0处开始往下数276个字节，节头表象有12个，每个是40个字节（一共有12*40B)
<br>ELF头占52B=0x34B
<br>最后一行指出第九项是字符串表
* 用readelf -S命令来读节头表信息
<br>![]((https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/readelf-S.png)
<br>首先会说明(这里)一共有12个节头信息，起始位置在114
<br>然后可以看到描述了0-11总共12个节的情况，第0个节设为空。每一个表象就是40个字节的数据结构
<br>数据结构
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/节头表象数据结构.png)
<br>Addr一栏可以看到地址都是0，因为是可重定位文件，不会执行
<br>Off指出了从ELF头开始偏移多少个字节开始这个节，Size指出了该节有多大，接下来指出是否可装入，可执行，对齐方式等等
<br>有四个节将会分配存储空间.text:可执行，.data和.bss:可读可写，.rodata：可读
<br>在这里我们可以看到.text起始位置是34正好呼应了我们上面所讲ELF头占52B=0x34B
* 用readelf -s可以看到符号表信息
<br>![](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/readelf%20-s.png)
<br>从中间可以看到main函数放在第一节对应.text,array放在第三节的.data中，而sun是一个未定义的符号
