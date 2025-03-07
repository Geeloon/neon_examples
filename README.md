# NEON INTRINSIC EXAMPLES

<br>

<dl>
<p align="right"> 
	<font color=gray size=6> jilong1993@gmail.com </font>
</p>
<dl>

<br>
<br>

* **本文只展示了部分指令 (*每种操作选取了1种/2种类型展示运行逻辑*)**
* **如果像了解全部指令(随着时间推移, 指令集也是有扩充的)，请移步下面的官方网站或其他网站**
* **解释中加入了一些个人理解，不代表编译器行为**
* **测试的时间在2021年, 不能保证本文展示的内容是一直正确的. (2024年来看没有发现什么变化)**

----
* **参考网站**：[https://developer.arm.com/documentation/dui0472/m/Using-NEON-Support](https://developer.arm.com/documentation/dui0472/m/Using-NEON-Support) <br>
* **向量给出的顺序为：第一输入向量，第二输入向量，...，输出向量**
* **表格在第一页的页末和第二页的页首的时候会出现分割，表格的表格会在第二页重复一次**
* **Neon intrinsic的一般规律**：v+(q/r/d)xxx+(q/l/n/w)+_type
> **紧跟v的q代表saturate饱和操作，r代表rounding四舍五入,d代表doubling翻倍。**
> **xxx代表某类型的命令：如add，sub，mul等。**
> **命令后紧跟的字母一般跟向量的长度有关系：**
> >后面的q代表操作对象是128bits的向量；
> >l代表long移动到长向量（输入为64bits，输出为128bits）；
> >相反地，n代表narrow移动到短向量（输入为128bits，输出为64bits）；
> >w代表widen拓宽操作（一般有两个输入，分别为64bits和128bits，输出则为128bits）。

<div STYLE="page-break-after: always;"></div>

# Table
- [NEON INTRINSIC EXAMPLES](#neon-intrinsic-examples)
- [Table](#table)
	- [1. Add](#1-add)
		- [1.1 vector add: vadd](#11-vector-add-vadd)
		- [1.2 vector add long: vaddl](#12-vector-add-long-vaddl)
		- [1.3 vector wide add: vaddw](#13-vector-wide-add-vaddw)
		- [1.4 vector halving add: vhadd](#14-vector-halving-add-vhadd)
		- [1.5 vector rounding halving add: vrhadd](#15-vector-rounding-halving-add-vrhadd)
		- [1.6 vector saturating add: vqadd](#16-vector-saturating-add-vqadd)
		- [1.7 vector add high half: vaddhn](#17-vector-add-high-half-vaddhn)
		- [1.8 vector rounding add high half: vaddhn](#18-vector-rounding-add-high-half-vaddhn)
	- [2 Substract](#2-substract)
		- [2.1 vector substract: vsub](#21-vector-substract-vsub)
		- [2.2 vector long substract: vsubl](#22-vector-long-substract-vsubl)
		- [2.3 vector wide substract: vsubw](#23-vector-wide-substract-vsubw)
		- [2.4 vector saturating sub:](#24-vector-saturating-sub)
		- [2.5 vector halving sub:](#25-vector-halving-sub)
		- [2.6 vector sub high half:](#26-vector-sub-high-half)
		- [2.7 vector rounding sub high half:](#27-vector-rounding-sub-high-half)
	- [3 Multiplication:](#3-multiplication)
		- [3.1 vector multiply](#31-vector-multiply)
		- [3.2 vector multiply accumulate:](#32-vector-multiply-accumulate)
		- [3.3 vector multiply accumulate long:](#33-vector-multiply-accumulate-long)
		- [3.4 vector multiply substract long:](#34-vector-multiply-substract-long)
		- [3.5 vector saturating double multiply high:](#35-vector-saturating-double-multiply-high)
		- [3.6 vector saturating rounding doubling multiply high:](#36-vector-saturating-rounding-doubling-multiply-high)
		- [3.7 vector long multiply:](#37-vector-long-multiply)
		- [3.8 vector saturating doubling multiply subtract long:](#38-vector-saturating-doubling-multiply-subtract-long)
		- [3.9 vector saturating doubling long multiply:](#39-vector-saturating-doubling-long-multiply)
	- [4 Compare](#4-compare)
		- [4.1 vector compare equal:](#41-vector-compare-equal)
		- [4.2 vector compare greater-than or equal:](#42-vector-compare-greater-than-or-equal)
		- [4.3 vector compare less-than or equal:](#43-vector-compare-less-than-or-equal)
		- [4.4 vector compare greater-than:](#44-vector-compare-greater-than)
		- [4.5 vector compare less-than:](#45-vector-compare-less-than)
		- [4.6 vector absolute compare:](#46-vector-absolute-compare)
		- [4.7 vector test bits:](#47-vector-test-bits)
	- [5 Absolute difference](#5-absolute-difference)
		- [5.1 absolute diff:](#51-absolute-diff)
		- [5.2 absolute diff long:](#52-absolute-diff-long)
		- [5.3 absolute diff and accumulate:](#53-absolute-diff-and-accumulate)
		- [5.4 absolute diff and accumulate-long:](#54-absolute-diff-and-accumulate-long)
	- [6 Max and Min](#6-max-and-min)
		- [6.1 max:](#61-max)
		- [6.2 min:](#62-min)
	- [7 Pairwise](#7-pairwise)
		- [7.1 pairwise add:](#71-pairwise-add)
		- [7.2 long pairwise add:](#72-long-pairwise-add)
		- [7.3 long pairwise add and accumulate:](#73-long-pairwise-add-and-accumulate)
		- [7.4 pairwise maximum:](#74-pairwise-maximum)
		- [7.5 pairwise minimum:](#75-pairwise-minimum)
	- [8 Shift](#8-shift)
		- [8.1 shift left by vector:](#81-shift-left-by-vector)
		- [8.2 saturating shift left by vector:](#82-saturating-shift-left-by-vector)
		- [8.3 rounding shift left by vector:](#83-rounding-shift-left-by-vector)
		- [8.4 saturating rounding shift left by vector:](#84-saturating-rounding-shift-left-by-vector)
		- [8.5 shift right by constant:](#85-shift-right-by-constant)
		- [8.6 shift left by constant:](#86-shift-left-by-constant)
		- [8.7 rounding shift right by constant:](#87-rounding-shift-right-by-constant)
		- [8.8 saturating shift left by constant:](#88-saturating-shift-left-by-constant)
		- [8.9 signed-to-unsigned saturating shift left by constant:](#89-signed-to-unsigned-saturating-shift-left-by-constant)
		- [8.10 narrow shift right by constant:](#810-narrow-shift-right-by-constant)
		- [8.11 signed-to-unsigned narrowing saturating shift right by constant:](#811-signed-to-unsigned-narrowing-saturating-shift-right-by-constant)
		- [8.12 signed-to-unsigned rounding narrowing saturating shift right by constant:](#812-signed-to-unsigned-rounding-narrowing-saturating-shift-right-by-constant)
		- [8.13 narrowing saturating shift right by constant:](#813-narrowing-saturating-shift-right-by-constant)
		- [8.14 rounding narrowing shift right by constant:](#814-rounding-narrowing-shift-right-by-constant)
		- [8.15 rounding narrowing saturating shift right by constant:](#815-rounding-narrowing-saturating-shift-right-by-constant)
		- [8.16 widening shift left by constant:](#816-widening-shift-left-by-constant)
	- [9 Combine and Split](#9-combine-and-split)
		- [9.1 combine:](#91-combine)
		- [9.2 split:](#92-split)
		- [9.3 transpose:](#93-transpose)
		- [9.4 interleave:](#94-interleave)
		- [9.5 De-Interleave:](#95-de-interleave)
		- [9.6 shift right and insert:](#96-shift-right-and-insert)
		- [9.7 shift left and insert:](#97-shift-left-and-insert)
		- [9.8 extraction](#98-extraction)
	- [10 Logical](#10-logical)
		- [10.1 Bitwise NOT:](#101-bitwise-not)
		- [10.2 Bitwise AND:](#102-bitwise-and)
		- [10.3 Bitwise OR:](#103-bitwise-or)
		- [10.4 Bitwise exclusive or (EOR or XOR):](#104-bitwise-exclusive-or-eor-or-xor)
		- [10.5 Bit Clear:](#105-bit-clear)
		- [10.6 Bitwise OR complement:](#106-bitwise-or-complement)
		- [10.7 Bitwise Select:](#107-bitwise-select)
		- [10.8 Reversing vector elements:](#108-reversing-vector-elements)
	- [11 Sign Related](#11-sign-related)
		- [11.1 absolute:](#111-absolute)
		- [11.2 saturating absolute:](#112-saturating-absolute)
		- [11.3 negate:](#113-negate)
		- [11.4 saturating negate:](#114-saturating-negate)
		- [11.5 count leading sign bits:](#115-count-leading-sign-bits)
		- [11.6 count leading zeros:](#116-count-leading-zeros)
	- [12 Look-Up Table](#12-look-up-table)
		- [12.1 table look up:](#121-table-look-up)
		- [12.2 extended table look up:](#122-extended-table-look-up)
	- [13 Load](#13-load)
		- [13.1 Load a single vector from memory:](#131-load-a-single-vector-from-memory)
		- [13.2 Load a lane vector from memory:](#132-load-a-lane-vector-from-memory)
		- [13.3 Load all lanes of vector with same value from memory:](#133-load-all-lanes-of-vector-with-same-value-from-memory)
		- [13.4 Load a single lane of a vector from a literal:](#134-load-a-single-lane-of-a-vector-from-a-literal)
	- [14 Store](#14-store)
		- [14.1 Store a single vector into memory:](#141-store-a-single-vector-into-memory)
		- [14.2 Store a lane into memory:](#142-store-a-lane-into-memory)
	- [15 Init](#15-init)
		- [15.1 Load all lanes of vector to the same literal value:](#151-load-all-lanes-of-vector-to-the-same-literal-value)
		- [15.2 Load all lanes of vector to the value of a lane of a vector:](#152-load-all-lanes-of-vector-to-the-value-of-a-lane-of-a-vector)
		- [15.3 Initializing a vector from a literal bit pattern:](#153-initializing-a-vector-from-a-literal-bit-pattern)
		- [15.4 Extracing lanes from a vector into a register:](#154-extracing-lanes-from-a-vector-into-a-register)
	- [16 Convert](#16-convert)
		- [16.1 convert from float:](#161-convert-from-float)
		- [16.2 convert to float:](#162-convert-to-float)
		- [16.3 convert btw floats:](#163-convert-btw-floats)
		- [16.4 vector narrow integer:](#164-vector-narrow-integer)
		- [16.5 vector long move:](#165-vector-long-move)
		- [16.6 vector saturating narrow integer:](#166-vector-saturating-narrow-integer)
		- [16.7 vector saturating narrow integer signed-\>unsigned:](#167-vector-saturating-narrow-integer-signed-unsigned)
		- [16.8 vector cast operation](#168-vector-cast-operation)
	- [17 With Scalar](#17-with-scalar)
		- [17.1 vector multiply accumulate with scalar:](#171-vector-multiply-accumulate-with-scalar)
		- [17.2 vector widening multiply accumulate with scalar:](#172-vector-widening-multiply-accumulate-with-scalar)
		- [17.3 vector widening saturating doubling multiply accumulate with scalar:](#173-vector-widening-saturating-doubling-multiply-accumulate-with-scalar)
		- [17.4 vector multiply subtract with scalar:](#174-vector-multiply-subtract-with-scalar)
		- [17.5 vector widening multiply subtract with scalar:](#175-vector-widening-multiply-subtract-with-scalar)
		- [17.6 vector widening saturating doubling multiply subtract with scalar:](#176-vector-widening-saturating-doubling-multiply-subtract-with-scalar)
		- [17.7 vector multiply by scalar:](#177-vector-multiply-by-scalar)
		- [17.8 vector long multiply with scalar:](#178-vector-long-multiply-with-scalar)
		- [17.9 vector long multiply by scalar:](#179-vector-long-multiply-by-scalar)
		- [17.10 vector saturating doubling long multiply with scalar:](#1710-vector-saturating-doubling-long-multiply-with-scalar)
		- [17.11 vector saturating doubling long multiply by scalar:](#1711-vector-saturating-doubling-long-multiply-by-scalar)
		- [17.12 vector saturating doubling multiply high with scalar:](#1712-vector-saturating-doubling-multiply-high-with-scalar)
		- [17.13 vector saturating doubling multiply high by scalar:](#1713-vector-saturating-doubling-multiply-high-by-scalar)
		- [17.14 vector saturating rounding doubling multiply high with scalar:](#1714-vector-saturating-rounding-doubling-multiply-high-with-scalar)
		- [17.15 vector rounding saturating doubling multiply high by scalar:](#1715-vector-rounding-saturating-doubling-multiply-high-by-scalar)
		- [17.16 vector multiply accumulate with scalar:](#1716-vector-multiply-accumulate-with-scalar)
		- [17.17 vector widening multiply accumulate with scalar:](#1717-vector-widening-multiply-accumulate-with-scalar)
		- [17.18 vector widening saturating doubling multiply accumulate with scalar:](#1718-vector-widening-saturating-doubling-multiply-accumulate-with-scalar)
		- [17.19 vector multiply subtract with scalar:](#1719-vector-multiply-subtract-with-scalar)
		- [17.20 vector widening multiply subtract with scalar:](#1720-vector-widening-multiply-subtract-with-scalar)
		- [17.21 vector widening saturating doubling multiply subtract with scalar:](#1721-vector-widening-saturating-doubling-multiply-subtract-with-scalar)
	- [18 Reciprocal Estimate](#18-reciprocal-estimate)
		- [18.1 Reciprocal estimate:](#181-reciprocal-estimate)
		- [18.2 Reciprocal square root estimate:](#182-reciprocal-square-root-estimate)
		- [18.3 Two steps in an interation of the Newton-Raphson method :](#183-two-steps-in-an-interation-of-the-newton-raphson-method-)
		- [TO ADD:](#to-add)
	- [19 Complex (since Armv8.3-a)](#19-complex-since-armv83-a)



## 1. Add

----

### 1.1 vector add: vadd 
<font color=blue>
int16x8_t vaddq_s16(int16x8_t, int16x8_t)
</font>

> 有符号16位整数向量加法：8个16bits的整数对应相加。如果结果超过最大范围，数值会溢出。

| v_s16 | 32767 | 32767 | -32768 | -32768  | 1    | 1   | 1    |  0   |
| :---: | ---:  | ---:  | ---:  | ---:    | ---: | ---: | ---: | ---: |
| v_s16 |    1 |  -1  |    1  |     -1 |   1  | -1  |  0   |  1   |
| v_s16 |**-32768** | 32766 | -32767 | **32767**   |   2  |  0  |   1  |  1   |

> 没有溢出的加法： 1 + 1 = 2 ： 0x0001 + 0x0001 = 0x0002
> 存在溢出的加法：32767 + 1 -> -32768 ： 0x7fff + 0x0001 = 0x8000
>           -32768 + (-1) -> 32767 : 0x8000 + 0xffff = 0x7fff
**> 使用此类命令的时候一定要提醒自己是否要将`溢出`考虑进去。在下面的命令中，如果有溢出的情况将不再一一赘述，而是直接用`黑体`表示。**

<font color=blue>
uint16x8_t vaddq_u16(uint16x8_t, uint16x8_t)
</font>

> 无符号16位整数加法。

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |    
| v_u16 | 1 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| v_u16 | **0** | **65534** | 0 | 2 | 4 | 6 | 8 | 10 |
<br>

### 1.2 vector add long: vaddl 

> 64bits的向量相加，得到128bits的向量

<font color=blue>
int16x8_t vaddl_s8(int8x8_t, int8x8_t)
</font>

| v_s8  | 127  | 127| -128| -128|  127|  1|  1|  0 |
| :---: | ---: | ---:  | ---:  | ---:  | ---: | ---: | ---: | ---: |
| v_s8  |   1| 127|  -1| -128| -128|  1|  0|  1 |
| v_s16 |  128| 254| -129| -256|  -1|  2|  1|  1|

<font color=blue>
uint16x8 vaddl_u8(uint8x8, uint8x8)
</font>
 
| v_u8 | 255 | 255 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---:| ---:  | ---:  | ---:  | ---:  | ---: | ---: | ---: | ---: |
| v_u8  | 1 | 255 | 0 | 1 | 2 | 3 | 4 | 5 |
| v_u16 | 256 | 510 | 0 | 2 | 4 | 6 | 8 | 10 |
<br>

### 1.3 vector wide add: vaddw 

> 64bits的向量和128bits的向量相加，得到一个128bits。

<font color=blue>
int16x8_t vaddw_s8(int16x8_t, int8x8_t)
</font>

| v_s16  | 32767| 32767| -32768| -32768| 1| 127| -1| -128|
| :---:  | ---:  | ---:  | ---:  | ---:  | ---: | ---: | ---: | ---: |
| v_s8   | 1| -1| 1| -1| 127| 127| -128| -128|
| v_s16  | **-32768**| 32766| -32767| **32767**| 128| 254| -129| -256|
 
<font color=blue>
uint16x8_t vaddw_u8(uint16x8_t, uint8x8_t)
</font>

| v_u16 | 65535 | 65535 | 1 | 255 | 0 | 1 | 2 | 3 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8 | 1 | 255 | 255 | 255 | 0 | 1 | 2 | 3 |
| v_u16 | **0** | **254** | 256 | 510 | 0 | 2 | 4 | 6 |
<br>

### 1.4 vector halving add: vhadd

> 两个向量相加，然后右移一位（除以2），不存在溢出情况。

<font color=blue>
int16x8_t vhaddq_s16(int16x8_t, int16x8_t)
</font>

| v_s16    | 32767| 32767| -32768| -32768|     1|    1|    -1|   -1|
| :---:    | ---:    | ---:    | ---:    | ---:     | ---: | ---: | ---: | ---: |
| v_s16    |       1| 32767|       -1|  -32768|    1|    2|    -1|    -2|
| v_s16    | 16384| 32767| -16385| -32768|     1|    1|    -1|   -2|

<font color=blue>
uint16x8_t vhaddq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |   
| v_u16 | 1 | 65535 | 0 | 0 | 0 | 0 | 0 | 0 |
| v_u16 | 32768 | 65535 | 0 | 0 | 1 | 1 | 2 | 2 |
<br>

### 1.5 vector rounding halving add: vrhadd

> 两个向量相加，然后右移一位（除以2），如果最低位为1，结果+1，不存在溢出情况。

<font color=blue>
int16x8_t vrhaddq_s16(int16x8_t, int16x8_t) 
</font>

| v_s16  | 32767| 32767| -32768| -32768| 1| 1| -1| -1|
| :---:  | ---:    | ---:    | ---:    | ---:     | ---: | ---: | ---: | ---: |
| v_s16  | 1| 32767| -1| -32768| 1| 2| -1| -2|
| v_s16  | 16384| 32767| -16384| -32768| 1| 2| -1| -1|

<font color=blue>
uint16x8_t vrhaddq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |   
| v_u16 | 1 | 65535 | 0 | 0 | 0 | 0 | 0 | 0 |
| v_u16 | 32768 | 65535 | 0 | 1 | 1 | 2 | 2 | 3 |
<br>

### 1.6 vector saturating add: vqadd

> 饱和加法：如果计算结果超过最大值，记为最大值；如果计算结果低于最小值，为最小值。

<font color=blue>
int16x8_t vqaddq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | 32767 | -32768 | -32768 | 1 | 1 | -1 | -1 |       
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |   
| v_s16 | 1 | 32767 | -1 | -32768 | 1 | 2 | -1 | -2 |
| v_s16 | 32767 | 32767 | -32768 | -32768 | 2 | 3 | -2 | -3 |       

<font color=blue>
uint16x8_t vqaddq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |   
| v_u16 | 1 | 65535 | 0 | 0 | 0 | 0 | 0 | 0 |
| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
<br>

### 1.7 vector add high half: vaddhn

> 相加，然后取高位。

<font color=blue>
int8x8_t vaddhn_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | 32767 | -32768 | -32768 | 256 | 256 | -256 | -256 |  
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_s16 | 1 | 32767 | -1 | -32768 | 127 | 128 | -128 | -129 |
| v_s16 | **-128** | **-1** | **127** | **0** | 1 | 1 | -2 | -2 |

<font color=blue>
uint8x8_t vaddhn_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 128 | 129 | 127 | 128 | 1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_u16 | 1 | 65535 | 65535 | 65535 | 4096 | 4096 | 255 | 255 |        
| v_u16 | **0** | **255** | **0** | **0** | 16 | 16 | 1 | 0 |
<br>

### 1.8 vector rounding add high half: vaddhn

> 相加，取高位，如果低位的最高位为1，向最高位进1.

<font color=blue>
int8x8_t vraddhn_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | 32767 | -32768 | -32768 | 256 | 256 | -256 | -256 |  
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_s16 | 1 | 32767 | -1 | -32768 | 127 | 128 | -128 | -129 |
| v_s8  | -128 | 0 | -128 | 0 | 1 | 2 | -1 | -2 |

> -32768 + (-1) -> -128 : 0x8000 + 0xffff = 0x7fff, 高8位为0x7f, 低8位为0xff.所以最后结果为 0x7f + 0x01 = 0x80

<font color=blue>
uint8x8_t vraddhn_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 128 | 129 | 127 | 128 | 1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_u16 | 1 | 65535 | 65535 | 65535 | 4096 | 4096 | 255 | 255 |        
| v_u8  | 0 | 0 | 0 | 1 | 16 | 17 | 1 | 1 |

<div STYLE="page-break-after: always;"></div>

## 2 Substract
----
<br>

### 2.1 vector substract: vsub
<font color=blue>
int16x8_t vsubq_s16(int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | -32768 | -32768 | 32767 | -2 | 0 | 1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |    
| v_s16 | -1 | 1 | 32767 | -32768 | 32767 | -32768 | 0 | 1 |
| v_s16 | **-32768** | **32767** | **1** | **-1** | **32767** | **-32768** | 1 | -1 |   

<font color=blue>
uint16x8_t vsubq_u16(uint16x8_t, uint16x8_t):
</font>

| v_u16 | 65535 | 0 | 1 | 1 | 0 | 2 | 0 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |    
| v_u16 | 1 | 65535 | 65535 | 0 | 1 | 0 | 2 | 0 |
| v_u16 | 65534 | **1** | **2** | 1 | **65535** | 2 | **65534** | 0 |
<br>

### 2.2 vector long substract: vsubl

<font color=blue>
int16x8_t vsubl_s8(int8x8_t, int8x8_t):
</font>

| v_s8  | 127 | -128 | -1 | -128 | 1 | 0 | 2 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8  | -128 | 127 | 127 | 1 | 0 | 1 | 0 | 2 |
| v_s16 | 255 | -255 | -128 | -129 | 1 | -1 | 2 | -2 |

<font color=blue>
uint16x8_t vsubl_u8(uint8x8_t, uint8x8_t):
</font>

| v_u8  | 255 | 1 | 1 | 0 | 2 | 0 | 3 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 1 | 255 | 0 | 1 | 0 | 2 | 0 | 3 |
| v_u16 | 254 | **65282** | 1 | **65535** | 2 | **65534** | 3 | **65533** |
<br>

### 2.3 vector wide substract: vsubw

<font color=blue>
int16x8_t vsubw_s8(int16x8_t, int8x8_t):
</font>

| v_s16 | 32767 | -32768 | -2 | 1 | 1 | 0 | 2 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8  | -1 | 1 | 127 | -128 | 0 | 1 | 0 | 2 |
| v_s16 | **-32768** | **32767** | -129 | 129 | 1 | -1 | 2 | -2 |

<font color=blue>
uint16x8_t vsubw_u8(uint16x8_t, uint8x8_t):
</font>

| v_u16 | 65535 | 1 | 1 | 0 | 2 | 0 | 3 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 1 | 255 | 0 | 1 | 0 | 2 | 0 | 3 |
| v_u16 | 65534 | **65282** | 1 | **65535** | 2 | **65534** | 3 | **65533** |
<br>

### 2.4 vector saturating sub:

<font color=blue>
int16x8_t vqsubq_s16(int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | -32768 | -1 | -2 | 0 | 1 | 1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -1 | 1 | 32767 | 32767 | -32768 | -32768 | 0 | 1 |
| v_s16 | 32767 | -32768 | -32768 | -32768 | 32767 | 32767 | 1 | -1 | 

<font color=blue>
uint16x8_t vqsubq_u16(uint16x8_t, uint16x8_t):
</font>

| v_u16 | 65535 | 0 | 1 | 1 | 0 | 2 | 0 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 1 | 65535 | 65535 | 0 | 1 | 0 | 2 | 0 |
| v_u16 | 65534 | 0 | 0 | 1 | 0 | 2 | 0 | 0 |
<br>

### 2.5 vector halving sub:
> 相减，除以2，没有检测出溢出.

<font color=blue>
int16x8_t vhsubq_s16(int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | -32768 | -2 | -32767 | 1 | 2 | -1 | -2 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_s16 | -32768 | 32767 | 32767 | 2 | 0 | 0 | 0 | 0 |
| v_s16 | 32767 | -32768 | -16385 | -16385 | 0 | 1 | -1 | -1 |

<font color=blue>
uint16x8_t vhsubq_u16(uint16x8_t, uint16x8_t):
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 1 | 0 | 2 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_u16 | 0 | 1 | 65535 | 65535 | 0 | 1 | 0 | 2 |
| v_u16 | 32767 | 32767 | 32768 | 32769 | 0 | 65535 | 1 | 65535 | 
<br>

### 2.6 vector sub high half:
> 相减，然后取高位。

<font color=blue>
int8x8_t vsubhn_s16(int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | -32768 | 32767 | -32768 | 32767 | -32768 | -256 | -257 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_s16 | 0 | 0 | -32768 | 32767 | -1 | 1 | 0 | 0 |
| v_s8  | 127 | -128 | **-1** | **0** | **-128** | **127** | -1 | -2 |

<font color=blue>
uint8x8_t vsubhn_u16(uint16x8_t, uint16x8_t):
</font>

| v_u16 | 65535 | 0 | 16384 | 16384 | 1024 | 1024 | 256 | 256 |        
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_u16 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 1 |
| v_u8  | 255 | **255** | 64 | 63 | 4 | 3 | 1 | 0 |
<br>

### 2.7 vector rounding sub high half:
> 相减，取高位，如果低位的最高位为1，向高位进1.

<font color=blue>
int8x8_t vrsubhn_s16(int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | -32768 | 32767 | -32768 | 32767 | -32768 | -256 | -256 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_s16 | 0 | 0 | -32768 | 32767 | -1 | 1 | -127 | -128 |
| v_s8  | -128 | -128 | **0** | **0** | **-128** | **-128** | -1 | 0 |

> 我们以 （-32768 - 1） -> -128为例进行解释：
> -32768（0x8000），减1后变成 0x7fff。由于低位为0xff，所以向高位进1。所以最后的结果变成 0x7f + 1 = 0x80 (-128)。

<font color=blue>
uint8x8_t vrsubhn_u16(uint16x8_t, uint16x8_t):
</font>

| v_u16 | 65535 | 0 | 16384 | 16384 | 16384 | 256 | 256 | 256 |        
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |      
| v_u16 | 0 | 1 | 0 | 128 | 129 | 0 | 128 | 129 |
| v_u8 | **0** | **0** | 64 | 64 | 63 | 1 | 1 | 0 |

<div STYLE="page-break-after: always;"></div>

## 3 Multiplication:
----
<br>

### 3.1 vector multiply

> 向量乘法

<font color=blue>
int16x8_t vmulq_s16(int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | 32767 | -32768 | -32768 | 256 | 256 | -256 | -256 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 2 | -2 | 2 | -2 | 127 | 128 | 128 | 129 |
| v_s16 | **-2** | **2** | **0** | **0** | 32512 | **-32768** | **-32768** | **32512** |

<font color=blue>
uint16x8_t vmulq_u16(uint16x8_t, uint16x8_t):
</font>

| v_u16 | 65535 | 65535 | 65535 | 65535 | 256 | 257 | 1 | 2 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 1 | 2 | 3 | 4 | 256 | 255 | 1 | 2 |
| v_u16 | 65535 | **65534** | **65533** | **65532** | **0** | 65535 | 1 | 4 |
<br>

### 3.2 vector multiply accumulate:

> 乘加： 向量1 + 向量2 * 向量3

<font color=blue>
int16x8_t vmlaq_s16(int16x8_t, int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | -32768 | 1 | 1 | 0 | 1 | 2 | 3 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 1 | 32767 | -32768 | 0 | 1 | 2 | 3 |
| v_s16 | 1 | -1 | 2 | 2 | 0 | 1 | 2 | 3 |
| v_s16 | **-32768** | **32767** | **-1** | **1** | 0 | 2 | 6 | 12 |

<font color=blue>
uint16x8_t vmlaq_u16(uint16x8_t, uint16x8_t, uint16x8_t):
</font>

| v_u16 | 65535 | 1 | 2 | 0 | 1 | 2 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 1 | 65535 | 256 | 0 | 1 | 2 | 3 | 4 |
| v_u16 | 1 | 2 | 256 | 0 | 1 | 2 | 3 | 4 |
| v_u16 | **0** | **65535** | **2** | 0 | 2 | 6 | 12 | 20 |
<br>

### 3.3 vector multiply accumulate long:

<font color=blue>
int16x8_t vmlal_s8(int16x8_t, int8x8_t, int8x8_t):
</font>

| v_s16 | 32767 | -32768 | 1 | 1 | 0 | 0 | 0 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 1 | 1 | 127 | -128 | 127 | -128 | 1 | 2 |
| v_s8 | 1 | -1 | 2 | 2 | 127 | -128 | 1 | 3 |
| v_s16 | **-32768** | **32767** | 255 | -255 | 16129 | 16384 | 1 | 7 |

<font color=blue>
uint16x8_t vmlal_u16(uint16x8_t, uint8x8_t, uint8x8_t):
</font>

| v_u16 | 65535 | 1 | 2 | 0 | 1 | 2 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 1 | 255 | 255 | 0 | 1 | 2 | 3 | 4 |
| v_s8 | 1 | 2 | 255 | 0 | 1 | 2 | 3 | 4 |
| v_u16 | **0** | 511 | 65027 | 0 | 2 | 6 | 12 | 20 |
<br>

### 3.4 vector multiply substract long:

<font color=blue>
int16x8_t vmlsl_s8(int16x8_t, int8x8_t, int8x8_t):
</font>

| v_s16 | 32767 | -32768 | 1 | 1 | 0 | 0 | 0 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 1 | 1 | 127 | -128 | 127 | -128 | 1 | 2 |
| v_s8 | -1 | 1 | 2 | 2 | 127 | -128 | 1 | 3 |
| v_s16 | **-32768** | **32767** | -253 | 257 | -16129 | -16384 | -1 | -5 |

<font color=blue>
uint16x8_t vmlsl_u8(uint16x8_t, uint8x8_t, uint8x8_t):
</font>

| v_u16 | 65535 | 509 | 65535 | 0 | 1 | 20 | 30 | 40 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 1 | 255 | 255 | 0 | 1 | 2 | 3 | 4 |
| v_s8 | 1 | 2 | 255 | 0 | 2 | 2 | 3 | 4 |
| v_u16 | 65534 | **65535** | 510 | 0 | **65535** | 16 | 21 | 24 |
<br>

### 3.5 vector saturating double multiply high:

> 相乘，假设结果存在32bits中，乘2，然后取高位的16bits作为结果。

<font color=blue>
int16x8_t vqdmulhq_s16(int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | -32768 | 32767 | -32768 | 256 | 256 | 255 | -256 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 1 | 2 | -32768 | 256 | 128 | 128 | -256 |
| v_s16 | 0 | -1 | 1 | 32767 | 2 | 1 | 0 | 2 |

>multiply: ( 32767 * 1) -> 0x00007fff, doubling: 0x00008ffe, get high: 0x0000  (0)
>multiply: (-32768 * 1) -> 0xffff8000, doubling: 0xffff0000, get high: 0xffff (-1)

<br>

### 3.6 vector saturating rounding doubling multiply high:

<font color=blue>
int16x8_t vqrdmulhq_s16(int16x8_t, int16x8_t):
</font>

| v_s16 | 32767 | -32768 | 32767 | -32768 | 256 | 256 | 255 | -256 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 1 | 2 | -32768 | 256 | 128 | 128 | -256 |
| v_s16 | 1 | -1 | 2 | 32767 | 2 | 1 | 1 | 2 |

> e.g. -32768 and -32768 -> 32767
> 0x8000 * 0x8000 = 0x40000000(positive), doubling -> 0x80000000(这个数值作为正数溢出了，饱和成0x7fffffff),然后取高位变成0x7fff

### 3.7 vector long multiply:

<font color=blue>
int16x8_t vmull_s16(int8x8_t, int8x8_t):
</font>

| v_s8 | 127 | 127 | 127 | -128 | -128 | -128 | -17 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 127 | 2 | -2 | -128 | 2 | -2 | -17 | 1 |
| v_s16 | 16129 | 254 | -254 | 16384 | -256 | 256 | 289 | 1 |

<font color=blue>
uint16x8_t vmull_u8(uint8x8_t, uint8x8_t)
</font>

| v_s8 | 255 | 255 | 255 | 255 | 2 | 8 | 32 | 128 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 255 | 2 | 16 | 64 | 2 | 8 | 32 | 128 |
| v_u16 | 65025 | 510 | 4080 | 16320 | 4 | 64 | 1024 | 16384 |
<br>

### 3.8 vector saturating doubling multiply subtract long:

<font color=blue>
int32x4_t vqdmlsl_s16(int32x4_t, int16x4_t, int16x4_t)
</font>

| v_s32 | 2147483647 | 0 | 10 | -10 | 
| :---: | ---: | ---: | ---: | ---: | 
| v_s16 | 1 | 32767 | 3 | 2 | | | | |
| v_s16 | -1 | 2 | 3 | -2 | | | | |
| v_s32 | **2147483647** | -131068 | -8 | -2 | | | | |
<br>

### 3.9 vector saturating doubling long multiply:

<font color=blue>
int32x4_t vqdmull_s16(int16x4_t, int16x4_t)
</font>

| v_s16 | 32767 | -32768 | 32767 | -32768 | 
| :---: | ---: | ---: | ---: | ---: |
| v_s16 | -32768 | -32768 | 2 | 2 | | | | |
| v_s32 | **-2147418112** | **2147483647** | 131068 | -131072 | | | | |

<div STYLE="page-break-after: always;"></div>

## 4 Compare
----
> 比较的结果全部为无符号向量。

<br>

### 4.1 vector compare equal:

<font color=blue>
uint16x8_t vceqq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 1 | 2 | 3 | -1 | -2 | -3 | 0 | 32767 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 2 | 1 | 3 | -2 | -1 | -3 | 0 | -32768 |
| v_u16 | 0 | 0 | 65535 | 0 | 0 | 65535 | 65535 | 0 |

<font color=blue>
uint16x8_t vceqq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 0 | 0 | 2 | 1 | 4 | 3 | 5 |
| v_u16 | 65535 | 0 | 65535 | 0 | 0 | 0 | 0 | 65535 |
<br>

### 4.2 vector compare greater-than or equal:

<font color=blue>
uint16x8_t vcgeq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 1 | 2 | 3 | -1 | -2 | -3 | 0 | 32767 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 2 | 1 | 3 | -2 | -1 | -3 | 0 | -32768 |
| v_u16 | 0 | 65535 | 65535 | 65535 | 0 | 65535 | 65535 | 65535 |

<font color=blue>
uint16x8_t vcgeq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 0 | 0 | 2 | 1 | 4 | 3 | 5 |
| v_u16 | 65535 | 65535 | 65535 | 0 | 65535 | 0 | 65535 | 65535 |
<br>

### 4.3 vector compare less-than or equal:

<font color=blue>
uint16x8_t vcleq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 1 | 2 | 3 | -1 | -2 | -3 | 0 | 32767 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 2 | 1 | 3 | -2 | -1 | -3 | 0 | -32768 |
| v_u16 | 65535 | 0 | 65535 | 0 | 65535 | 65535 | 65535 | 0 |

<font color=blue>
uint16x8_t vcgeq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 0 | 0 | 2 | 1 | 4 | 3 | 5 |
| v_u16 | 65535 | 0 | 65535 | 65535 | 0 | 65535 | 0 | 65535 |
<br>

### 4.4 vector compare greater-than:

<font color=blue>
uint16x8_t vcgtq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 1 | 2 | 3 | -1 | -2 | -3 | 0 | 32767 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 2 | 1 | 3 | -2 | -1 | -3 | 0 | -32768 |
| v_u16 | 0 | 65535 | 0 | 65535 | 0 | 0 | 0 | 65535 |

<font color=blue>
uint16x8_t vcgtq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 0 | 0 | 2 | 1 | 4 | 3 | 5 |
| v_u16 | 0 | 65535 | 0 | 0 | 65535 | 0 | 65535 | 0 |
<br>

### 4.5 vector compare less-than:
<font color=blue>
uint16x8_t vcltq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 1 | 2 | 3 | -1 | -2 | -3 | 0 | 32767 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 2 | 1 | 3 | -2 | -1 | -3 | 0 | -32768 |
| v_u16 | 65535 | 0 | 0 | 0 | 65535 | 0 | 0 | 0 |

<font color=blue>
uint16x8_t vcltq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 0 | 0 | 2 | 1 | 4 | 3 | 5 |
| v_u16 | 0 | 0 | 0 | 65535 | 0 | 65535 | 0 | 0 |
<br>

### 4.6 vector absolute compare:

<font color=blue>
uint32x4_t vcageq_f32(float32x4_t, float32x4_t) -- greater or equal
</font>

| v_f32 | -3.000000e+00 | -2.000000e+00 | -1.000000e+00 | -0.000000e+00 |
| :---: | ---:          | ---:          | ---:          | ---:          |         
| v_f32 | 1.000000e+00  | 2.000000e+00  | 3.000000e+00  | 0.000000e+00  |
| v_u32 | 0xffffffff    | 0xffffffff    | 0             | 0xffffffff    |

<font color=blue>
uint32x4_t vcaleq_f32(float32x4_t, float32x4_t) -- less or equal
</font>

| v_f32 | -3.000000e+00 | -2.000000e+00 | -1.000000e+00 | -0.000000e+00 |
| :---: | ---: | ---: | ---: | ---: |       
| v_f32 | 1.000000e+00 | 2.000000e+00 | 3.000000e+00 | 0.000000e+00 |
| v_u32 | 0 | 0xffffffff | 0xffffffff | 0xffffffff |

<font color=blue>
uint32x4_t vcagtq_f32(float32x4_t, float32x4_t) -- greater than
</font>

| v_f32 | -3.000000e+00 | -2.000000e+00 | -1.000000e+00 | -0.000000e+00 | 
| :---: | ---: | ---: | ---: | ---: |      
| v_f32 | 1.000000e+00 | 2.000000e+00 | 3.000000e+00 | 0.000000e+00 |
| v_u32 | 0xffffffff | 0 | 0 | 0 |

<font color=blue>
uint32x4_t vcaltq_f32(float32x4_t, float32x4_t) -- less than
</font>

| v_f32 | -3.000000e+00 | -2.000000e+00 | -1.000000e+00 | -0.000000e+00 | 
| :---: | ---: | ---: | ---: | ---: |    
| v_f32 | 1.000000e+00 | 2.000000e+00 | 3.000000e+00 | 0.000000e+00 |
| v_u32 | 0 | 0 | 0xffffffff | 0 |
<br>

### 4.7 vector test bits:
> 向量input1和input2做AND,如果结果不为0,将输出向量的所有bit设为1, 否则设为0.
<font color=blue>

uint16x8_t vtstq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | -1(0xffff) | -16(0xfff0) | 255(0x00ff) | -3856(0xf0f0) | -1(0xffff) | -241(0xff0f) | -1(0xffff) | -241(0xff0f) |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0(0x0000) | 15(0x000f) | -256(0xff00) | 3855(0x0f0f) | -15(0xfff1) | 241(0x00f1) | 4096(0x1000) | 3840(0x0f00) |
| v_u16 | 0 | 0 | 0 | 0 | 65535 | 65535 | 65535 | 65535 |

<font color=blue>
uint16x8_t vtstq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535(0xffff) | 65520(0xfff0) | 255(0x00ff) | 61680(0xf0f0) | 65535(0xffff) | 65295(0xff0f) | 65535(0xffff) | 65295(0xff0f) |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 0(0x0000) | 15(0x000f) | 65280(0xff00) | 3855(0x0f0f) | 65521(0xfff1) | 241(0x00f1) | 4096(0x1000) | 3840(0x0f00) |
| v_u16 | 0 | 0 | 0 | 0 | 65535 | 65535 | 65535 | 65535 |


<div STYLE="page-break-after: always;"></div>


## 5 Absolute difference
----
<br>

### 5.1 absolute diff:

<font color=blue>
int16x8_t vabdq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | -1 | -32768 | 32767 | 0 | 16 | 16384 | 16383 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -1 | 32767 | 1 | -32768 | 0 | 0 | -16384 | -16384 |
| v_s16 | **-32768** | **-32768** | **-32767** | **-1** | 0 | 16 | **-32768** | 32767 |
<font color=red> 注意：如果数值溢出，则会出现负数。 </font>

> 这个命令的逻辑应该就是大的减小的；
> 比如说： 
> > abd(32767, -1)
> > res = 32767 - (-1) = 0x7fff - 0xffff = 0x8000 = -32768
> > abd(-32768, 1)
> > res = 1 - (-32768) = 0x0001 - 0x8000 = 0x8001 = -32767

<font color=blue>
uint16x8_t vabdq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 65535 | 0 | 8 | 64 | 512 | 4096 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 0 | 1 | 65535 | 0 | 128 | 1024 | 8192 | 16384 |
| v_u16 | 65535 | 65534 | 0 | 0 | 120 | 960 | 7680 | 12288 |
<br>

### 5.2 absolute diff long:

<font color=blue>
int16x8_t vabdl_s8(int8x8_t, int8x8_t)
</font>

| v_s8 | 127 | -128 | -128 | 127 | 0 | 16 | 64 | 127 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_s8 | -128 | 127 | 1 | -1 | 0 | 32 | 127 | 64 |
| v_s16 | 255 | 255 | 129 | 128 | 0 | 16 | 63 | 63 |

<font color=blue>
uint16x8_t vabdl_u8(uint8x8_t, uint8x8_t)
</font>

| v_u8 | 255 | 255 | 255 | 0 | 8 | 32 | 64 | 128 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8 | 0 | 1 | 255 | 0 | 128 | 15 | 71 | 8 |
| v_u16 | 255 | 254 | 0 | 0 | 120 | 17 | 7 | 120 |
<br>

### 5.3 absolute diff and accumulate:

<font color=blue>
int16x8_t vabaq_s16(int16x8_t, int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | -32768 | 0 | 0 | 0 | 1 | 2 | 3 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_s16 | 1 | -1 | 32767 | -32768 | 1 | 3 | 5 | 7 |
| v_s16 | 0 | 0 | -1 | 1 | 2 | 5 | 8 | 11 |
| v_s16 | **-32768** | -32767 | **-32768** | **-32767** | 1 | 3 | 5 | 7 |      

<font color=blue>
uint16x8_t vabaq_u16(uint16x8_t, uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 0 | 65535 | 0 | 1 | 2 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_u16 | 1 | 65535 | 65535 | 0 | 2 | 4 | 6 | 8 |
| v_u16 | 0 | 0 | 65535 | 0 | 3 | 6 | 9 | 12 |
| v_u16 | **0** | 65535 | 65535 | 0 | 2 | 4 | 6 | 8 |
<br>

### 5.4 absolute diff and accumulate-long:

<font color=blue>
int16x8_t vabal_s8(int16x8_t, int8x8_t, int8x8_t)
</font>

| v_s16 | 32767 | -32768 | 0 | 32767 | 0 | 1 | 2 | 3 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 1 | -128 | 127 | 127 | 0 | 16 | 64 | 127 |
| v_s8 | 0 | 0 | -128 | -128 | 0 | 32 | 127 | 64 |
| v_s16 | **-32768** | **-32640** | 255 | **-32514** | 0 | 17 | 65 | 66 |

<font color=blue>
uint16x8_t vabal_u8(uint8x8_t, uint8x8_t)
</font>

| v_u16 | 65535 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 1 | 255 | 255 | 0 | 8 | 32 | 64 | 128 |
| v_s8 | 0 | 0 | 255 | 0 | 128 | 15 | 71 | 8 |
| v_u16 | **0** | **254** | 65535 | 0 | 121 | 19 | 10 | 124 |

<div STYLE="page-break-after: always;"></div>


## 6 Max and Min
----
<br>

### 6.1 max:

<font color=blue>
int16x8_t vmaxq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | -1 | -32768 | 32767 | 0 | 16 | 16384 | 16383 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -1 | 32767 | 1 | -32768 | 0 | 0 | -16384 | -16384 |
| v_s16 | 32767 | 32767 | 1 | 32767 | 0 | 16 | 16384 | 16383 |

<font color=blue>
uint16x8_t vmaxq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 0 | 0 | 2 | 1 | 4 | 3 | 5 |
| v_u16 | 65535 | 65535 | 0 | 2 | 2 | 4 | 4 | 5 |
<br>

### 6.2 min:

<font color=blue>
int16x8_t vminq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | -1 | -32768 | 32767 | 0 | 16 | 16384 | 16383 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -1 | 32767 | 1 | -32768 | 0 | 0 | -16384 | -16384 |
| v_s16 | -1 | -1 | -32768 | -32768 | 0 | 0 | -16384 | -16384 |

<font color=blue>
uint16x8_t vminq_u16(uint16x8_t, uint16x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 1 | 2 | 3 | 4 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 0 | 0 | 2 | 1 | 4 | 3 | 5 |
| v_u16 | 65535 | 0 | 0 | 1 | 1 | 3 | 3 | 5 |

<div STYLE="page-break-after: always;"></div>

## 7 Pairwise
----
<br>

> **注意**：此时的指令不再是两个向量对应的元素进行操作，而是相邻两个元素进行操作。

### 7.1 pairwise add:

<font color=blue>
int8x8_t vpadd_s8(int8x8_t, int8x8_t)
</font>

> 先将第一个输入向量的相邻元素两两相加,再对第二个输入向量的相邻元素两两相加,然后将结果先后存入输出向量中.

| v_s8 | 127 | 1 | -128 | -1 | 127 | -128 | 0 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | -3 | -2 | -1 | 0 | 1 | 2 | 3 | 4 |
| v_s8 | **-128** | **127** | -1 | 0 | -5 | -1 | 3 | 7 |

<font color=blue>
uint8x8_t vpadd_u8(uint8x8_t, uint8x8_t)
</font>

| v_u8 | 255 | 255 | 255 | 1 | 255 | 2 | 255 | 3 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| v_u8 | **254** | **0** | **1** | **2** | 1 | 5 | 9 | 13 |
<br>

### 7.2 long pairwise add:

<font color=blue>
int16x4_t vpaddl_s8(int8x8_t)
</font>

| v_s8 | 127 | 1 | -128 | -1 | 127 | -128 | 0 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 128 | -129 | -1 | 1 | | | | |

<font color=blue>
uint16x4_t vpaddl_u8(uint8x8_t)
</font>

| v_u8 | 255 | 255 | 255 | 1 | 1 | 2 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 510 | 256 | 3 | 7 | | | | |
<br>

### 7.3 long pairwise add and accumulate:

<font color=blue>
int16x4_t vpadal_s8(int16x4_t, int8x8_t)
</font>

| v_s16 | 32767 | -32768 | 0 | 0 | | | | |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 1 | 0 | -1 | 0 | 127 | 1 | -128 | -1 |
| v_s16 | **-32768** | **32767** | 128 | -129 | | | | |

<font color=blue>
uint16x4_t vpadal_u8(uint16x4_t, uint8x8_t)
</font>

| v_u16 | 65535 | 65535 | 0 | 0 | | | | |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8 | 1 | 0 | 255 | 1 | 255 | 1 | 3 | 4 |
| v_u16 | **0** | **255** | 256 | 7 | | | | |

<div STYLE="page-break-after: always;"></div>
<br>

### 7.4 pairwise maximum:

> 将相邻元素两两成对比较,保留最大值

<font color=blue>
int8x8_t vpmax_s8(int8x8_t, int8x8_t)
</font>

| v_s8 | 127 | 1 | -128 | -1 | 127 | -128 | 0 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | -3 | -2 | -1 | 0 | 1 | 2 | 3 | 4 |
| v_s8 | 127 | -1 | 127 | 0 | -2 | 0 | 2 | 4 |

<font color=blue>
uint8x8_t vpmax_u8(uint8x8_t, uint8x8_t)
</font>

| v_u8 | 255 | 255 | 255 | 1 | 255 | 2 | 255 | 3 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| v_u8 | 255 | 255 | 255 | 255 | 1 | 3 | 5 | 7 |
<br>

### 7.5 pairwise minimum:

> 将相邻元素两两成对比较,保留最小值

<font color=blue>
int8x8_t vpmin_s8(int8x8_t, int8x8_t)
</font>

| v_s8 | 127 | 1 | -128 | -1 | 127 | -128 | 0 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | -3 | -2 | -1 | 0 | 1 | 2 | 3 | 4 |
| v_s8 | 1 | -128 | -128 | 0 | -3 | -1 | 1 | 3 |

<font color=blue>
uint8x8_t vpmin_u8(uint8x8_t, uint8x8_t)
</font>

| v_u8 | 255 | 255 | 255 | 1 | 255 | 2 | 255 | 3 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| v_u8 | 255 | 1 | 2 | 3 | 0 | 2 | 4 | 6 |

<div STYLE="page-break-after: always;"></div>

## 8 Shift 
----


> **注意**：左移的时候，正数和负数都补0. 右移时，负数补1，正数补0；

<br>

### 8.1 shift left by vector:

> 如果移位变量为正,左移; 如果为负, 右移. 

<font color=blue>
int16x8_t vshlq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | -32768 | 8192 | 8192 | -24576 | -24576 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 1 | 1 | 2 | 1 | 2 | 8 | 8 |
| v_s16 | -2 | 0 | 16384 | -32768 | 16384 | -32768 | 256 | -256 |

| v_s16 | 32767 | -32768 | -1 | -2 | -3 | -4 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -1 | -1 | -1 | -2 | -2 | -2 | 1 | 1 |
| v_s16 | 16383 | -16384 | -1 | -1 | -1 | -1 | 6 | 8 |

----
> * <font color=red> shift过多位数可能会导致未定义行为。 </font>
> 
>| v_s16 | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
>| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
>| v_s16 | -127 | -128 | -129 | -130 | -131 | -241 | -247 | -253 |
>| v_s16 | -1 | -1 | 0 | 0 | 0 | -32768 | -512 | -8 |

<font color=blue>
uint16x8_t vshlq_u16(uint16x8_t, int16x8_t)
</font>

| v_u16 | 65535 | 65535 | 65535 | 65535 | 1 | 1 | 256 | 256 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 2 | -1 | -2 | 1 | -1 | 8 | -8 |
| v_u16 | 65534 | 65532 | 32767 | 16383 | 2 | 0 | 0 | 1 |
<br>

### 8.2 saturating shift left by vector:

<font color=blue>
int16x8_t vqshlq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | 32767 | -32768 | -32768 | -1 | -1 | 1 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | -1 | 1 | -1 | 1 | -1 | 1 | -1 |
| v_s16 | 32767 | 16383 | -32768 | -16384 | -2 | -1 | 2 | 0 |

| v_s16 | 32767 | 32767 | -32768 | -16384 | 8192 | 8192 | -24576 | -24576 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -14 | 15 | -15 | -1 | 1 | 2 | 1 | 2 |
| v_s16 | 1 | 32767 | -1 | -8192 | 16384 | 32767 | -32768 | -32768 |

<font color=blue>
uint16x8_t vqshlq_u16(uint16x8_t, int16x8_t)
</font>

| v_u16 | 65535 | 65535 | 65535 | 65535 | 1 | 1 | 256 | 256 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 2 | -1 | -2 | 1 | -1 | 8 | -8 |
| v_u16 | 65535 | 65535 | 32767 | 16383 | 2 | 0 | 65535 | 1 |
<br>

### 8.3 rounding shift left by vector:

<font color=blue>
int16x8_t vrshlq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | -32768 | -1 | -7 | -6 | 5 | 6 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 1 | -1 | -2 | -2 | -2 | -2 | -1 |
| v_s16 | -2 | 0 | 0 | -2 | -1 | 1 | 2 | 1 |

<font color=blue>
uint16x8_t vrshlq_u16(uint16x8_t, int16x8_t)
</font>

| v_u16 | 65535 | 256 | 5 | 6 | 24 | 23 | 384 | 383 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 8 | -1 | -2 | -4 | -4 | -8 | -8 |
| v_u16 | 65534 | 0 | 3 | 2 | 2 | 1 | 2 | 1 |
<br>

### 8.4 saturating rounding shift left by vector:

<font color=blue>
int16x8_t vqrshlq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 32767 | -32768 | -1 | -7 | -6 | 5 | 6 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 1 | -1 | -2 | -2 | -2 | -2 | -1 |
| v_s16 | 32767 | -32768 | 0 | -2 | -1 | 1 | 2 | 0 |

<font color=blue>
uint16x8_t vqrshlq_u16(uint16x8_t, int16x8_t)
</font>

| v_u16 | 65535 | 256 | 5 | 6 | 24 | 23 | 384 | 383 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 8 | -1 | -2 | -4 | -4 | -8 | -8 |
| v_u16 | 65535 | 65535 | 3 | 2 | 2 | 1 | 2 | 1 |
<br>

### 8.5 shift right by constant:

<font color=blue>
int16x8_t  vshrq_n_s16(int16x8_t, __constrange(1,16) int)    
</font>

> const:  1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |      
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_s16 | 16383 | -16384 | 63 | 64 | -64 | -64 | 0 | -1 |

<font color=blue>
uint16x8_t vshrq_n_u16(uint16x8_t, __constrange(1,16) int)  
</font>

> const: 8

| v_u16 | 65535 | 384 | 383 | 256 | 255 | 128 | 127 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_u16 | 255 | 1 | 1 | 1 | 0 | 0 | 0 | 0 |
<br>

### 8.6 shift left by constant:

<font color=blue>
int16x8_t  vshlq_n_s16(int16x8_t, __constrange(1,16) int) 
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |      
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_s16 | -2 | 0 | 254 | 256 | -254 | -256 | 2 | -2 |

<font color=blue>
uint16x8_t vshlq_n_u16(uint16x8_t, __constrange(1,16) int) 
</font>

> const: 8

| v_u16 | 65535 | 384 | 383 | 256 | 255 | 128 | 127 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_u16 | 65280 | 32768 | 32512 | 0 | 65280 | 32768 | 32512 | 256 |
<br>

### 8.7 rounding shift right by constant:

<font color=blue>
int16x8_t  vrshrq_n_s16(int16x8_t, __constrange(1,16) int) 
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |      
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_s16 | 16384 | -16384 | 64 | 64 | -63 | -64 | 1 | 0 |

<font color=blue>
uint16x8_t vrshrq_n_u16(uint16x8_t, __constrange(1,16) int)
</font>

> const: 8

| v_u16 | 65535 | 384 | 383 | 256 | 255 | 128 | 127 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |  
| v_u16 | 256 | 2 | 1 | 1 | 1 | 1 | 0 | 0 |
<br>

### 8.8 saturating shift left by constant:

<font color=blue>
int16x8_t  vqshlq_n_s16(int16x8_t, __constrange(0,15) int)
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 32767 | -32768 | 254 | 256 | -254 | -256 | 2 | -2 |

<font color=blue>
uint16x8_t  vqshlq_n_u16(uint16x8_t, __constrange(0,15) int)
</font>

> const: 8

| v_u16 | 65535 | 384 | 383 | 256 | 255 | 128 | 127 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 65535 | 65535 | 65535 | 65280 | 32768 | 32512 | 256 |
<br>

### 8.9 signed-to-unsigned saturating shift left by constant:

> negative number will be set as zero

<font color=blue>
uint16x8_t  vqshluq_n_s16(int16x8_t, __constrange(0,15) int)
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65534 | 0 | 254 | 256 | 0 | 0 | 2 | 0 |
<br>

### 8.10 narrow shift right by constant:

<font color=blue>
int8x8_t   vshrn_n_s16(int16x8_t, __constrange(1,8) int)
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8  | -1 | 0 | 63 | 64 | -64 | -64 | 0 | -1 |

<font color=blue>
uint8x8_t  vshrn_n_u16(uint16x8_t, __constrange(1,8) int)
</font>

> const: 8

| v_u16 | 65535 | 384 | 383 | 256 | 255 | 128 | 127 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 255 | 1 | 1 | 1 | 0 | 0 | 0 | 0 |
<br>

### 8.11 signed-to-unsigned narrowing saturating shift right by constant:

<font color=blue>
uint8x8_t  vqshrun_n_s16(int16x8_t, __constrange(1,8) int)
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 255 | 0 | 63 | 64 | 0 | 0 | 0 | 0 |
<br>

### 8.12 signed-to-unsigned rounding narrowing saturating shift right by constant:

<font color=blue>
uint8x8_t  vqrshrun_n_s16(int16x8_t, __constrange(1,8) int)
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 255 | 0 | 64 | 64 | 0 | 0 | 1 | 0 |
<br>

### 8.13 narrowing saturating shift right by constant:

<font color=blue>
int8x8_t   vqshrn_n_s16(int16x8_t, __constrange(1,8) int)
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8  | 127 | -128 | 63 | 64 | -64 | -64 | 0 | -1 |

<font color=blue>
uint8x8_t  vqshrn_n_u16(uint16x8_t, __constrange(1,8) int)
</font>

> const: 8

| v_u16 | 65535 | 384 | 383 | 256 | 255 | 128 | 127 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 255 | 1 | 1 | 1 | 0 | 0 | 0 | 0 |
<br>

### 8.14 rounding narrowing shift right by constant:

<font color=blue>
int8x8_t   vrshrn_n_s16(int16x8_t, __constrange(1,8) int)
</font>

> const: 1

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8  | 0 | 0 | 64 | 64 | -63 | -64 | 1 | 0 |

<font color=blue>
uint8x8_t  vrshrn_n_u16(uint16x8_t, __constrange(1,8) int)
</font>

> const: 8

| v_u16 | 65535 | 384 | 383 | 256 | 255 | 128 | 127 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 0 | 2 | 1 | 1 | 1 | 1 | 0 | 0 |
<br>

### 8.15 rounding narrowing saturating shift right by constant:

<font color=blue>
int8x8_t   vqrshrn_n_s16(int16x8_t, __constrange(1,8) int)
</font>

> const: 8

| v_s16 | 32767 | -32768 | 127 | 128 | -127 | -128 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8  | 127 | -128 | 64 | 64 | -63 | -64 | 1 | 0 |

<font color=blue>
uint8x8_t  vqrshrn_n_u16(uint16x8_t, __constrange(1,8) int)
</font>

> const: 8

| v_u16 | 65535 | 384 | 383 | 256 | 255 | 128 | 127 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 255 | 2 | 1 | 1 | 1 | 1 | 0 | 0 |
<br>

### 8.16 widening shift left by constant:

<font color=blue>
int16x8_t  vshll_n_s8(int8x8_t, __constrange(0,8) int)
</font>

> const: 8

| v_s8  | 127 | -128 | 64 | -64 | 2 | -2 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 32512 | -32768 | 16384 | -16384 | 512 | -512 | 256 | -256 |

<font color=blue>
uint16x8_t  vshll_n_u8(uint8x8_t, __constrange(0,8) int)
</font>

> const: 8

| v_u8  | 255 | 128 | 64 | 32 | 16 | 8 | 4 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65280 | 32768 | 16384 | 8192 | 4096 | 2048 | 1024 | 256 |


<div STYLE="page-break-after: always;"></div>

## 9 Combine and Split
----
<br>

### 9.1 combine:

<font color=blue>
uint16x8_t vcombine_u16(uint16x4_t, uint16x4_t)
</font>

> 将输入的64bits的向量按照低位高位的顺序排列在一起，形成一个128bits的向量

| v_u16 | 0 | 1 | 2 | 3 | | | | |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 4 | 5 | 6 | 7 | | | | |
| v_u16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
<br>

### 9.2 split:

<font color=blue>
uint16x4_t vget_low_u16(uint16x8_t)
uint16x4_t vget_high_u16(uint16x8_t)   
</font>

> 将128bits长的向量进行分解，得到低位/高位的64bits的向量。

| v_u16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| low | 0 | 1 | 2 | 3 | | | | |
| high | 4 | 5 | 6 | 7 | | | | |
<br>

### 9.3 transpose:

<font color=blue>
int16x8x2_t vtrnq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
| v_s16x2.val[0] | 0 | 8 | 2 | 10 | 4 | 12 | 6 | 14 |
| v_s16x2.val[1] | 1 | 9 | 3 | 11 | 5 | 13 | 7 | 15 |
<br>

### 9.4 interleave:

<font color=blue>
int16x8x2_t vzipq_s16(int16x8_t, int16x8_t)
</font>

> 将输入向量进行交错排列

| v_s16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
| v_s16x2.val[0] | 0 | 8 | 1 | 9 | 2 | 10 | 3 | 11 |
| v_s16x2.val[1] | 4 | 12 | 5 | 13 | 6 | 14 | 7 | 15 |
<br>

### 9.5 De-Interleave:

<font color=blue>
int16x8x2_t vuzpq_s16(int16x8_t, int16x8_t)
</font>

> interleave命令的逆操作

| v_s16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
| v_s16x2.val[0] | 0 | 2 | 4 | 6 | 8 | 10 | 12 | 14 |
| v_s16x2.val[1] | 1 | 3 | 5 | 7 | 9 | 11 | 13 | 15 |
<br>

### 9.6 shift right and insert:

<font color=blue>
uint16x8_t  vsriq_n_u16(uint16x8_t, uint16x8_t, __constrange(1,16) int)
</font>

> const: 4

| v_u16 | 0xffff | 0x0fff | 0xc000 | 0x3000 | 0xffff | 0x0000 | 0xcfff | 0x3fff |
| :---: | ---:   | ---:  | ---:  | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 0xffff | 0x7fff | 0x3fff | 0x1fff | 0x0fff | 0x07ff | 0x03ff | 0x01ff |
| v_u16 | 0xffff | 0x07ff | 0xc3ff | 0x31ff | 0xf0ff | 0x007f | 0xc03f | 0x301f |

> res = vsriq_n_u16(a, b, n)
> res = `a`最高位的n个bit + `b`最高位的(16 - n)个bit

<br>

### 9.7 shift left and insert:

<font color=blue>
int16x8_t  vsliq_n_s16(int16x8_t, int16x8_t, __constrange(0,15) int)
</font>

> const: 4

| v_s16 | 0xffff | 0x0000 | 0xff00 | 0x00ff | 0x0f0f | 0xf0f0 | 0xfff0 | 0x000f |
| :---: | ---:  | ---:   | ---:   | ---:  | ---:   | ---:  | ---:   | ---:  |
| v_s16 | 0xffff |  0xfffc| 0xfff0 | 0xffc0 | 0xff00 | 0xfc00 | 0xf000 | 0xc000 |
| v_s16 | 0xffff | 0xffc0 | 0xff00 | 0xfc0f | 0xf00f | 0xc000 | 0x0000 | 0x000f |

> res = vsliq_n_s16(a, b, n)
> res = `a`最低位的n个bit + `b`最低位的(16 - n)个bit

<br>

 
### 9.8 extraction

<font color=blue>
int16x8_t vextq_s16(int16x8_t, int16x8_t, __constrange(0, 7) int)
</font>

> const: 2

| v_s16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
| v_s16 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |

> 输入的向量（假设长度为n，这里n=8）中，从第一个向量中的高位拿出（n - const）个元素长度，从第二个向量中的低位拿出const个元素长度，然后拼接在一块。

<div STYLE="page-break-after: always;"></div>

## 10 Logical 
----

### 10.1 Bitwise NOT:

<font color=blue>
int16x8_t vmvnq_s16(int16x8_t)
</font>

> 所有的bit翻转。

| v_s16 |  0x7fff | 0x8000 | 0xffff | 0x0000 | 0x0001 | 0xfffe | 0x00ff | 0xff00 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16  | 0x8000| 0x7fff | 0x0000| 0xffff | 0xfffe | 0x0001  | 0xff00  | 0x00ff |
<br>

### 10.2 Bitwise AND:

<font color=blue>
int16x8_t vandq_s16(int16x8_t)
</font>

| v_s16 | 0xffff | 0x0000 | 0xffff | 0x0000 | 0x00ff | 0xff00 | 0x00ff | 0x00ff |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0xffff | 0x0000 | 0x0000 | 0xffff | 0xff00 | 0xffff | 0x0000 | 0x00ff |
| v_s16 | 0xffff | 0x0000 | 0x0000 | 0x0000 | 0x0000 | 0xff00 | 0x0000 | 0x00ff |
<br>

### 10.3 Bitwise OR:

<font color=blue>
int16x8_t vorrq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 0xffff | 0x0000 | 0xffff | 0x0000 | 0x00ff | 0xff00 | 0x00ff | 0x00ff |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0xffff | 0x0000 | 0x0000 | 0xffff | 0xff00 | 0xffff | 0x0000 | 0x00ff |
| v_s16 | 0xffff | 0x0000 | 0xffff | 0xffff | 0xffff | 0xffff | 0x00ff | 0x00ff |
<br>

### 10.4 Bitwise exclusive or (EOR or XOR):

<font color=blue>
int16x8_t veorq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 |  0xffff | 0x0000 | 0xffff | 0x0000 | 0x00ff | 0xff00 | 0x00ff | 0x00ff |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0xffff | 0x0000 | 0x0000 | 0xffff | 0xff00 | 0xffff | 0x0000 | 0x00ff |
| v_s16 | 0x0000 | 0x0000 | 0xffff | 0xffff | 0xffff | 0x00ff | 0x00ff | 0x0000 |
<br>

### 10.5 Bit Clear:

<font color=blue>
int16x8_t vbicq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 0xffff | 0x0000 | 0xffff | 0x0000 | 0x00ff | 0xff00 | 0x00ff | 0x00ff |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0xffff | 0x0000 | 0x0000 | 0xffff | 0xff00 | 0xffff | 0x0000 | 0x00ff |
| v_s16 | 0x0000 | 0x0000 | 0xffff | 0x0000 | 0x00ff | 0x0000 | 0x00ff | 0x0000 |

> res = 第二个向量的补 与 第一个向量 做AND操作
> 或者也可以这么理解：如果遇到第二个向量的bit为1，那么将第一个向量中对应的bit置为0.

<br>

### 10.6 Bitwise OR complement:

<font color=blue>
int16x8_t vornq_s16(int16x8_t, int16x8_t)
</font>

| v_s16 | 0xffff | 0x0000 | 0xffff | 0x0000 | 0x00ff | 0xff00 | 0x00ff | 0x00ff |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0xffff | 0x0000 | 0x0000 | 0xffff | 0xff00 | 0xffff | 0x0000 | 0x00ff |
| v_s16 | 0xffff | 0xffff | 0xffff | 0x0000 | 0x00ff | 0xff00 | 0xffff | 0xffff |

> res = 第二个向量的补 与 第一个向量 做OR操作。（n代表not，先做not再做or，所以指令名字是orn）
> 或者也可以这么理解：如果遇到第二个向量的bit为0，那么将第一个向量中对应的bit置为1.

<br>     

### 10.7 Bitwise Select:

<font color=blue>
int16x8_t vbslq_s16(int16x8_t, int16x8_t, int16x8_t)
</font>

| v_s16 | 0xffff | 0x0000 | 0xff00  | 0x00ff | 0x0f0f | 0xf0f0 | 0x0ff0 | 0xf00f |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0xffff | 0x0000 | 0xffff | 0x0000 | 0xffff | 0x0000 | 0xffff | 0x0000 |
| v_s16 | 0x0000 | 0xffff | 0x0000 | 0xffff | 0x0000 | 0xffff | 0x0000 | 0xffff |
| v_s16 | 0xffff | 0xffff | 0xff00 | 0xff00 | 0x0f0f | 0x0f0f | 0x0ff0 | 0x0ff0 |

> res = 第一个向量 ? 第二个向量 : 第三个向量.
> 如果第一个向量的bit为1，取第二个向量的bit，否则取第三个向量的bit作为结果向量对应的bit。

<br>

### 10.8 Reversing vector elements:

<font color=blue>
int16x8_t vrev64q_s16(int16x8_t)
</font>

> 将一个向量中的每段64bits进行翻转，然后存入结果向量中。

| v_s16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 3 | 2 | 1 | 0 | 7 | 6 | 5 | 4 |

<font color=blue>
int16x8_t vrev32q_s16(int16x8_t)
</font>

> 将一个向量中的每段32bits进行翻转，然后存入结果向量中。

| v_s16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | 0 | 3 | 2 | 5 | 4 | 7 | 6 |

<div STYLE="page-break-after: always;"></div>


## 11 Sign Related
----
<br>

### 11.1 absolute:

<font color=blue>
int16x8_t vabsq_s16(int16x8_t)
</font>

> 取有符号向量的绝对值。（如果元素为负，即最高位为1，将所有的bit进行翻转，然后+1）

| v_s16 | 32767 | -32768 | 32766 | -32767 | -2 | 2 | -1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 32767 | <font color=red>-32768</font> | 32766 | 32767 | 2 | 2 | 1 | 0 |

> **注意**：这里的-32768将所有bit进行翻转后变为32767，然后+1，数值溢出再次变成-32768。所以，经过这个指令得到的结果并不总>=0。

<br>

### 11.2 saturating absolute:

<font color=blue>
int16x8_t vqabsq_s16(int16x8_t)
</font>

> 如果担心上面的溢出问题，应当使用这个指令进行操作。

| v_s16 | 32767 | -32768 | 32766 | -32767 | -2 | 2 | -1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 32767 | 32767 | 32766 | 32767 | 2 | 2 | 1 | 0 |

> **注意**：此时-32768对应的绝对值也不再是准确的了，而是饱和后的32767.

<br>

### 11.3 negate:

<font color=blue>
int16x8_t vnegq_s16(int16x8_t)
</font>

> 将向量中的每个元素进行正负转换。
> -- 负数变成正数： 将所有bit进行翻转，然后+1.
> -- 正数变成负数： -1，然后将所有bit进行翻转。

| v_s16 | 32767 | -32768 | 32766 | -32767 | -2 | 2 | -1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -32767 | <font color=red>-32768</font> | -32766 | 32767 | 2 | -2 | 1 | 0 |
<br>

### 11.4 saturating negate:

<font color=blue>
int16x8_t vqnegq_s16(int16x8_t)
</font>

| v_s16 | 32767 | -32768 | 32766 | -32767 | -2 | 2 | -1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -32767 | 32767 | -32766 | 32767 | 2 | -2 | 1 | 0 |
<br>

### 11.5 count leading sign bits:

<font color=blue>
int16x8_t vclsq_s16(int16x8_t)
</font>

> 元素的最高位作为sign bit（不计算在内），然后从最高位开始数：
> 如果是负数（sign bit = 1），数连续的1的个数
> 如果是正数（sign bit = 0），数连续的0的个数

| v_s16 | 32767 | -32768 | 16384 | -16384 | 5 | -3 | -1 | 0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0 | 0 | 0 | 1 | 12 | 13 | 15 | 15 |

<br>

### 11.6 count leading zeros:

<font color=blue>
int16x8_t vclzq_s16(int16x8_t)
</font>

> 对每个元素从最高位开始数，数连续的0的个数。

| v_s16 | -1 | -32768 | 32767 | 4096 | 255 | 4 | 3 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0 | 0 | 1 | 3 | 8 | 13 | 14 | 15 |
<br>

<div STYLE="page-break-after: always;"></div>

## 12 Look-Up Table
----
<br>

### 12.1 table look up:

> **注意**： 超过索引范围的结果将被置为0. 支持对三维向量进行查表。

<font color=blue>
int8x8_t vtbl1_s8(int8x8_t v_table, int8x8_t v_index)
</font>

| v_table | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_index | -1 | 5 | 4 | 3 | 2 | 1 | 0 | 8 |
| v_res | 0 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |

<font color=blue>
uint8x8_t vtbl1_u8(uint8x8_t v_table, uint8x8_t v_index)
</font>

| v_table | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_index | 6 | 5 | 4 | 3 | 2 | 1 | 0 | 8 |
| v_res | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |

<font color=blue>
int8x8_t vtbl2_s8(int8x8x2_t v_table, int8x8_t v_index)
</font>

| v_table.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_table.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| v_index | -1 | 14 | 10 | 6 | 2 | 1 | 0 | 16 |
| v_res | 0 | 15 | 11 | 7 | 3 | 2 | 1 | 0 |

<font color=blue>
int8x8_t vtbl4_s8(int8x8x4_t v_table, int8x8_t v_index)
</font>

| v_table.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_table.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| v_table.val[2] | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 |
| v_table.val[3] | 25 | 26 | 27 | 28 | 29 | 30 | 31 | 32 |
| v_index | -1 | 32 | 31 | 23 | 15 | 8 | 7 | 0 |
| v_res | 0 | 0 | 32 | 24 | 16 | 9 | 8 | 1 |
<br>

### 12.2 extended table look up:

> **注意**： 超过索引范围的结果将被置为默认向量中的对应元素.

<font color=blue>
int8x8_t vtbx1_s8(int8x8_t v_default, int8x8_t v_table, int8x8 v_index)
</font>

| v_default | -1 | -2 | -3 | -4 | -5 | -6 | -7 | -8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_table | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| v_index | -1 | 9 | 7 | 5 | 3 | 1 | 10 | -3 |
| v_res | -1 | -2 | 8 | 6 | 4 | 2 | -7 | -8 |

<font color=blue>
int8x8_t vtbx2_s8(int8x8_t v_default, int8x8x2_t v_table, int8x8_t v_index)
</font>

| v_default | -1 | -2 | -3 | -4 | -5 | -6 | -7 | -8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_table.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| v_table.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| v_index | -1 | 16 | 15 | 8 | 7 | 0 | -2 | -3 |
| v_res | -1 | -2 | 16 | 9 | 8 | 1 | -7 | -8 |

<font color=blue>
int8x8_t vtbx4_s8(int8x8_t v_default, int8x8x4_t v_table, int8x8_t v_index)
</font>

| v_default | -1 | -2 | -3 | -4 | -5 | -6 | -7 | -8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_table.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| v_table.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| v_table.val[2] | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 |
| v_table.val[3] | 25 | 26 | 27 | 28 | 29 | 30 | 31 | 32 |
| v_index | -1 | 32 | 31 | 23 | 15 | 7 | 0 | -2 |
| v_res | -1 | -2 | 32 | 24 | 16 | 8 | 1 | -8 |

<div STYLE="page-break-after: always;"></div>

## 13 Load
----

`short data_src[] = { 0,  1,  2,  3,  4,  5,  6,  7, 8,  9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31};`
<br>

### 13.1 Load a single vector from memory:

<font color=blue>
int16x8_t vld1q_s16(int16_t const *data_src)
</font>

| v_load | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |

<font color=blue>
int16x8x2_t vld2q_s16(int16_t *data_src)
</font>

> **注意**： 使用ld2的指令后，数据会被交错存入。

| v_load.val[0] | 0 | 2 | 4 | 6 | 8 | 10 | 12 | 14 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_load.val[1] | 1 | 3 | 5 | 7 | 9 | 11 | 13 | 15 |

<font color=blue>
int16x8x3_t vld3q_s16(int16_t *data_src)
</font>

> **注意**： 使用ld3的指令后，数据会被交错存入。

| v_load.val[0] | 0 | 3 | 6 | 9 | 12 | 15 | 18 | 21 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_load.val[1] | 1 | 4 | 7 | 10 | 13 | 16 | 19 | 22 |
| v_load.val[2] | 2 | 5 | 8 | 11 | 14 | 17 | 20 | 23 |

<font color=blue>
int16x8x4_t vld4q_s16(int16_t *data_src)
</font>

> **注意**： 使用ld4的指令后，数据会被交错存入。

| v_load.val[0] | 0 | 4 | 8 | 12 | 16 | 20 | 24 | 28 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_load.val[1] | 1 | 5 | 9 | 13 | 17 | 21 | 25 | 29 |
| v_load.val[2] | 2 | 6 | 10 | 14 | 18 | 22 | 26 | 30 |
| v_load.val[3] | 3 | 7 | 11 | 15 | 19 | 23 | 27 | 31 |
<br>

### 13.2 Load a lane vector from memory:

> only load for specific lane

<font color=blue>
int16x8_t vld1q_lane_s16(int16_t *data_src, int16x8_t v_ori,  __constrange(0,7) int lane)
</font>

> lane = 2
 
| v_ori | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_res | -1 | -1 | **0** | -1 | -1 | -1 | -1 | -1 |

<font color=blue>
int16x8x2_t vld2q_lane_s16(int16_t *data_src, int16x8x2_t v_ori,  __constrange(0,7) int lane)
</font>

> lane = 2

| v_ori.val[0] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_ori.val[1] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| v_res.val[0] | -1 | -1 | **0** | -1 | -1 | -1 | -1 | -1 |
| v_res.val[1] | -1 | -1 | **1** | -1 | -1 | -1 | -1 | -1 |

<font color=blue>
int16x8x3_t vld3q_lane_s16(int16_t *data_src, int16x8x3_t v_ori,  __constrange(0,7) int lane)
</font>

> lane = 2

| v_ori.val[0] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_ori.val[1] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| v_ori.val[2] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| v_res.val[0] | -1 | -1 | **0** | -1 | -1 | -1 | -1 | -1 |
| v_res.val[1] | -1 | -1 | **1** | -1 | -1 | -1 | -1 | -1 |
| v_res.val[2] | -1 | -1 | **2** | -1 | -1 | -1 | -1 | -1 |

<font color=blue>
int16x8x4_t vld4q_lane_s16(int16_t *data_src, int16x8x4_t v_ori,  __constrange(0,7) int lane)
</font>

> lane = 2

| v_ori.val[0] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_ori.val[1] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| v_ori.val[2] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| v_ori.val[3] | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| v_res.val[0] | -1 | -1 | **0** | -1 | -1 | -1 | -1 | -1 |
| v_res.val[1] | -1 | -1 | **1** | -1 | -1 | -1 | -1 | -1 |
| v_res.val[2] | -1 | -1 | **2** | -1 | -1 | -1 | -1 | -1 |
| v_res.val[3] | -1 | -1 | **3** | -1 | -1 | -1 | -1 | -1 |
<br>

### 13.3 Load all lanes of vector with same value from memory:

<font color=blue>
int16x8_t vld1q_dup_s16(int16_t *(data_src+5) )
</font>

| v_res | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |

<font color=blue>
int16x4x2_t vld2q_dup_s16(int16_t *(data_src+5))
</font>

| v_res.val[0] | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_res.val[1] | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |

<font color=blue>
int16x4x3_t vld3q_dup_s16(int16_t *(data_src+5))
</font>

| v_res.val[0] | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_res.val[1] | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |
| v_res.val[2] | 7 | 7 | 7 | 7 | 7 | 7 | 7 | 7 |

<font color=blue>
int16x4x4_t vld4q_dup_s16(int16_t *(data_src+5))
</font>

| v_res.val[0] | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_res.val[1] | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |
| v_res.val[2] | 7 | 7 | 7 | 7 | 7 | 7 | 7 | 7 |
| v_res.val[3] | 8 | 8 | 8 | 8 | 8 | 8 | 8 | 8 |
<br>

### 13.4 Load a single lane of a vector from a literal:

<font color=blue>
int16x8_t vsetq_lane_s16(int16_t value, int16x8_t v_ori, __constrange(0, 7) int lane)
</font>

> value = 1, lane = 2

| v_s16 | -1 | -1 | -1 | -1 | -1 | -1 | -1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -1 | -1 | **1** | -1 | -1 | -1 | -1 | -1 |

<div STYLE="page-break-after: always;"></div>

## 14 Store
----
<br>

### 14.1 Store a single vector into memory:

<font color=blue>
void vst1q_s16(int16_t *dst_data, int16x8_t v_sotre)
</font>

| v_sotre | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| dst_data | 1 | 2 | 3 | 4 |5 | 6 | 7 | 8 |

<font color=blue>
void vst2q_s16(int16_t *dst_data, int16x8x2_t v_sotre)
</font>

> **注意**：存入的时候，data会被交错存储。

| v_sotre.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_sotre.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| dst_data |  1 | 9 | 2 | 10 | 3 | 11 | 4 | 12 |
|       |  5 | 13 | 6 | 14 | 7 | 15 | 8 | 16 |

<font color=blue>
void vst3q_s16(int16_t *dst_data, int16x8x3_t v_sotre)
</font>

> **注意**：存入的时候，data会被交错存储。

| v_sotre.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_sotre.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| v_sotre.val[2] | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 |
| dst_data |  1 | 9 | 17 | 2 | 10 | 18 | 3 | 11 |
|        |  19 | 4 | 12 | 20 | 5 | 13 | 21 | 6 |
|        |  14 | 22 | 7 | 15 | 23 | 8 | 16 | 24 |

<font color=blue>
void vst4q_s16(int16_t *dst_data, int16x8x4_t v_sotre)
</font>

> **注意**：存入的时候，data会被交错存储。

| v_sotre.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_sotre.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| v_sotre.val[2] | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 |
| v_sotre.val[3] | 25 | 26 | 27 | 28 | 29 | 30 | 31 | 32 |
| dst_data |  1 | 9 | 17 | 25 | 2 | 10 | 18 | 26 |
|       |  3 | 11 | 19 | 27 | 4 | 12 | 20 | 28 |
|       |  5 | 13 | 21 | 29 | 6 | 14 | 22 | 30 |
|       |  7 | 15 | 23 | 31 | 8 | 16 | 24 | 32 |
<br>

### 14.2 Store a lane into memory:

<font color=blue>
void vst1q_lane_s16(int16_t *dst_data, int16x8_t v_store, __constrange(0, 7) int lane)
</font>

> lane = 2

| v_store | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| dst_data | 3 |

<font color=blue>
void vst2q_lane_s16(int16_t *dst_data, int16x8x2_t v_store, __constrange(0, 7) int lane)
</font>

> lane = 2

| v_store.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_store,val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| dst_data |  3 | 11 | 

<font color=blue>
void vst3q_lane_s16(int16_t *dst_data, int16x8x3_t v_store, __constrange(0, 7) int lane)
</font>

> lane = 2

| v_store.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_store.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| v_store.val[2] | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 |
| dst_data |  3 | 11 | 19 |

<font color=blue>
void vst4q_lane_s16(int16_t *dst_data, int16x8x4_t v_store, __constrange(0, 7) int lane)
</font>

> lane = 2

| v_store.val[0] | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_store.val[1] | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| v_store.val[2] | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 |
| v_store.val[3] | 25 | 26 | 27 | 28 | 29 | 30 | 31 | 32 |
| dst_data |  3 | 11 | 19 | 27 | 


<div STYLE="page-break-after: always;"></div>

## 15 Init
----
<br>

### 15.1 Load all lanes of vector to the same literal value:

<font color=blue>
int16x8_t vdupq_n_s16(int16_t value) // VDUP.16 q0,r0:
</font>

> value = 1

| v_s16 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |

<font color=blue>
int16x8_t vmovq_n_s16(int16_t value) // VDUP.16 q0,r0:
</font>

> value = 2

| v_s16 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
> 与上一个指令实际上是一个汇编指令。

<br>

### 15.2 Load all lanes of vector to the value of a lane of a vector:

<font color=blue>
int16x4_t vdup_lane_s16(int16x4_t vec, __constrange(0, 3) int lane) // VDUP.16 q0,r0:
</font>

> lane = 2

| v_s16 | 1 | 2 | 3 | 4 | 
| :---: | ---: | ---: | ---: | ---: |
| v_s16 | 3 | 3 | 3 | 3 |

<font color=blue>
int16x8_t vdupq_lane_s16(int16x4_t vec, __constrange(0, 3) int lane) // VDUP.16 q0,r0:
</font>

> lane = 2

| v_s16 | 1 | 2 | 3 | 4 | | | | |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 3 |
<br>

### 15.3 Initializing a vector from a literal bit pattern:

<font color=blue>
int16x4_t vcreate_s16(uint64_t a)
</font>

> 从一个64bits的元素创建一个64bits的向量。
> uint64_t a = uint64_t(0x000f,00ff,0fff,ffff)

| v_s16 | -1 | 4095 | 255 | 15 |
| :---: | ---: | ---: | ---: | ---: |

> 我们可以看到，输入元素的0~15位是向量的最后一个元素，16~31位是倒数第二个元素，32~47位是第二位元素，48~63位是第一位元素。
<br>

### 15.4 Extracing lanes from a vector into a register:

<font color=blue>
int16_t vgetq_lane_s16(int16x8_t vec, __constrange(0, 7) int lane)
</font>

| v_s16 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |

> lane = 2, res = 2

<div STYLE="page-break-after: always;"></div>


## 16 Convert 
----
<br>

### 16.1 convert from float:

> MAX_INT:  2,147,483,647
> MIN_INT:  -2,147,483,648
> MAX_UINT:  4,294,967,295

<font color=blue>
int32x4_t vcvtq_s32_f32(float32x4_t)
</font>

| v_f32 | -2.147484e+09 | 2.147484e+09 | -2.147483e+09 | 2.147483e+09 |
| :---: | ---: | ---: | ---: | ---: |
| v_s32 | **807539936** | **545005626** | -2147483008 | 2147483008 |

> 当float的数值超过int范围的时候，结果产生错误，具体行为待研究。

<font color=blue>
uint32x4_t vcvtq_u32_f32(float32x4_t)
</font>

| v_f32 | -1.100000e+00 | 4.294968e+09 | 4.294967e+09 | 1.100000e+00 |
| :---: | ---: | ---: | ---: | ---: |
| v_u32 | 807539936 | 545005626 | 4294967040 | 1 |

> 当float的数值为负超过int范围的时候，结果产生错误，具体行为待研究。当float在u32范围内时，仅保留整数部分。
<br>

### 16.2 convert to float:

> **注意**：由于float的精度有限，转换可能会丢失一定精度。

<font color=blue>
float32x4_t vcvtq_f32_s32(int32x4_t)
</font>

| v_s32 | 2147483647 | -2147483648 | -87654321 | 87654321 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 2.147484e+09 | -2.147484e+09 | -8.765432e+07 | 8.765432e+07 |

<font color=blue>
float32x4_t vcvtq_f32_u32(uint32x4_t)
</font>

| v_u32 | 4294967295 | 1 | 1234567 | 87654321 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 4.294967e+09 | 1.000000e+00 | 1.234567e+06 | 8.765432e+07 |
<br>

### 16.3 convert btw floats:

> MAX_FLOAT16: 65504
> MIN_FLOAT16: -65504
[float16 - wikipedia](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)

<font color=blue>
int32x4_t vcvt_f16_f32(float32x4_t)
</font>

> 数值超过float16的范围可能会变成inf，但是稍稍超过一点好像不会。（可能跟bits的操作有关系，有待后续查证。）

| v_f32 | -6.550400e+04 | 6.550440e+04 | -6.550410e+04 | 6.552000e+04 |
| :---: | ---: | ---: | ---: | ---: |
| v_f16 | -6.550400e+04 | 6.550400e+04 | -6.550400e+04 | inf |
<br>

### 16.4 vector narrow integer:

> 保留最低位。

<font color=blue>
int8x8_t vmovn_s16(int16x8_t)
</font>

> **注意**：这个指令并不会考虑输入向量的正负。

| v_s16 | 0xffff | 0xfff | 0x3e7f | 0xabd | 0x7a9c | 0x60a | **0xff3a** | 0x0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 0xff| 0xff| 0x7f| 0xbd| 0x9c| 0xa| **0x3a**| 0x0 |

> 0xff3a为负值，经过这个指令后变成正值0x3a。

<font color=blue>
uint8x8_t vmovn_u16(uint16x8_t)
</font>

| v_u16 | 0xffff | 0xfff | 0x3e7f | 0xabd | 0x7a9c | 0x60a | 0xff3a | 0x0 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8  | 0xff| 0xff| 0x7f| 0xbd| 0x9c| 0xa| 0x3a| 0x0 |
<br>

### 16.5 vector long move:

<font color=blue>
int16x8_t vmovl_s8(int8x8_t)
</font>

| v_s8 | 127 | -128 | -1 | 0 | 1 | 2 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 127 | -128 | -1 | 0 | 1 | 2 | 3 | 4 |

<font color=blue>
uint16x8_t vmovl_u8(uint8x8_t)
</font>

| v_u8 | 255 | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 255 | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
<br>

### 16.6 vector saturating narrow integer:

<font color=blue>
int8x8_t vqmovn_s16(int16x8_t)
</font>

> **注意**：饱和指令不仅对元素进行了饱和narrow，而且也对负数进行了判断。

| v_s16 | 32767 | -32768 | -129 | -128 | 128 | 127 | 0 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s8 | 127 | -128 | -128 | -128 | 127 | 127 | 0 | 1 |

<font color=blue>
uint8x8_t vqmovn_u16(uint16x8_t)
</font>

| v_u16 | 65535 | 256 | 255 | 0 | 1 | 2 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8 | 255 | 255 | 255 | 0 | 1 | 2 | 3 | 4 |
<br>

### 16.7 vector saturating narrow integer signed->unsigned:

<font color=blue>
uint8x8_t vqmovun_s16(int16x8_t)
</font>

> 负数一律被饱和成0.

| v_s16 | 32767 | -32768 | -1 | 256 | 255 | 0 | 1 | 2 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u8 | 255 | 0 | 0 | 255 | 255 | 0 | 1 | 2 |
<br>

### 16.8 vector cast operation

> 储存的字节并没有变，只是类型声明变了而已。

<font color=blue>
uint16x8_t vreinterpretq_u16_s16(int16x8_t)
</font>

| v_s16 | 32767 | -32768 | 1 | -1 | 2 | -2 | 256 | -256 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 32767 | 32768 | 1 | 65535 | 2 | 65534 | 256 | 65280 |

<font color=blue>
int16x8_t vreinterpretq_s16_u16(uint16x8_t)
</font>

| v_u16 | 32767 | 32768 | 1 | 65535 | 2 | 65534 | 256 | 65280 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 32767 | -32768 | 1 | -1 | 2 | -2 | 256 | -256 |

<div STYLE="page-break-after: always;"></div>

## 17 With Scalar
----

<br>

### 17.1 vector multiply accumulate with scalar:


<font color=blue>
int16x8_t vmlaq_lane_s16(int16x8_t, int16x8_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s16 | 0 | 0 | 2 | -1 | 1 | 0 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 32767 | -32768 | 16383 | -16384 | 16383 | -16384 | 1 | -1 |
| v_s16 | 0 | 1 | **2** | 3 | | | | |
| v_s16 | -2 | 0 | -32768 | 32767 | 32767 | -32768 | 3 | -3 |


<font color=blue>
uint16x8_t vmlaq_lane_u16(uint16x8_t, uint16x8_t, uint16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_u16 | 0 | 0 | 1 | 1 | 1 | 1 | 1 | 1 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65535 | 32768 | 32767 | 4096 | 1024 | 256 | 1 | 0 |
| v_u16 | 0 | 1 | **2** | 3 | | | | |
| v_u16 | 65534 | 0 | 65535 | 8193 | 2049 | 513 | 3 | 1 |
<br>

### 17.2 vector widening multiply accumulate with scalar:

<font color=blue>
int32x4_t vmlal_lane_s16(int32x4_t, int16x4_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s32 | 2147483647 | -2147483648 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | -1 | 32767 | -32768 |
| v_s16 | 0 | 1 | **2** | 3 |
| v_s32 | -2147483647 | 2147483646 | 65535 | -65537 |

<font color=blue>
uint32x4_t vmlal_lane_u16(uint32x4_t, uint16x4_t, uint16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_u32 | 4294967295 | 1 | 1 | 1 |
| :---: | ---: | ---: | ---: | ---: |
| v_u16 | 1 | 65535 | 1 | 2 |
| v_u16 | 0 | 1 | **2** | 3 |
| v_u32 | 1 | 131071 | 3 | 5 |
<br>

### 17.3 vector widening saturating doubling multiply accumulate with scalar:

<font color=blue>
int32x4_t vqdmlal_lane_s16(int32x4_t, int16x4_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s32 | 2147483647 | -2147483648 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | -1 | 32767 | -32768 |
| v_s16 | 0 | 1 | **2** | 3 |
| v_s32 | 2147483647 | -2147483648 | 131069 | -131073 |
<br>

### 17.4 vector multiply subtract with scalar:

<font color=blue>
int16x8_t vmlsq_lane_s16(int16x8_t, int16x8_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s16 | 32767 | -32768 | 1 | -1 | 2 | -2 | 4 | -4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | 
| v_s16 | -1 | 1 | 0 | 2 | -4 | 6 | -8 | 16 |
| v_s16 | 0 | 1 | **2** | 3 | | | | |
| v_s16 | -32767 | 32766 | 1 | -5 | 10 | -14 | 20 | -36 |

<font color=blue>
uint16x8_t vmlsq_lane_u16(uint16x8_t, uint16x8_t, uint16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_u16 | 1 | 65535 | 10 | 20 | 30 | 40 | 50 | 60 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | 
| v_u16 | 1 | 32768 | 0 | 1 | 2 | 3 | 4 | 5 |
| v_u16 | 0 | 1 | **2** | 3 | | | | |
| v_u16 | 65535 | 65535 | 10 | 18 | 26 | 34 | 42 | 50 |
<br>

### 17.5 vector widening multiply subtract with scalar:

<font color=blue>
int32x4_t vmlsl_lane_s16(int32x4_t, int16x4_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s32 | 2147483647 | -2147483648 | 3 | -1 |
| :---: | ---: | ---: | ---: | ---: |       
| v_s16 | -1 | 1 | 1 | -2 |
| v_s16 | 0 | 1 | **2** | 3 |
| v_s32 | -2147483647 | 2147483646 | 1 | 3 |

<font color=blue>
uint32x4_t vmlsl_lane_u16(uint32x4_t, uint16x4_t, uint16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_u32 | 4294967295 | 10 | 20 | 30 |
| :---: | ---: | ---: | ---: | ---: |     
| v_u16 | 32768 | 1 | 2 | 3 |
| v_u16 | 0 | 1 | **2** | 3 |
| v_u32 | 4294901759 | 8 | 16 | 24 |
<br>

### 17.6 vector widening saturating doubling multiply subtract with scalar:

<font color=blue>
int32x4_t vqdmlsl_lane_s16(int32x4_t, int16x4_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s32 | 2147483647 | -2147483648 | 10 | 20 |
| :---: | ---: | ---: | ---: | ---: |      
| v_s16 | -1 | 1 | 4 | 5 |
| v_s16 | 0 | 1 | **2** | 3 |
| v_s32 | 2147483647 | -2147483648 | -6 | 0 |
<br>

### 17.7 vector multiply by scalar:

<font color=blue>
int16x8_t vmulq_n_s16(int16x8_t, int16_t scalar)
</font>

> scalar = 2

| v_s16 | 32767 | -32768 | 16384 | -16384 | -1 | 1 | 2 | -2 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | -2 | 0 | -32768 | -32768 | -2 | 2 | 4 | -4 |

<font color=blue>
uint16x8_t vmulq_n_u16(uint16x8_t, uint16_t scalar)
</font>

> scalar = 2

| v_u16 | 65535 | 32768 | 32767 | 0 | 1 | 2 | 3 | 4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_u16 | 65534 | 0 | 65534 | 0 | 2 | 4 | 6 | 8 |
<br>

### 17.8 vector long multiply with scalar:

<font color=blue>
int32x4_t vmull_n_s16(int16x4_t, int16_t scalar)
</font>

> scalar = 2;
 
| v_s16 | 32767 | -32768 | 16384 | -16384 |
| :---: | ---: | ---: | ---: | ---: |
| v_s32 | 65534 | -65536 | 32768 | -32768 |
<br>

### 17.9 vector long multiply by scalar:

<font color=blue>
int32x4_t vmull_lane_s16(int16x4_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s16 | 32767 | -32768 | 16384 | -16384 |
| :---: | ---: | ---: | ---: | ---: |
| v_s16 | 0 | 1 | **2** | 3 |
| v_s32 | 65534 | -65536 | 32768 | -32768 |
<br>

### 17.10 vector saturating doubling long multiply with scalar:

<font color=blue>
int32x4_t vqdmull_n_s16(int16x4_t, int16_t scalar)
</font>

> scalar = 2

| v_s16 | 32767 | -32768 | 16384 | -16384 |
| :---: | ---: | ---: | ---: | ---: |
| v_s32 | 131068 | -131072 | 65536 | -65536 |
<br>

### 17.11 vector saturating doubling long multiply by scalar:

<font color=blue>
int32x4_t vqdmull_lane_s16(int16x4_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s16 | 32767 | -32768 | 16384 | -16384 |
| :---: | ---: | ---: | ---: | ---: |
| v_s16 | 0 | 1 | **2** | 3 |
| v_s32 | 131068 | -131072 | 65536 | -65536 |
<br>

### 17.12 vector saturating doubling multiply high with scalar:

> 这个操作的逻辑是：先将结果想象成 2n 位的，然后取高位的 n 位作为结果。（并不代表编译器逻辑）

<font color=blue>
int16x8_t vqdmulhq_n_s16(int16x8_t, int16_t scalar)
</font>

> scalar = 256
 
| v_s16 | 32767 | -32768 | 1 | -1 | 255 | -256 | 127 | -128 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 255 | -256 | 0 | -1 | 1 | -2 | 0 | -1 |

> 我们以32767 x 256举例。按照8进制表示方法，前面的乘法可以表示成 0x7fff x 0x00000100, 结果应为0x007fff00。然后结果保留高位的16位字节，所以最终结果为 0x007f（255）。

<br>

### 17.13 vector saturating doubling multiply high by scalar:

<font color=blue>
int16x8_t vqdmulhq_lane_s16(int16x8_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s16 | 32767 | -32768 | 1 | -1 | 255 | -256 | 127 | -128 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0 | 1 | **2** | 3 | | | | |
| v_s16 | 1 | -2 | 0 | -1 | 0 | -1 | 0 | -1 |
<br>

### 17.14 vector saturating rounding doubling multiply high with scalar:   

<font color=blue>
int16x8_t vqrdmulhq_n_s16(int16x8_t, int16_t scalar)
</font>

> scalar = 256
 
| v_s16 | 32767 | -32768 | 1 | -1 | 255 | -256 | 127 | -128 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 256 | -256 | 0 | 0 | 2 | -2 | 1 | -1 |
<br>

### 17.15 vector rounding saturating doubling multiply high by scalar: 

<font color=blue>
int16x8_t vqrdmulhq_lane_s16(int16x8_t, int16x4_t, __constrange(0,3) int lane)
</font>

> lane = 2

| v_s16 | 32767 | -32768 | 1 | -1 | 255 | -256 | 127 | -128 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 0 | 1 | **2** | 3 | | | | |
| v_s16 | 2 | -2 | 0 | 0 | 0 | 0 | 0 | 0 |
<br>

### 17.16 vector multiply accumulate with scalar:

<font color=blue>
int16x8_t vmlaq_n_s16(int16x8_t, int16x8_t, int16_t scalar)
</font>

> scalar = 2

| v_s16 | 32767 | -32768 | 1 | -1 | 2 | -2 | 4 | -4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| v_s16 | 1 | -1 | 0 | 1 | 2 | 3 | 4 | 5 |
| v_s16 | -32767 | 32766 | 1 | 1 | 6 | 4 | 12 | 6 |
<br>

### 17.17 vector widening multiply accumulate with scalar:

<font color=blue>
int32x4_t vmlal_n_s16(int32x4_t, int16x4_t, int16_t scalar)
</font>

> scalar = 2

| v_s32 | 2147483647 | -2147483648 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: |  
| v_s16 | 1 | -1 | 32767 | -32768 |
| v_s32 | -2147483647 | 2147483646 | 65535 | -65537 |
<br>

### 17.18 vector widening saturating doubling multiply accumulate with scalar:

<font color=blue>
int32x4_t vqdmlal_n_s16(int32x4_t, int16x4_t, int16_t scalar)
</font>

> scalar = 2

| v_s32 | 2147483647 | -2147483648 | 1 | -1 |
| :---: | ---: | ---: | ---: | ---: |  
| v_s16 | 1 | -1 | 32767 | -32768 |
| v_s32 | 2147483647 | -2147483648 | 131069 | -131073 |
<br>

### 17.19 vector multiply subtract with scalar:

<font color=blue>
int32x4_t vmlsq_n_s16(int16x8_t, int16x8_t, int16_t scalar)
</font>

> scalar = 2

| v_s16 | 32767 | -32768 | 1 | -1 | 2 | -2 | 4 | -4 |
| :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |    
| v_s16 | -1 | 1 | 0 | 1 | 2 | 3 | 4 | 5 |
| v_s16 | -32767 | 32766 | 1 | -3 | -2 | -8 | -4 | -14 |
<br>

### 17.20 vector widening multiply subtract with scalar:

<font color=blue>
int32x4_t vmlsl_n_s16(int32x4_t, int16x4_t, int16_t scalar)
</font>

> scalar = 2

| v_s32 | 2147483647 | -2147483648 | 1 | -1 | 
| :---: | ---: | ---: | ---: | ---: |   
| v_s16 | -1 | 1 | 32767 | -32768 | 
| v_s32 | -2147483647 | 2147483646 | -65533 | 65535 |  
<br>

### 17.21 vector widening saturating doubling multiply subtract with scalar:

<font color=blue>
int32x4_t vqdmlsl_n_s16(int32x4_t, int16x4_t, int16_t scalar)
</font>

> scalar = 2

| v_s32 | 2147483647 | -2147483648 | 1 | -1 | 
| :---: | ---: | ---: | ---: | ---: |    
| v_s16 | -1 | 1 | 32767 | -32768 | 
| v_s32 | 2147483647 | -2147483648 | -131067 | 131071 | 

<div STYLE="page-break-after: always;"></div>


## 18 Reciprocal Estimate
-------------
<br>

> **注意**：最后的结果与精确结果有一定差距，应该谨慎使用此类指令。

### 18.1 Reciprocal estimate:

<font color=blue>
float32x4_t vrecpeq_f32(float32x4_t a)
</font>

> res = 1 / a

| v_f32 | 1.000000 | 2.000000 | 3.000000 | 0.000000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 0.998047 | 0.499023 | 0.333008 | inf |
<br>

### 18.2 Reciprocal square root estimate:

<font color=blue>
float32x4_t vrsqrteq_f32(float32x4_t a)
</font>

> res = 1 / sqrt(a)

| v_f32 | 1.000000 | 2.000000 | 3.000000 | 0.000000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 0.998047 | 0.705078 | 0.576172 | inf |
<br>

### 18.3 Two steps in an interation of the Newton-Raphson method :

<font color=blue>
float32x4_t vrecpsq_f32(float32x4_t a, float32x4_t b)
</font>

> res = 2 - (a * b)

| v_f32 | 1.000000 | 2.000000 | 3.000000 | 0.000000 |
| :----:|:----: | ----: | ----: | ----: |
| v_f32 | 2.000000 | 2.000000 | 2.000000 | 2.000000 |
| v_f32 | 0.000000 | -2.000000 | -4.000000 | 2.000000 |

<font color=blue>
float32x4_t vrsqrtsq_f32(float32x4_t a, float32x4_t b)
</font>

> res = (3 - a * b) / 2

| v_f32 | 1.000000 | 2.000000 | 3.000000 | 0.000000 |
| :----:| :----:  | ----: | ----: | ----: |
| v_f32 | 2.000000 | 2.000000  | 2.000000  | 2.000000 |
| v_f32 | 0.500000 | -0.500000 | -1.500000 | 1.500000 |

### TO ADD:
* vrecpsq_f32
* vrecpss_f32
* vrecpsd_f64
* vrecpsh_f16

* vrecpxh_f16
* vrecpxs_f32
* vrecpxd_f64

## 19 Complex (since Armv8.3-a)
-------------
<br>

> **注意**：工程的编译需要开启-march=armv8.3-a选项。
例子：
```shell
cmake_minimum_required(VERSION 2.8)
project(neon_intrinsics)
add_compile_options(-march=armv8.3a)
add_subdirectory(src)
```

vector complex mul: 

 **float32x4_t vcmlaq_f32(float32x4_t v0, float32x4_t v1, float32x4_t v2)** 
| v_f32 | 0.1000 | 0.2000 | 0.3000 | 0.4000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 1.0000 | 2.0000 | 3.0000 | 4.0000 |
| v_f32 | 5.0000 | 6.0000 | 7.0000 | 8.0000 |
| v_f32 | 5.1000 | 6.2000 | 21.3000 | 24.4000 |

> 在输入是连续的实部+虚部的时候， 假如 v0 = a + bi, v1 = c + di, v2 = e + fi, 结果实际上是v1的实部，分别乘以v2的实部/虚部，然后加上v0。

 **float32x4_t vcmlaq_rot90_f32(float32x4_t, float32x4_t, float32x4_t)** 
| v_f32 | 0.1000 | 0.2000 | 0.3000 | 0.4000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 1.0000 | 2.0000 | 3.0000 | 4.0000 |
| v_f32 | 5.0000 | 6.0000 | 7.0000 | 8.0000 |
| v_f32 | -11.9000 | 10.2000 | -31.7000 | 28.4000 |

> 在输入是连续的实部+虚部的时候， 假如 v0 = a + bi, v1 = c + di, v2 = e + fi, 结果实际上是v1的虚部，分别乘以v2的实部/虚部，然后加上v0。

 **float32x4_t vcmlaq_rot180_f32(float32x4_t, float32x4_t, float32x4_t)** 
| v_f32 | 0.1000 | 0.2000 | 0.3000 | 0.4000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 1.0000 | 2.0000 | 3.0000 | 4.0000 |
| v_f32 | 5.0000 | 6.0000 | 7.0000 | 8.0000 |
| v_f32 | -4.9000 | -5.8000 | -20.7000 | -23.6000 |

> 在输入是连续的实部+虚部的时候， 假如 v0 = a + bi, v1 = c + di, v2 = e + fi, 结果实际上是v1的实部取对应的负值，分别乘以v2的实部/虚部，然后加上v0。

 **float32x4_t vcmlaq_rot270_f32(float32x4_t, float32x4_t, float32x4_t)** 
| v_f32 | 0.1000 | 0.2000 | 0.3000 | 0.4000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 1.0000 | 2.0000 | 3.0000 | 4.0000 |
| v_f32 | 5.0000 | 6.0000 | 7.0000 | 8.0000 |
| v_f32 | 12.1000 | -9.8000 | 32.3000 | -27.6000 |

> 在输入是连续的实部+虚部的时候， 假如 v0 = a + bi, v1 = c + di, v2 = e + fi, 结果实际上是v1的虚部取对应的负值，分别乘以v2的实部/虚部，然后加上v0。

如果想实现复数的乘法的话，可以组合 ‘vcmlaq_f32’ 和 ‘vcmlaq_rot90_f32’ 来实现。
```c
// vc0 * vc1
float32x4_t v_res0 = vcmlaq_f32(v_zeros, vc0, vc1);
float32x4_t v_res  = vcmlaq_rot90_f32(v_res0, vc0, vc1);
```

如果想实现复数的共轭乘复数，可以组合 ‘vcmlaq_f32’ 和 ‘vcmlaq_rot270_f32’ 来实现。
```c
// conj(vc0) * vc1
float32x4_t v_res0 = vcmlaq_f32(v_zeros, vc0, vc1);
float32x4_t v_res  = vcmlaq_rot270_f32(v_res0, vc0, vc1);
```

 **float32x4_t vcmlaq_lane_f32(float32x4_t v0, float32x4_t v1, float32x4_t v2, const int lane)** 
lane = 0 
| v_f32 | 0.1000 | 0.2000 | 0.3000 | 0.4000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 1.0000 | 2.0000 | 3.0000 | 4.0000 |
| v_f32 | 5.0000 | 6.0000 | 7.0000 | 8.0000 |
| v_f32 | 5.1000 | 6.2000 | 15.3000 | 18.4000 |
lane = 1 
| v_f32 | 0.1000 | 0.2000 | 0.3000 | 0.4000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 1.0000 | 2.0000 | 3.0000 | 4.0000 |
| v_f32 | 5.0000 | 6.0000 | 7.0000 | 8.0000 |
| v_f32 | 7.1000 | 8.2000 | 21.3000 | 24.4000 |

类似 vcmlaq_f32 的做法，只不过 lane=0 的时候，只取 v2 的第一个复数进行计算，lane=1 的时候，只取 v2 的第二个复数进行计算。
其他的，还有 rot90/180/270 的版本，计算方式都是类似的。


vector complex add: （不知道如何解释， 暂时想不出来应用场景）

 **float32x4_t vcaddq_rot90_f32(float32x4_t, float32x4_t)** 
| v_f32 | 0.1000 | 0.2000 | 0.3000 | 0.4000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 1.0000 | 2.0000 | 3.0000 | 4.0000 |
| v_f32 | -1.9000 | 1.2000 | -3.7000 | 3.4000 |

 **float32x4_t vcaddq_rot270_f32(float32x4_t, float32x4_t)** 
| v_f32 | 0.1000 | 0.2000 | 0.3000 | 0.4000 |
| :---: | ---: | ---: | ---: | ---: |
| v_f32 | 1.0000 | 2.0000 | 3.0000 | 4.0000 |
| v_f32 | 2.1000 | -0.8000 | 4.3000 | -2.6000 |


