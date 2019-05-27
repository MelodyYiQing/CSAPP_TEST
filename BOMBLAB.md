BOMBLAB
----
Learn gdb
----
<br>`break` set breakpoint
<br>`run` run the program
<br>`disas` disassemble the block
<br>`info registers` to see whats in the registers
<br>`print $eax`printf the register
<br>`stpi`run by step
* Phase_1
first see the bomb.c
```cpp
    input = read_line();             /* Get input                   */
    phase_1(input);                  /* Run the phase               */
    phase_defused();                 /* Drat!  They figured it out!
				      * Let me know how they did it. */
    printf("Phase 1 defused. How about the next one?\n");
```    
Then we use `objdump-d bomb > bomb.txt`to see the disassembled code.
```cpp

0000000000400ee0 <phase_1>:
  400ee0:	48 83 ec 08          	sub    $0x8,%rsp
  400ee4:	be 00 24 40 00       	mov    $0x402400,%esi
  400ee9:	e8 4a 04 00 00       	callq  401338 <strings_not_equal>
  400eee:	85 c0                	test   %eax,%eax
  400ef0:	74 05                	je     400ef7 <phase_1+0x17>
  400ef2:	e8 43 05 00 00       	callq  40143a <explode_bomb>
  400ef7:	48 83 c4 08          	add    $0x8,%rsp
  400efb:	c3                   	retq   
```
we can see in 0x402400,store the second argument which are supposed to the answer.
<br>we first set the breakpoint at phase_1 and explode_bomb then run the program.When asked enter a string ,enter sth casually(I just input my english name.hh)
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/Bomb1.png)
<br>Then we stop at the breakpoint of phase_1,the use disas to disassemble it and it can be seen same with our objdump file.Use the print and x can see what we put in.
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb2.png)
<br>Then we use x/s to see what's in the esi,which should be the answer of the question.
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb3.png)
<br>Then we can use `quit` to quit the gdb and enter it again to test the answer.This time we set one more breakpoint of phase_2 in advance to do the next part.And because when we enter continue,it doesn't stop at the explode_bomb,so the answer is right.Then we casually enter an argument to run the next code.
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb4.png)
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb5.png)
* Phase_2
<br>First disassemble the code again.
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb6.png)
<br>This one is bit harder and from several `cmp` instructs it is clear that this answer is composed of several numbers.
<br>So after `callq <read_six_numbers>`,from`cmpl $0x1,(%rsp)`we can deduce that the first number is `1`;
<br>then we jump to `0x400f30`(from former deduction we now know (%rsp)=1),the instruct is `lea 0x4(%rsp),%rbx`so in rbx we get the second argument;
<br>and then from`lea 0x18(%rsp),%rbp`,rbp no meaning because it's the seventh argument ;
<br>then jump to `0x400f17` there,the eax get 1(eax=first argument);
<br>then the `add %eax %eax` double the eax,so the eax is 2 now;
<br>then do the compare,so the second argument is 2;<br>jump to `0x400f25`,third argument in rbx;and here we know actually it is a circulation,and rbp is like a guard.
<br>`cmp`beacuse rbp is the seventh argument they are not equal, so jump to`0x400f17`eax=2,then double again get 4 so it's the third argument =4;
<br>So from the former code we can conclude it is a progressive increase.and the answer is 1 2 4 8 16 32.
<br>Then we quit and test it again.And meanwhile make preparation for the third part.
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb7.png)
<br>As expected it is right!
* Phase_3
<br>Always disassemble the code first.
```cpp
Breakpoint 3, 0x0000000000400f43 in phase_3 ()
(gdb) disas
Dump of assembler code for function phase_3:
=> 0x0000000000400f43 <+0>:	sub    $0x18,%rsp
   0x0000000000400f47 <+4>:	lea    0xc(%rsp),%rcx
   0x0000000000400f4c <+9>:	lea    0x8(%rsp),%rdx
   0x0000000000400f51 <+14>:	mov    $0x4025cf,%esi
   0x0000000000400f56 <+19>:	mov    $0x0,%eax
   0x0000000000400f5b <+24>:	callq  0x400bf0 <__isoc99_sscanf@plt>
   0x0000000000400f60 <+29>:	cmp    $0x1,%eax
   0x0000000000400f63 <+32>:	jg     0x400f6a <phase_3+39>
   0x0000000000400f65 <+34>:	callq  0x40143a <explode_bomb>
   0x0000000000400f6a <+39>:	cmpl   $0x7,0x8(%rsp)
   0x0000000000400f6f <+44>:	ja     0x400fad <phase_3+106>
   0x0000000000400f71 <+46>:	mov    0x8(%rsp),%eax
   0x0000000000400f75 <+50>:	jmpq   *0x402470(,%rax,8)
   0x0000000000400f7c <+57>:	mov    $0xcf,%eax
   0x0000000000400f81 <+62>:	jmp    0x400fbe <phase_3+123>
   0x0000000000400f83 <+64>:	mov    $0x2c3,%eax
   0x0000000000400f88 <+69>:	jmp    0x400fbe <phase_3+123>
   0x0000000000400f8a <+71>:	mov    $0x100,%eax
   0x0000000000400f8f <+76>:	jmp    0x400fbe <phase_3+123>
   0x0000000000400f91 <+78>:	mov    $0x185,%eax
   0x0000000000400f96 <+83>:	jmp    0x400fbe <phase_3+123>
   0x0000000000400f98 <+85>:	mov    $0xce,%eax
   0x0000000000400f9d <+90>:	jmp    0x400fbe <phase_3+123>
   0x0000000000400f9f <+92>:	mov    $0x2aa,%eax
   0x0000000000400fa4 <+97>:	jmp    0x400fbe <phase_3+123>
   0x0000000000400fa6 <+99>:	mov    $0x147,%eax
---Type <return> to continue, or q <return> to quit---
   0x0000000000400fab <+104>:	jmp    0x400fbe <phase_3+123>
   0x0000000000400fad <+106>:	callq  0x40143a <explode_bomb>
   0x0000000000400fb2 <+111>:	mov    $0x0,%eax
   0x0000000000400fb7 <+116>:	jmp    0x400fbe <phase_3+123>
   0x0000000000400fb9 <+118>:	mov    $0x137,%eax
   0x0000000000400fbe <+123>:	cmp    0xc(%rsp),%eax
   0x0000000000400fc2 <+127>:	je     0x400fc9 <phase_3+134>
   0x0000000000400fc4 <+129>:	callq  0x40143a <explode_bomb>
   0x0000000000400fc9 <+134>:	add    $0x18,%rsp
   0x0000000000400fcd <+138>:	retq   
End of assembler dump.
```
we also get the func4 disassenmbled
```cpp

0000000000400fce <func4>:
  400fce:	48 83 ec 08          	sub    $0x8,%rsp
  400fd2:	89 d0                	mov    %edx,%eax
  400fd4:	29 f0                	sub    %esi,%eax
  400fd6:	89 c1                	mov    %eax,%ecx
  400fd8:	c1 e9 1f             	shr    $0x1f,%ecx
  400fdb:	01 c8                	add    %ecx,%eax
  400fdd:	d1 f8                	sar    %eax
  400fdf:	8d 0c 30             	lea    (%rax,%rsi,1),%ecx
  400fe2:	39 f9                	cmp    %edi,%ecx
  400fe4:	7e 0c                	jle    400ff2 <func4+0x24>
  400fe6:	8d 51 ff             	lea    -0x1(%rcx),%edx
  400fe9:	e8 e0 ff ff ff       	callq  400fce <func4>
  400fee:	01 c0                	add    %eax,%eax
  400ff0:	eb 15                	jmp    401007 <func4+0x39>
  400ff2:	b8 00 00 00 00       	mov    $0x0,%eax
  400ff7:	39 f9                	cmp    %edi,%ecx
  400ff9:	7d 0c                	jge    401007 <func4+0x39>
  400ffb:	8d 71 01             	lea    0x1(%rcx),%esi
  400ffe:	e8 cb ff ff ff       	callq  400fce <func4>
  401003:	8d 44 00 01          	lea    0x1(%rax,%rax,1),%eax
  401007:	48 83 c4 08          	add    $0x8,%rsp
  40100b:	c3                   	retq   
```
<br>This part we see`mov    $0x4025cf,%esi`,so we first use x/s to see what's in the 0x4025cf
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb8.png)
<br>And then we find this part has a restriction of the format of our input that is we need to input 2 numbers.So it's advisable that guess rsp+12 is the second argument and rsp+8 is the first argument.
<br>From `0x400f51--0x400f63`ensures that we must input 2 numbers.Jump to`0x400f6a`
<br>the second argument must <= 7,eax=first argument,and from`0x400f71`we can see it function as the case number in switch.
<br>And when we us x/s to see `*0x402470` we can see it turns to <phase3_+57>
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb9.png)
<br>so when the first argument equals 0,`jmpq *0x402470(,%rax,8)`just move to the next instruct `mov $0xcf,%eax`so cf=207=second argument.And actually it has several matches which you can choose,for the first argument ranges from 0 to 7;
<br>So lets try the answer.
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb10.png)
<br>As expected it is true!
