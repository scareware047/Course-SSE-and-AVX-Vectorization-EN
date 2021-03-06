# What is SSE and AVX?

## History

In recent years, CPUs have reached some physical and power limitations, so CPU speeds have not increased noticeably, in terms of Ghz.
As computation requirements continue increasing, CPU designers have decided to solve this problem with three solutions:

-  Adding more cores. This way Operating Systems can distribute running applications among different cores. Also, programs can create multiple threads to maximize core usage.
-  Adding vector operations to each core. This solution allows the CPU to execute the same instructions on a vector of data. This can only be done at the application level.
-  Out-of-order execution of multiple instructions. Modern CPUs can execute up to four instructions at the same time if they are independent.
Read http://www.agner.org/optimize/blog/read.php?i=417 for more information

Vector registers started in 1997 with MMX instruction set, having 80-bit registers. After that SSE instruction sets were released (several versions of them, from SSE1 to SEE4.2), with 128-bit registers.
In 2011, Intel released the Sandy Bridge architecture with the AVX instruction set (256-bit registers).
In 2016 the first AVX-512 CPU was released, with 512-bit registers (up to 16x 32-bit float vectors).

In this Course we'll focus on both SSE and AVX instruction sets, because they are commonly found in recent processors. AVX-512 is out of scope, but most of the course can be reused, just by changing the 256-bit registers to the 512-bit counterparts (ZMM registers).

## SSE & AVX Registers

SSE and AVX have 16 registers each. On SSE they are referenced as XMM0-XMM15, and on AVX they are called YMM0-YMM15. XMM registers are 128 bits long, whereas YMM are 256bit.

SSE adds three typedefs: `__m128` , `__m128d` and `__m128i`. Float, double (d) and integer (i) 
respectively.

AVX adds three typedefs: `__m256` , `__m256d` and `__m256i`. Float, double (d) and integer (i) 
respectively.

![SSE & AVX Registers](/images/avx.png)

>**NOTE:** XMM and YMM overlap! XMM registers are treated as the lower half of the corresponding YMM register. This can introduce some performance issues when mixing SSE and AVX code.

Floating point datatypes (\_\_m128, \_\_m128d, \_\_m256 and \_\_m256d) have only one kind of data structure. Because of this, GCC allows for access to data components as an array.
 I.e: This is valid:
 
```cpp
  __m256 myvar = _mm256_set1_ps(6.665f); //Set all vector values to a single float
  myvar[0] = 2.22f;                       //This is valid in GCC compiler
  float f = (3.4f + myvar[0]) * myvar[7]; //This is valid in GCC compiler
```

\_\_m128i and \_\_m256i are unions, so the datatype needs to be referenced. I have not found a proper way to get the union declaration, so I use _mm_extract_epiXX() functions to retrieve individual data values from integer vectors.

## AVX Operation Example

When an AVX instruction is executed, the process is as follows:

![AVX Add](/images/avxplus.png)

All operations are applied at the same time. Performance-wise, the cost of executing a single `Add` on a float is similar to executing `VAdd` on 8 floats in AVX. In [Agner Fog's Instruction Tables](http://www.agner.org/optimize/instruction_tables.pdf), you have more information about instruction latencies and throughput.  On Sandy Bridge architecture, `VADDPS/D` has latency 3 and throughput 1, just like `FADD(P)`.

