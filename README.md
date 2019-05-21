CS:APP Learning Blog & Semester sumary
==
This semester I had learnt some part of the CS:APP.So cosidering my performance score(this blog is part of the score)and also for following review,here I write some understanding about all the programs we ran this semester and the two projects of the CMU(datalab & bomblab).It's my first time to write a learning blog with my poor english,so just read it for fun.
<br>Some of the programs are cited from our class ppts,but I carefully write the annotation.Also I do the 2 tests (datalab&bomblab)from CMU.
<br>`Since I'm still confused about how to use the github to write a goodlooking blog.All that I want to convey is added to the annotation above or below the code blocks.`
<br>#Datalab
* bitXor - x^y using only ~ and &
  <br>Example: bitXor(4, 5) = 1
  <br>Legal ops: ~ &
  <br>Max ops: 14
  <br>Rating: 1
<br>`Analyze:`<br>`x^y=(x&~y)|(~x&y)<br>(x&~y)：when x=1,y=0,remain 1   (y&~x):when x=0,y=1,remain 1;`
<br>`| :when x and y both are zero get zero   &:when x and y both are one get one; so there's some similarity;`
<br>`we can convert  (x|y) to ((~x)&(~y))`
```cpp
int bitXor(int x, int y)
{
  return ~(~(~x&y)&~(x&~y));
}
```
* tmin - return minimum two's complement integer
<br>Legal ops: ! ~ & ^ | + << >>
<br>Max ops: 4
<br>Rating: 1
<br>`Analyze:`
<br>`tmin there's 1 bit of 1 and 31 bits of 0`
```cpp
int tmin(void)
{
  return 1<<31;
}
```
* isTmax - returns 1 if x is the maximum, two's complement number, and 0 otherwise
<br> Legal ops: ! ~ & ^ | +
<br> Max ops: 10
<br> Rating: 1
<br>`Analyze:`
<br>`Tmax+1=Tmin then just convert the problem to how to assure Tmin`
<br> `Tmin+Tmin=0 and considering 0+0=0 but !!x only when x=0 we get 0`
<br> `and (!(x+x))&(!!x) only when x=Tmin get 1`
```cpp
int isTmax(int x) 
{
  return (!(x+1+x+1))&(!!(x+1)) ;
}
```
* allOddBits - return 1 if all odd-numbered bits in word set to 1
<br> where bits are numbered from 0 (least significant) to 31 (most significant)
<br>Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
<br>Legal ops: ! ~ & ^ | + << >>
<br>Max ops: 12
<br>Rating: 2
<br>`Analyze:`
<br>`if x meet the requirement ,when x | 0x55555555(even bits are 1),get Tmin;`<br>`and !~Tmin get 1;`
<br>`0x55555555=0x55+0x55<<8+0x55<<16+0x55<<24`
```cpp
int allOddBits(int x) 
{
    int a=85;
    int b=a+(a<<8)+(a<<16)+a(<<24);
  return !~(x|b);
}
```
 * negate - return -x
<br>Example: negate(1) = -1.
<br>Legal ops: ! ~ & ^ | + << >>
<br> Max ops: 5
<br> Rating: 2
<br>`Analyze:`
<br>`-x=~x+1`
```cpp
int negate(int x) {
  return (~x)+1;
}
```
