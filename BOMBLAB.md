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
