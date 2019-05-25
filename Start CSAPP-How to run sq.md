```cpp
#include <stdio.h>
#include <stdlib.h>

int sq(int x) {
    return x*x;
}

int main(int argc, char *argv[]) {          //argc:how many char you enter in .char* argv[]:The string you enter in.
    int i;
    for (i = 1; i < argc; i++) 
    {
	int x = atoi(argv[i]);                    //int atoi(const char *str):convert string into int.
	int sx = sq(x);
	printf("sq(%d) = %d\n", x, sx);
    }
    return 0;
} 
```
This is the first program i ran on Linux <br>
First,you need to enter in'cd/ mnt'(mount挂载）<br>
Then,enter in hgfs.<br>
cd means enter into,and ls can help us search what's in the foldler<br>
Here we can see the .c files that we can run.<br>
Choose one the gcc -o 'give the file a name' 'file.c'<br>
Some times -O1 -O2 can optimize our program at different degree.<br>
Then ./'file name'.out 'the argument that you may want to enter in'<br>
--
