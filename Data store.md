Different data in different area 
----
function of malloc and free is stored in the stack.
<br>the area applied by malloc is stored in heap.Heap is a dynamic space,which grows from two directions.
<br>arguments and local variable is in the stack.Stack is an area grows from upper to lower.
<br>global and static is in the data area where is far below the stack and heap.
![store](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/Storage(cr.CMU).png)
```cpp
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>

static void show_pointer(void *p, char *descr) {
    //    printf("Pointer for %s at %p\n", descr, p);
    printf("%s\t%p\t%lu\n", descr, p, (unsigned long) p);
}

char big_array[1L<<24];    /*  16 MB */
//char huge_array[1L<<31];   /*   2 GB */
char huge_array[1L<<30];/*   1 GB */
int global = 0;

int useless() { return 0; }

int main ()
{
    void *p1, *p2, *p3, *p4;
    int local = 0;
    p1 = malloc(1L << 28);
    p2 = malloc(1L << 8);
    //p3 = malloc(1L << 32);
	p3 = malloc(1L << 16);
    p4 = malloc(1L << 8);

    show_pointer((void *) big_array, "big array");
    show_pointer((void *) huge_array, "huge array");
    show_pointer((void *) &local, "local");
    show_pointer((void *) &global, "global");
    show_pointer((void *) p1, "p1");
    show_pointer((void *) p2, "p2");
    show_pointer((void *) p3, "p3");
    show_pointer((void *) p4, "p4");
    show_pointer((void *) useless, "useless");
    show_pointer((void *) exit, "exit");
    show_pointer((void *) malloc, "malloc");
    return 0;
}
```
Outcome
--
![locate](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/locate.png)
