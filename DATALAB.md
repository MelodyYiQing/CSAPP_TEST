Datalab
====
This homework can be download from cmu,and you will get a .tar file.use`tar xvf xxx.tar`in linux can help you open this file.And the README can help you understand the homework.
-----
* bitXor - x^y using only ~ and &
  <br>Example: bitXor(4, 5) = 1
  <br>Legal ops: ~ &
  <br>Max ops: 14
  <br>Rating: 1
<br>`Analyze:`<br>`x^y=(x&~y)|(~x&y)`<br>`(x&~y)：when x=1,y=0,remain 1   (y&~x):when x=0,y=1,remain 1;`
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
* isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
<br>Example: isAsciiDigit(0x35) = 1.
 <br> isAsciiDigit(0x3a) = 0.
 <br>isAsciiDigit(0x05) = 0.
  <br>   Legal ops: ! ~ & ^ | + << >>
 <br>Max ops: 15
 <br>Rating: 3
<br>`Analyze:`<br>`When x is between 30 to 39,x-0x39 <=0 and x-0x30>=0,because when we define a number is positive or not ,we look at the highest bit ,whether it is 0 or not .So it's better to convert x-0x39<=0 to x-0x3A<0.`
```cpp
int isAsciiDigit(int x) 
{
  return (!((x+~48+1)>>31)&!!((x+~58+1)>>31);
}
```
* conditional - same as x ? y : z
<br> Example: conditional(2,4,5) = 4
<br>    Legal ops: ! ~ & ^ | + << >>
<br>    Max ops: 16
<br>   Rating: 3
<br>`Analyze:`
<br>` that is (a&y)|(b&z) and make a =0xffffffff(0-1),b=0(1-1)  (x!=0) a=0,b=0xffffffff (x=0)`<br>`so a=(!x+~1+1)  b=(~!x+1)`
```cpp
int conditional(int x, int y, int z)
{
  return ((!x+~1+1)&y)|((~!x+1)&z);
}
```
* isLessOrEqual - if x <= y  then return 1, else return 0
<br>  Example: isLessOrEqual(4,5) = 1.
<br>  Legal ops: ! ~ & ^ | + << >>
<br>  Max ops: 24
<br> Rating: 3
<br>`Analyze:`
<br>`2 conditions: x and y are both positive or negative.or not.`<br>`case 1: compare x-y<=0 => x+~y<0    case 2:see whether x is positive or not `
```cpp
int isLessOrEqual(int x, int y) {
    int sign_x=x>>31;
    int sign_y=y>>31;
    int equal =!(sign_x^sign_y)&((~y+x)>>31);
    int notequal=sign_x^!sign_y;
    return equal|notequal;
```
* logicalNeg - implement the ! operator, using all of
             the legal operators except !
<br> Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 <br>   Legal ops: ~ & ^ | + << >>
 <br>   Max ops: 12
 <br>   Rating: 4
<br>`Analyze:`
<br>`3 conditions: when x!=0 or Tmin,sign of x & sign of ~x =0 ; x=0 ;x= Tmin; `<br>`and Tmin +1 =0 and ~0=Tmin so`
```cpp
int logicalNeg(int x) {
  return ((~(~x+1)&~x)>>31)&1;
}
```
* howManyBits - return the minimum number of bits required to represent x in
             two's complement
 <br>  Examples: howManyBits(12) = 5
 <br>            howManyBits(298) = 10
<br>            howManyBits(-5) = 4
 <br>           howManyBits(0)  = 1
 <br>            howManyBits(-1) = 1
<br>            howManyBits(0x80000000) = 32
 <br>  Legal ops: ! ~ & ^ | + << >>
 <br>  Max ops: 90
<br>  Rating: 4
<br> `Analyze:`
<br>`Like Binary search , and add 2 to the sum(one for sign and another is at least you should have one `
```cpp
int howManyBits(int x) {
    int shift1,shift2,shift4,shift8,shift16;
    int sum;
    int t =((!x)<<31)>>31; // whether = 0?
    int t2=((!~x)<<31)>>31;// whether = Tmin?
    int op=x^(x>>31);//make negative positive
    shift16=(!!(op>>16))<<4;//2^4=16
    op=op>>shift16;
    shift8=(!!(op>>8))<<3;
    op=op>>shift8;
    shift4=(!!(op>>4))<<2;
    op=op>>shift4;
    shift4=(!!(op>>2))<<1;
    op=op>>shift2;
    shift1=!!(op>>1);
    op=op>>shift1;
    sum=2+shift1+shift2+shift4+shift8+shift16;
  return (t2&1)|((~t2)&((t&1)|(~t)&sum));
}
```
* floatScale2 - Return bit-level equivalent of expression 2*f for floating point argument f.
<br>   Both the argument and result are passed as unsigned int's, but they are to be interpreted as the bit-level representation of  single-precision floating point values.
 <br> When argument is NaN, return argument
<br>Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
<br>   Max ops: 30
<br> Rating: 4
<br>`Analyze:`
<br>`First we need to dispose the float into sign ,exponent,fraction.Then judge it,if exp=0xff,then just print 0x7f800000,else if exp =0,just move 1 bit of the float leftwards.For normalized number,just exp+1.`
<br>`Attention:if the exp=0x11111110,then need to print NAN`
 ```cpp
unsigned floatScale2(unsigned uf) {
    unsigned sign =(uf&0x80000000);
    unsigned exp=(uf&0x7f800000);
    unsigned frac=(uf<<9)>>9;
    if(!exp)
    {
        frac=frac<<1;
        return sign|frac;
    }
    else if(!(exp^0xff))
    {
        return uf;
    }
    else if(!(exp^0xfe))
    {
        return sign|0x7f800000;
    }
    else
        {
            return (((exp+1)<<23)|sign)|frac;
        }
}
```
* floatFloat2Int - Return bit-level equivalent of expression (int) f for floating point argument f.
<br> Argument is passed as unsigned int, but it is to be interpreted as the bit-level representation of a single-precision floating point value.
<br>   Anything out of range (including NaN and infinity) should return  0x80000000u.
<br>  Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
<br> Max ops: 30
<br>  Rating: 4
<br>`Analyze:`
<br>`if exp<=127,just return 0 (the value is <1),if(exp>158(127+31(because normalized number has 1 already),print out NAN;else do normal process;`
<br>`If exp-127<=23,then the fraction may not maintain,some may lost.Else,all bits of fraction will be remained.So just enlarge the fraction first them judge the exp to move leftwards or rigntwards.`
```cpp
int floatFloat2Int(unsigned uf) {
    unsigned sign =(uf&0x80000000);
    unsigned exp =(uf & 0x7f800000)>>23;
    unsigned frac = (1<<23)|((uf<<9)>>9);
    int E = exp-127;
    int val;
    if (E<=0) return 0;
    if(exp>=158) return 0x80000000;
    if(E<=23)
    {
        val=(frac>>(23-E));
        return (!sign?val:-val);
    }
    else
    {
        val=(frac<<(23-E));
        return (!sign?val:-val);
    }
}
```
* floatPower2 - Return bit-level equivalent of the expression 2.0^x  (2.0 raised to the power x) for any 32-bit integer x.
<br>The unsigned value that is returned should have the identical bit representation as the single-precision floating-point number 2.0^x.
<br>If the result is too small to be represented as a denorm, return   0. If too large, return +INF.
<br>  Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while
<br>  Max ops: 30
<br>   Rating: 4
<br>`Analyze:`
<br>`Because the largest float is (1.111~11)<<(254-127) so x should <=127,else print out 0x7f800000;the smallest shoule be (0.00000~~01)>>(1-127),so x should >=-149,else print 0;`
<br>`If x <=-127,then just move the fraction rigntward.Else move the exponent(exp=x+127) then leftward 23 bits(out of the fraction part)`
```cpp
unsigned floatPower2(int x) {
    int frac = x+149;
    if(x>127) return 0x7f800000;
    if(x<-149) return 0;
    if(x<=-127)
    {
        return 1u<<frac;
    }
    else
    {
        unsigned exp=x+127;
        return exp<<23;
    }
}
```
