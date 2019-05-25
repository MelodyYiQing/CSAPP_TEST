Though when two float conduct addtion there will be no overflow but the accuracy maybe lost when matching the exponent
---
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define BUFSIZE 256

int main(int argc, char *argv[]) {
  char prefix[BUFSIZE];
  char next[BUFSIZE];
    int i;
    float sum = 0.0;
  for (i = 1; i < argc; i++) 
  {
	float x = atof(argv[i]);    //convert the string into float.
	sum += x;
	  if (i == 1)
    {
	  sprintf(prefix, "%.4g", x);       //*sprintf:enter the int x into string prefix in the form of %g(using e or f)
	  } //end if
    else {
	  sprintf(next, " + %.4g", x);
	  strcat(prefix, next);            //char *strcat(char *dest, const char *src):add the src at the end of des
	  printf("%s = %.4g\n", prefix, sum);   //strcpy just move the src to dest
	  }//end else
  }
    return 0;
}
```
OUTCOME
---
![struct](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/fsum.png)
