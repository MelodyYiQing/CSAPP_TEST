Show_Bytes
----
Problems
---
<br>1.Why use unsigned char?
<br>unsigned char *ensures that even if the address overflows it wouldn't extend its bits.while char* int* considering they have the sign bit if overflow they will extend thus lead to wrong answer
<br>2.This programme can help us to see whether the computer is little endian or not.
```cpp
/*show-bytes - prints byte representation of data */
/* $begin show-bytes */
#include <stdio.h>
/* $end show-bytes*/
#include <stdlib.h>
#include <string.h>
/* $begin show-bytes */

typedef unsigned char *byte_pointer;                      //unsigned char *ensures that even if the address overflows it wouldn't extend its bits
//typedef char *byte_pointer;                             //while char* int* considering they have the sign bit if overflow they will extend thus lead to wrong answer
//typedef int *byte_pointer;

void show_bytes(byte_pointer start,size_t len){
	size_t i;					//size_t is defined as unsigned int in C
	for(i = 0;i<len;i++)
        printf("%p\t0x%.2x\n",&start[i],start[i]);      //%p print the pointer. %.2x :if less than 2 bits add 0 else print all(even more than 2 bits)
	printf("\n");                                          //explain why change the typedef of byte_pointer,the outcome varies some showing 8 bits
  }                                                      

void show_int(int x){
	show_bytes((byte_pointer)&x,sizeof(int));
}

void show_float(float x){
	show_bytes((byte_pointer)&x,sizeof(float));
}

void show_pointer(void *x){
	show_bytes((byte_pointer)&x,sizeof(void*));         //void* can point to any type of data
}
/* $end show-bytes */


/* $begin test-show-bytes */
void test_show_bytes(int val){
	int ival = val;
	//float fval =(float)ival;            //though int-to-float won't overflow but the accuracy may lost
	double fval = (double) ival;
	int *pval = &ival;
	printf("Stack variable ival =%d\n",ival);
	printf("(int)ival:\n");
	show_int(ival);
	printf("(float)ival:\n");
	show_float(fval);
	printf("&ival:\n");
	show_pointer(pval);
}
/* $end test-show-bytes */

void simple_show_a(){
/* $begin simple-show-a */
	int val= 0x87654321;
	byte_pointer valp=(byte_pointer)&val;
	show_bytes(valp,1); /* A. */                      //test type of endian
	show_bytes(valp,2); /* B. */
	show_bytes(valp,3); /* C. */
/* $end simple-show-a */
}

void simple_show_b(){
/*	$begin simple-show-b */
	int val=0x12345678;
	byte_pointer valp =(byte_pointer)&val;
	show_bytes(valp,1); /* A */
	show_bytes(valp,2); /* B */
	show_bytes(valp,3); /* C */
/* $end simple-show-b */
}

void float_eg(){
	int x = 3490593;
	float f=(float) x;
	printf("For x =%d\n",x);
	show_int(x);			//x in hexa=0x354321
	show_float(f);		

	x = 3410593;
	f=(float) x;
	printf("For x =%d\n",x);
	show_int(x);
	show_float(f);
}

void string_ueg() {
/* $begin show-ustring */
    const char *s = "ABCDEF";
    show_bytes((byte_pointer)s, strlen(s));
/* $end show-ustring */
}

void string_leg(){
/* $begin show-lstringm*/
	const char *s="abcdef";
	show_bytes((byte_pointer)s,strlen(s));
/* $end show-lstring */
}

void show_twocomp()
{
/* $begin show-twocomp */
	short x=12345;     					//12345=0x3039(little endian)
	short mx=-x;	  					//-12345=0xcfc7(little endian)

	show_bytes((byte_pointer)&x,sizeof(short));
	show_bytes((byte_pointer)&mx,sizeof(short));
/* $end show-twocomp */
}

int main (int argc,char* argv[])
{
	int val=12345;
	if(argc>1){
	val=strtol(argv[1],NULL,0);                   //convert to type long
	printf("calling test_show_bytes\n");
	test_show_bytes(val);
	}
	else
    {
	printf("calling show_twocomp\n");
	show_twocomp();
	printf("Calling simple_show_a\n");
	simple_show_a();
	printf("Calling simple_show_b\n");
	simple_show_b();
	printf("Calling float_eg\n");
	float_eg();
	printf("Calling string_ueg\n");
	string_ueg();
	printf("Calling string_leg\n");
	string_leg();
	}
	return 0;
} //no wrong
```
