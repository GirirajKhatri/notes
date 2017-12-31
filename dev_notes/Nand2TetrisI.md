
# Week1 


## Introduction

### 0.0 Introduction

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_layers.png)

 - book: `<<The Elements of Computing System>>`

 - N2T , toolsuit
    - http://nand2tetris.org/software.php


## Boolean Functions and Gate Logic

### 1.2 Boolean Function Synthesis

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_truth_tab_2_bool_expr.png)

 - Truth Table to Boolean Expression ?
    - constructing a disjuctive normal form formula  构建一个分离的范式公式 
 - how
    1. go row by row in the truth table, focus only on the rows have a value(f) 1 
        - for example , the 1st row
    2. 写一个 范式，to get a value of 1 only at this row
        - that is， 只有 这一行 会得到 1， 其它都是 0
        - i.e. `( NOT(x) AND NOT(y) and NOT(z) )`
    3. do same operation on other row that have value 1
        - 3th :
            - `( NOT(x) AND y AND NOT(z) )` 
        - 5th :
            - `( x AND NOT(y) AND NOT(z)  )`
    4. 剩下的，就是把 这三个 范式， 用 OR 连接起来
 - Tips: 
    - 因为设计 logic gate最基本的单元是 Nand, 所以有时后把 truth table反一下考虑 会有更好的效果
 - 很长的范式，我们怎么能得到一个 最小范式呢？ 
    - In fact, this is an NP-hard problem.  

 - Theorem
    - Any Boolean function can be represented using an expression containing AND, OR and NOT operations.
    - Any Boolean function can be represented using an expression containing AND, NOT operations.
    - 还能更少吗？
 - There is yet another operation that by itself does suffice to actually compute everything -- the NADN function

x | y | NAND 
--- | --- | --- 
0 | 0 | 1
0 | 1 | 1 
1 | 0 | 1
1 | 1 | 0

 - `(x NAND y) == NOT(x AND y) `
 - 妙处在哪里？
    - NAND(x,x) == NOT(x)  ! 
 - **Any Boolean function can be represented using an expression containin only NAND operation.**
    1. NOT(x) == (x NAND x)
        - 现在你已经有NOT了！
    2. (x AND y) = NOT( x NAND y )

### 1.3 Logic Gates

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_gate_interface.png)

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_gate_implementation.png)

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_circuit_impl.png)

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_circuit_impl2.png)


### 1.4 Hardware Description Language

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_xor.png)

- General idea:
    - out =1 , when :
        - a AND NOT(b)
        - OR
        - b AND NOT(a)

 - From gate diagram to HDL

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_gate_diag_2_hdl.png)

 1. HDL is a functional / declarative language
 2. THe order of HDL statements is insignificant
 3. Before using a chip part , you must know its interface. For example
    - Not(int=, out=)
    - And(a=,b=, out=)
    - Or(a=,b= , out=)


### 1.5 Hardware Simulation

.hdl --> HS  -->
      |     |
.tst -| ....|... -> .out


 - 配合 tst 文件，可以进行自动化测试 
 - tst 里可以 把结果输出， 还可以把 输出结果 和 事先准备的 .cmp 文件做比较

### 1.6 Multi-Bit Buses

 - Sometimes we manipulate "together" an array of bits
 - It is conecptually convenient to think about such a group of bits as a single entity 
    - sometime termed "bus"
 - HDLs will usually provide some convenient notation for handling these buses
 - Example: Addition of two 16-bit integers
    - ![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_16bit_adder.png)

```hdl
/*
 * Adds two 16-bit values
 */
CHIP Add16 {
    IN a[16] , b[16];
    OUT out[16];
    
    PARTS:
    ...    
}
```

```hdl
/*
 * Adds three 16-bit values
 */
CHIP Add3Way16 {
    IN first[16] , second[16], third[16] ;
    OUT out[16];
    
    PARTS:
        
        Add16(a=first, b=second, out=tmp) ;  
        Add16(a=temp, b=third, out=out ) ;  
}
```

```hdl
/*
 * Adds together all 4 bits of the input
 */
CHIP And4Way {
    IN a[4] ;
    OUT out;
    
    PARTS:
        
        ADD(a=a[0], b=a[1], out=t01) ;
        ADD(a=t01, b=a[2], out=t012) ;
        ADD(a=t012, b=a[3], out=out ) ;
}
```

```hdl
/*
 * Computes a bit-wise AND of its two 4-bit input bus
 */
CHIP And4W {
    IN a[4], b[4] ;
    OUT out[4];
    
    PARTS:
        
        ADD(a=a[0], b=b[0], out=out[0])
        ADD(a=a[1], b=b[1], out=out[1])
        ADD(a=a[2], b=b[2], out=out[2])
        ADD(a=a[3], b=b[3], out=out[3])
}
```

 - Sub-buses
    - Buses can be composed from ( and broken into ) sub-buses

```
/* compose bus example */
IN lsb[8], msb[8], ...
// 把这两个8bit bus 用到 And16上去
Add16( a[0..7]=lsb, a[8..15]=msb, b=... , out...  );

Add16( ... , out[0..3]=t1, out[4..15]=t2 ) ;
```

 - Some syntactic choices of our HDL 
    - Overlaps of sub-buses are allowed on output buses of parts
    - Width of internal pins is deduced automatically
    - `false` and `true` may be used as buses of any width
        - put lots of 1 , lots of 0 to a bus

### 1.7 Project 1 Overview 

 - Given: Nand
 - Goal: Build the following gates:
    - Not
    - And
    - Or
    - Xor 
    - Mux
    - Dmux # 4 elememtary logic gates
    - Not16
    - And16
    - Or16
    - Mux16  # 4 16-bit variants
    - Or8Way
    - Mux4Way16
    - Mux8Way16
    - DMux4Way
    - DMux8Way # 5 Multi-way variants
 - Why these 15 particular gates ?
 - Because ...
    - The are commonly used gates
    - They comprise all the elememtary logic gates needed to build our computer
 - Multiplexor (Mux)
    - ![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_multiplexor.png)
    - 3 input, 1 output

```c
if (sel==0)
    out=a
else
    out=b
```

 - A 2-way multiplexor enables *selecting* , and outputing, one out of 2 possible inputs
 - Widely used in : 
    - Digital design
    - Communications networks
 - Example: using mux logic to build a programmable gate
    - ![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_programmable_gate_use_mux.png)
 - Demultiplexor
    - ![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_demultiplexor.png)
    - acts like the inverse of a multiplexor
    - Distributes the single input value into one of 2 possible destinations
        - based on the selection bit, it either channels the input to an a output , or to a b output
 - Example: Multiplexing / demultiplexing in communications networks
    - ![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_mux_dmux_example.png)
    - we may have several channels coming in. Let's say channels of music or movies
        - that we want to send over a single communications line
    - and through this single line, I want to send multiple messages. How can I possiblely do it ?
    - I can put a Mux in the sending end , and I can feed the Mux an ongoing train of 0101010...
        - This can be done using what is known as an oscillator 振荡器
        - Each `sel` bit is connected to an oscillator that produces a repetitive train of alternating 0 and 1 signals.
    - In every cycle the Mux will output one bit from one of the 2 inputs
    - At the receiving end , I put a different oscillator and therefore the dmux is going to distribute the incoming inputs according to the dmux logic. 
    - So this logic here , the Dmux and the Mux logic taken together enable me to interleave several messages over a single communications line. 
        - which may be very expensive
 - 16-bit, 4-way multiplexor
    - ![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_16bit4waymultiplexor.png)

---

# Week2 

## ALU (Arithmetic Logic Unit)

 - Addtion / Subtraction are easy
 - Multiplication and division are complicated, but nicely enough, we can actually postpone them to software.

### 2.2 Binary Addition

 - Building an Adder
    1. Half Adder -- adds two bits
    2. Full Adder -- adds 3 bits
    3. Adder -- Adds two numbers

#### Half Adder

a | b | sum | carry
--- | --- | --- | ---
0 | 0 | 0 | 0
0 | 1 | 1 | 0
1 | 0 | 1 | 0
1 | 1 | 0 | 1

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_half_adder.png)

 - Xor -> sum , And -> carry ?

#### Full Adder

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_fullAdder.png)

 - use 2 half adder ,  plus ... 



#### Multi-bit Adder 

 1. half adder
 2. full adder , repeart

### 2.3 Negative Numbers 

 - Q: 为什么计算机不直接 把 最高为 解释成符号位，其他不变？
    - 以8bit为例，这样会造成 10000000 = -0 的情况，非常蠢。
 - 2ⁿ-x => -x
    - 10000000 => -128
 - Positive Numbers : 0 ... 2ⁿ⁻¹-1
 - Negative Numbers : -1 ... =-2ⁿ⁻¹
 - Input x , Output -x, how?
    - -x = !x +1
 - Tips for `+1`
    - 从低位开始， 逢1变0，直到把第一个碰到的0变成1，退出

### 2.4 Arithmetic Logic Unit

 - Arithmetic operations:
    - integer addition, multiplication, division, ...
 - Logical operations:
    - And, Or, Xor, ...

#### The Hack ALU 

 - ALU used in this course ...

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_hack_alu.png)

 - take two 16-bit input, generate one 16-bit output
 - Which function to compute is set by 6 1-bit inputs
    - zx , nx , zy, ny , f , no
    - Based on these 6 control bits, the ALU computes one out of the following ***18*** functions.
        - the ALU can compute many more functions, but we will focus these 18 only

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_alu_18funcs.png)

 - Also outputs two 1-bit valuse
    - zr, ng


#### The 6 Control bits

![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/n2t_alu_18funcs_truth_table.png)

 - if zx , and nx , then x = -1 (1111)
 - Example: Compute !x , x: 1100, y: 1011
    - zx=nx=0 => x = x
    - zy=ny=1 => y = 1111
    - f=0 => x&y = 1100
    - no=1 => !(1100) = 0011 , it is !x
 - 理论上6个控制位可以产生 2⁶ 个 结果，但实际上有些组合的output是相同的
    - 比如 zx=1,nx=0 , zy=1,ny=1, f=1,no=1 , 同样也输出 0

#### The 2 output control bits

 - if out == 0 , then zr = 1 , else zr = 0.
 - if out < 0 . then ng =1 , else ng =0.
 - These two control bits will come into play when we build the complete computer's architecture.

---

 - ALU 所有所需要的运算，我们之前做的 chips 都能实现
 - The Hack ALU is both simple but quite sophisticated.

### 2.5 Project2 

 - Goal: 
    - HalfAdder
    - FullAdder
    - Add16
        - full adder * 16 实现方式可以优化， it called *carry look ahead*
    - Inc16
    - ALU  , less than 20 lines of HDL code
 






    






       






