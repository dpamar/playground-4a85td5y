# The new RPN calc tool

Let's enhance our calc tool
* 10 (carriage return) is a valid code we can leverage
* Let's have a dual Space+(print "> ")+P+Space behavior on Enter
  * Space is a number or command separator : previous input is over
  * P prints the result (first entry on the stack)
  * Space is a number or command separator : previous "pseudo input" (P) is over
* we can keep "P" for print (and space as separator of course)

_Note_: Print starts with some < and space is a > : let's not write ><<< but just << instead

_Note 2_: we can remove the line break after "P" print. Enter will do it, and P just prints the value.

# Let's start

* Memory: empty
* Cursor: first cell
* Input: space separated integers and operators

# Process

* Leave an empty space (current number on stack is null)
* While there is a char to read
  * subtract **10** and set else flag
  * if char is null : it's Enter
    * reset else flag, print stack first value, move to the right
  * subtract **22**, if char is null : it's space
    * reset else flag, move to the right
  * subtract 5 (total 37), if char is null : it's modulus
    * reset else flag, compute A mod B
  * subtract 5 (total 42), if char is null : it's multiply
    * reset else flag, multiply the 2 operands
  * subtract 1 (total 43), if char is null : it's addition
    * reset else flag, sum the 2 operands
  * subtract 2 (total 45), if char is null : it's subtraction
    * reset else flag, subtract the 2 operands
  * subtract 2 (total 47), if char is null : it's division
    * reset else flag, divide the 2 operands
  * subtract 18 (total 65), if char is null : it's ASCII print
    * reset else flag, print first operand as char
  * subtract 2 (total 67), if char is null : it's copy
    * reset else flag, copy first operand
  * subtract 1 (total 68), if char is null : it's drop
    * reset else flag, drop first operand
  * subtract 12 (total 80), if char is null : it's print
    * reset else flag, print first operand
  * subtract 2 (total 82), if char is null : it's rot
    * reset else flag, rotate stack's head
  * subtract 1 (total 83), if char is null : it's swap
    * reset else flag, swap first 2 operands
  * otherwise
    * add 35 (to get the digit value)
    * add current number 10 times to current digit to get new current number
* loop

# Code
```
#!/usr/local/bin/bf
>,[                     leave empty space and read char
  let's track our target values here
  enter 10
  space 32
  modulus 37
  multiply 42
  add 43
  subtract 45
  divide 47
  ascii print 65
  copy 67
  drop 68
  print 80
  rot 82
  swap 83
  digit 48 to 57

  ---------->+<         subtract 10
  [>+++++[-<---->]+<++  subtract 22
  [-----                subtract 5
  [-----                subtract 5
  [-                    subtract 1
  [--                   subtract 2
  [--                   subtract 2
  [>+++++[-<--->]+<     subtract 18
  [--                   subtract 2
  [-                    subtract 1
  [------------         subtract 12
  [--                   subtract 2
  [-                    subtract 1
  [                     it's a digit
    >++++++[-<+++++>]<  add 35 to get digit value (and reset else flag)
    <[->++++++++++<]    add current number 10 times to current digit
    >[-<+>]             replace current number by current digit
  ]>[-                  now unwind cases: it's swap
    <<<[->+<]           move A to the right
    <[->+<]             move B at A initial location
    >>[-<<+>>]>         move A where B was initially
  ]<]>[-                next case: it's rot
    <<<[->+<]<[->+<]<[- move A and B and C to the right
    >+<]>>>[-<<<+>>>]>    and move A where C was initially
  ]<]>[-                next case: it's print
    <<<[->+>+<<]>[-<+>] copy number (to remain value unchanged after print)
    >[>>>>++++++++++<<< print copied value
    <[->+>>+>-[<-]<[->>   as a decimal number
    +<<<<[->>>+<<<]>]<<   ** part 3 **
    ]>+[-<+>]>>>[-]>[-<   ** part 4 **
    <<<+>>>>]<<<<]<[>++   ** part 5 **
    ++++[<++++++++>-]<-   ** part 6 **
    .[-]<]>               ** part 7 **
  ]<]>[-                next case: it's drop
    <<<[-]>             remove stack first value
  ]<]>[-                next case: it's copy
    <<<[->+>+<<]        duplicate stack first value
    >>[-<<+>>]>         move duplicate back to original location
  ]<]>[-                next case: it's ASCII print
    <<<.>>              Print value as ASCII char
  ]<]>[-                next case: it's division
    <<<[->>>+<<<]<      move 2nd operand
    [->+>>+>-[<-]<[->>+ divide
    <<<<[->>>+<<<]>]<<]   ** part 2 **
    >[-]>>>[-]>[-<<<<   move result
    <+>>>>>]<<<           ** part 2**
  ]<]>[-                next case: it's subtraction
    <<<[-<->]>          do the subtraction
  ]<]>[-                next case : it's add
    <<<[-<+>]>          do the addition
  ]<]>[-                next case : it's multiply
    <<<[->>+<<]>>[-<<<  do the multiplication
    [->+>+<<]>[-<+>]>>    ** part 2 **
    ]<<<[-]>>[-<<+>>]     ** part 3 **
  ]<]>[-                next case : it's modulo
    <<<[->>>+<<<]<      move 2nd operand
    [->+>>+>-[<-]<[->>+ divide
    <<<<[->>>+<<<]>]<<]   ** part 2 **
    >[-<+>]>>>[-]       move result
    >[-]<<<               ** part 2 **
  ]<]>[-                next case : it's space
    >                   move to next stack entry
  ]<]>[-                next case : it's Enter 
    >++++++++           prepare chars 62 and 32
    [-<++++++++<++++>>] 64 and 32
    <--.[-]<.[-]<       print and reset
    [->+>+<<]>[-<+>]    move on stack and copy number
    >[>>>>++++++++++<<< print copied value
    <[->+>>+>-[<-]<[->>   as a decimal number
    +<<<<[->>>+<<<]>]<<   ** part 3 **
    ]>+[-<+>]>>>[-]>[-<   ** part 4 **
    <<<+>>>>]<<<<]<[>++   ** part 5 **
    ++++[<++++++++>-]<-   ** part 6 **
    .[-]<]                ** part 7 **
    ++++++++++.[-]>     print newline (and reset newline char)
    >                   move to next stack entry
  ]                     end of switch/case
<,]                     read char and loop
```

# Final state

* Memory: stack of integers
* Cursor: after stack
* Input: empty (read)
* Output: unchanged

# Test program

The program can be tested on its own.

```
./rpn.bf
```

Then enjooy you command line RPN calc

Example
```
1 2 +
> 3
3 *
> 9
C C 3 / P 10 A D * *
3
> 243
123 C P 32 A 47 A D A 5 P S A 61 A D A R C R / P D R 44 A S A S 5 * 2 / 4 + A 13 - A 8 + A D A D S % P 10 A D
123 / 5 = 24, rem 3
> 3
```

1+2 = 3, 3x3 = 9, 2 copies= 9,9,9, then 9/3 = 3 (printed) and finally 9x3=27 and 9x27 = 243
and for the long line, it prints the " / ", " = " and ", rem " from ASCII codes.