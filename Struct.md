Segment Fault
-----
Because the compiler can not detect the problem of the stack overflow,so when we enter the arguments,we should be really careful.
<br>In this case 1073741824 is a really big number,and we only prepare 2 int to accommodate it.
<br>So if the number of the arguments goes beyond 2, the extra number will wash out the double 3.14.
<br>And when enter more arguments,it will wash out our return address then the program will commit alarm.(Since our computer will place a canary to guard the security of our return address.
<br>And sometimes when we enter 1 there's nothing wrong that is because the compiler always make the space a little larger then our 
declaration for prepration in case.
<br>This also tells us that our stack doesn't have the ability to tell wheter the array is illegal.
```cpp
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int a[2];             //int a[] first enter into stack. Here is the promblem.a[] is at the upper. 
    double d;             //double d then enter the stack
} struct_t;

double fun(int i) {
    volatile struct_t s;
    s.d = 3.14;
    s.a[i] = 1073741824; /* Possibly out of bounds */    
    return s.d; /* Should be 3.14 */
}

int main(int argc, char *argv[]) {
    int i = 0;
    if (argc >= 2)
	i = atoi(argv[1]);
    double d = fun(i);
    printf("fun(%d) --> %.10f\n", i, d);
    return 0;
}
```
Outcome
----
![struct.c](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/struct.c.png)

