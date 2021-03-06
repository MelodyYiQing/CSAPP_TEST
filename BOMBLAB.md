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
we can see in 0x402400,store the second argument which are supposed to be the answer.
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
<br>This part we see`mov    $0x4025cf,%esi`,so we first use x/s to see what's in the 0x4025cf
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb8.png)
<br>And then we find this part has a restriction of the format of our input that is we need to input 2 numbers.So it's advisable that guess rsp+12 is the second argument and rsp+8 is the first argument.
<br>From `0x400f51--0x400f63`ensures that we must input 2 numbers.Jump to`0x400f6a`
<br>the second argument must <= 7,eax=first argument,and from`0x400f71`we can see it function as the case number in switch.
<br>And when we us x/s to see `*0x402470` we can see it turns to <phase3_+57>
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb9.png)
<br>so when the first argument equals 0,`jmpq *0x402470(,%rax,8)`just move to the next instruct `mov $0xcf,%eax`so cf=207=second argument.And actually it has several matches which you can choose,for the first argument ranges from 0 to 7;
<br>So lets try the answer.<br>
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb10.png)
<br>As expected it is true!
* Phase_4
<br>First disassemble it.
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb11.png)
<br>And we can see the format of input is quite similar with phase_3
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb12.png)
<br>`	cmpl   $0xe,0x8(%rsp)`along with the next instruct`jbe    0x40103a <phase_4+46>`ensures the first argument <= 14;
<br>Then the following three instructs make edx=14,esi=0,edi=first argument;
<br>It's time to enter func4.So disassemble func4.Wait!Before enter it,the following instructs ensures that func4 must return 0 to avoid explosion.
```cpp
0000000000400fce <func4>:
  400fce:	48 83 ec 08          	sub    $0x8,%rsp
  400fd2:	89 d0                	mov    %edx,%eax	//eax=x	
  400fd4:	29 f0                	sub    %esi,%eax	//eax=x-y
  400fd6:	89 c1                	mov    %eax,%ecx	//ecx=x-y
  400fd8:	c1 e9 1f             	shr    $0x1f,%ecx	//ecx>>16  0
  400fdb:	01 c8                	add    %ecx,%eax	//eax=x-y
  400fdd:	d1 f8                	sar    %eax		//eax=(x-y)/2
  400fdf:	8d 0c 30             	lea    (%rax,%rsi,1),%ecx   //ecx=(x+y)/2
  400fe2:	39 f9                	cmp    %edi,%ecx      
  400fe4:	7e 0c                	jle    400ff2 <func4+0x24>	//ecx<=z
  400fe6:	8d 51 ff             	lea    -0x1(%rcx),%edx 		//edx=(x+y)/2-1
  400fe9:	e8 e0 ff ff ff       	callq  400fce <func4>    	//recursion start
  400fee:	01 c0                	add    %eax,%eax		//return 2*func4((x+y)/2-1,0,z)
  400ff0:	eb 15                	jmp    401007 <func4+0x39>
  400ff2:	b8 00 00 00 00       	mov    $0x0,%eax		//eax= 0
  400ff7:	39 f9                	cmp    %edi,%ecx		
  400ff9:	7d 0c                	jge    401007 <func4+0x39>	//ecx>=z
  400ffb:	8d 71 01             	lea    0x1(%rcx),%esi		//ecx<=z,esi=(x+y)/2+1
  400ffe:	e8 cb ff ff ff       	callq  400fce <func4>		//return 2*func4((x+y)/2+1,0,z)
  401003:	8d 44 00 01          	lea    0x1(%rax,%rax,1),%eax
  401007:	48 83 c4 08          	add    $0x8,%rsp		
  40100b:	c3                   	retq   
```
<br>edx=14,esi=0,edi=first argument(<14);And we see `400fe9:	e8 e0 ff ff ff       	callq  400fce <func4>  ` we know there may be a recursion.
<br>when first argument >= ecx && first argument <= ecx return 0(ok), and there must be first argument =z ,so when edx =14,esi=0,ecx=7 .
<br>And quit from func4,the following compare ensures the second input must be 0;
<br>Try the answer and as expected it is right.

![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb13.png)

* Phase 5
<br>As always disassemble first.
```cpp
Breakpoint 2, 0x0000000000401062 in phase_5 ()
(gdb) disas
Dump of assembler code for function phase_5:
=> 0x0000000000401062 <+0>:	push   %rbx
   0x0000000000401063 <+1>:	sub    $0x20,%rsp
   0x0000000000401067 <+5>:	mov    %rdi,%rbx
   0x000000000040106a <+8>:	mov    %fs:0x28,%rax
   0x0000000000401073 <+17>:	mov    %rax,0x18(%rsp)
   0x0000000000401078 <+22>:	xor    %eax,%eax
   0x000000000040107a <+24>:	callq  0x40131b <string_length>
   0x000000000040107f <+29>:	cmp    $0x6,%eax
   0x0000000000401082 <+32>:	je     0x4010d2 <phase_5+112>
   0x0000000000401084 <+34>:	callq  0x40143a <explode_bomb>
   0x0000000000401089 <+39>:	jmp    0x4010d2 <phase_5+112>
   0x000000000040108b <+41>:	movzbl (%rbx,%rax,1),%ecx
   0x000000000040108f <+45>:	mov    %cl,(%rsp)
   0x0000000000401092 <+48>:	mov    (%rsp),%rdx
   0x0000000000401096 <+52>:	and    $0xf,%edx
   0x0000000000401099 <+55>:	movzbl 0x4024b0(%rdx),%edx
   0x00000000004010a0 <+62>:	mov    %dl,0x10(%rsp,%rax,1)
   0x00000000004010a4 <+66>:	add    $0x1,%rax
   0x00000000004010a8 <+70>:	cmp    $0x6,%rax
   0x00000000004010ac <+74>:	jne    0x40108b <phase_5+41>
   0x00000000004010ae <+76>:	movb   $0x0,0x16(%rsp)
   0x00000000004010b3 <+81>:	mov    $0x40245e,%esi
   0x00000000004010b8 <+86>:	lea    0x10(%rsp),%rdi
   0x00000000004010bd <+91>:	callq  0x401338 <strings_not_equal>
   0x00000000004010c2 <+96>:	test   %eax,%eax
   0x00000000004010c4 <+98>:	je     0x4010d9 <phase_5+119>
---Type <return> to continue, or q <return> to quit---return
   0x00000000004010c6 <+100>:	callq  0x40143a <explode_bomb>
   0x00000000004010cb <+105>:	nopl   0x0(%rax,%rax,1)
   0x00000000004010d0 <+110>:	jmp    0x4010d9 <phase_5+119>
   0x00000000004010d2 <+112>:	mov    $0x0,%eax
   0x00000000004010d7 <+117>:	jmp    0x40108b <phase_5+41>
   0x00000000004010d9 <+119>:	mov    0x18(%rsp),%rax
   0x00000000004010de <+124>:	xor    %fs:0x28,%rax
   0x00000000004010e7 <+133>:	je     0x4010ee <phase_5+140>
   0x00000000004010e9 <+135>:	callq  0x400b30 <__stack_chk_fail@plt>
   0x00000000004010ee <+140>:	add    $0x20,%rsp
   0x00000000004010f2 <+144>:	pop    %rbx
   0x00000000004010f3 <+145>:	retq   
End of assembler dump.
```
<br>`phase_5+24 phase_5+29`ensures a string containing 6 letters.
<br>After 2 jump,we arrive at<+41>,previous through `xor` eax=0,so here ecx=rbx. and there we get into a circulation.
<br>It makes us always get the last four bits of the string element.And search the string in 0x4024b0 accroing to the number of the four bits,than store the letter in 0x10(%rsp,%rax,1).Not until we get 6 letters can we quit the circulation.
<br> Then move 0 to rsp+24(every string has /0 as the end signal)Look into the 0x40245e we see the processed string should be 'flyers'
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb14.jpg)
<br>So the last four bits are 1001 1111 1110 0101 0110 1110.Then search the ascii(using man ascii in linux)To look for char whose last four bits are matched(a lot of).
<br>So I choose IONEFG as the answer.Try and as expected it is right.HHHH
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb15.png)
* Phase_6
<br>First disassable(a little long)
```cpp
Breakpoint 2, 0x00000000004010f4 in phase_6 ()
(gdb) disas
Dump of assembler code for function phase_6:
=> 0x00000000004010f4 <+0>:	push   %r14
   0x00000000004010f6 <+2>:	push   %r13
   0x00000000004010f8 <+4>:	push   %r12
   0x00000000004010fa <+6>:	push   %rbp
   0x00000000004010fb <+7>:	push   %rbx
   0x00000000004010fc <+8>:	sub    $0x50,%rsp
   0x0000000000401100 <+12>:	mov    %rsp,%r13
   0x0000000000401103 <+15>:	mov    %rsp,%rsi
   0x0000000000401106 <+18>:	callq  0x40145c <read_six_numbers>
   0x000000000040110b <+23>:	mov    %rsp,%r14
   0x000000000040110e <+26>:	mov    $0x0,%r12d
   0x0000000000401114 <+32>:	mov    %r13,%rbp
   0x0000000000401117 <+35>:	mov    0x0(%r13),%eax
   0x000000000040111b <+39>:	sub    $0x1,%eax
   0x000000000040111e <+42>:	cmp    $0x5,%eax
   0x0000000000401121 <+45>:	jbe    0x401128 <phase_6+52>
   0x0000000000401123 <+47>:	callq  0x40143a <explode_bomb>
   0x0000000000401128 <+52>:	add    $0x1,%r12d
   0x000000000040112c <+56>:	cmp    $0x6,%r12d
   0x0000000000401130 <+60>:	je     0x401153 <phase_6+95>
   0x0000000000401132 <+62>:	mov    %r12d,%ebx
   0x0000000000401135 <+65>:	movslq %ebx,%rax
   0x0000000000401138 <+68>:	mov    (%rsp,%rax,4),%eax
   0x000000000040113b <+71>:	cmp    %eax,0x0(%rbp)
   0x000000000040113e <+74>:	jne    0x401145 <phase_6+81>
   0x0000000000401140 <+76>:	callq  0x40143a <explode_bomb>
---Type <return> to continue, or q <return> to quit---return
   0x0000000000401145 <+81>:	add    $0x1,%ebx
   0x0000000000401148 <+84>:	cmp    $0x5,%ebx
   0x000000000040114b <+87>:	jle    0x401135 <phase_6+65>
   0x000000000040114d <+89>:	add    $0x4,%r13
   0x0000000000401151 <+93>:	jmp    0x401114 <phase_6+32>
   0x0000000000401153 <+95>:	lea    0x18(%rsp),%rsi
   0x0000000000401158 <+100>:	mov    %r14,%rax
   0x000000000040115b <+103>:	mov    $0x7,%ecx
   0x0000000000401160 <+108>:	mov    %ecx,%edx
   0x0000000000401162 <+110>:	sub    (%rax),%edx
   0x0000000000401164 <+112>:	mov    %edx,(%rax)
   0x0000000000401166 <+114>:	add    $0x4,%rax
   0x000000000040116a <+118>:	cmp    %rsi,%rax
   0x000000000040116d <+121>:	jne    0x401160 <phase_6+108>
   0x000000000040116f <+123>:	mov    $0x0,%esi
   0x0000000000401174 <+128>:	jmp    0x401197 <phase_6+163>
   0x0000000000401176 <+130>:	mov    0x8(%rdx),%rdx
   0x000000000040117a <+134>:	add    $0x1,%eax
   0x000000000040117d <+137>:	cmp    %ecx,%eax
   0x000000000040117f <+139>:	jne    0x401176 <phase_6+130>
   0x0000000000401181 <+141>:	jmp    0x401188 <phase_6+148>
   0x0000000000401183 <+143>:	mov    $0x6032d0,%edx
   0x0000000000401188 <+148>:	mov    %rdx,0x20(%rsp,%rsi,2)
   0x000000000040118d <+153>:	add    $0x4,%rsi
   0x0000000000401191 <+157>:	cmp    $0x18,%rsi
   0x0000000000401195 <+161>:	je     0x4011ab <phase_6+183>
   0x0000000000401197 <+163>:	mov    (%rsp,%rsi,1),%ecx
---Type <return> to continue, or q <return> to quit---return
   0x000000000040119a <+166>:	cmp    $0x1,%ecx
   0x000000000040119d <+169>:	jle    0x401183 <phase_6+143>
   0x000000000040119f <+171>:	mov    $0x1,%eax
   0x00000000004011a4 <+176>:	mov    $0x6032d0,%edx
   0x00000000004011a9 <+181>:	jmp    0x401176 <phase_6+130>
   0x00000000004011ab <+183>:	mov    0x20(%rsp),%rbx
   0x00000000004011b0 <+188>:	lea    0x28(%rsp),%rax
   0x00000000004011b5 <+193>:	lea    0x50(%rsp),%rsi
   0x00000000004011ba <+198>:	mov    %rbx,%rcx
   0x00000000004011bd <+201>:	mov    (%rax),%rdx
   0x00000000004011c0 <+204>:	mov    %rdx,0x8(%rcx)
   0x00000000004011c4 <+208>:	add    $0x8,%rax
   0x00000000004011c8 <+212>:	cmp    %rsi,%rax
   0x00000000004011cb <+215>:	je     0x4011d2 <phase_6+222>
   0x00000000004011cd <+217>:	mov    %rdx,%rcx
   0x00000000004011d0 <+220>:	jmp    0x4011bd <phase_6+201>
   0x00000000004011d2 <+222>:	movq   $0x0,0x8(%rdx)
   0x00000000004011da <+230>:	mov    $0x5,%ebp
   0x00000000004011df <+235>:	mov    0x8(%rbx),%rax
   0x00000000004011e3 <+239>:	mov    (%rax),%eax
   0x00000000004011e5 <+241>:	cmp    %eax,(%rbx)
   0x00000000004011e7 <+243>:	jge    0x4011ee <phase_6+250>
   0x00000000004011e9 <+245>:	callq  0x40143a <explode_bomb>
   0x00000000004011ee <+250>:	mov    0x8(%rbx),%rbx
   0x00000000004011f2 <+254>:	sub    $0x1,%ebp
   0x00000000004011f5 <+257>:	jne    0x4011df <phase_6+235>
   0x00000000004011f7 <+259>:	add    $0x50,%rsp
---Type <return> to continue, or q <return> to quit---return
   0x00000000004011fb <+263>:	pop    %rbx
   0x00000000004011fc <+264>:	pop    %rbp
   0x00000000004011fd <+265>:	pop    %r12
   0x00000000004011ff <+267>:	pop    %r13
   0x0000000000401201 <+269>:	pop    %r14
   0x0000000000401203 <+271>:	retq   
End of assembler dump.
```
<br>see the`callq 0x40145c<read_six_numbers>`,so restriction is 6 numbers.
<br>From<+23>to<+35>,these instructs makes:r14,r13,rbp,equals the address of the first argument.eax equals first argument.And<+39>to<+47>ensures first argument - 1 <=5,that is first agument <=6.
<br>Jump to <+52>,from<+52>to <+60>,because r12d is 0,and once add 1,we can deduce that it may be a circulation.And for first time ,we continute to <+62>
<br><+62>to<+74>,second argument in eax,rbp=first argument.And ensures 2 is not equal.
<br>jump to<+84>,ebx=2<5,jump to<+65> actually we can see it is a circulation to ensure the later numbers you enter all less than the first one.
<br><+89>get the second argument's address in %r13.So actually it is a binary circulation,the outer one ensures every element all smaller or equal with 6,and the inner one ensures the 6 numbers is not equal.
<br>circulation will finally stop at<+60>,jump to <+95>.rax store the first argument's address.edx=7-argument,Then do the circulation until all 6 numbers do the minus.
<br>Then arrive at<+123>esi=0,jumo to<+163>
<br>ecx=first argument,if it <=1,jump to <+143>where we meet`0x6032d0` Actually i don't know so i searched online.Use x/24x2 it is a struct and then it is just rearrange the number according to 7-x,
![bomb](https://github.com/MelodyYiQing/CSAPP_TEST/blob/master/bomb16.png)
<br>Ok.I just stop here because I can't do the next part because I have no idea about the remains.
