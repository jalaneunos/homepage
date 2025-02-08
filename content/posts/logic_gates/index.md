+++
date = '2025-02-08T23:51:21+08:00'
draft = true
title = 'logic gates'
+++

### NAND

![[nand_gate.png]](images/nand_gate.png)

| a   | b   | out |
| --- | --- | --- |
| 0   | 0   | 1   |
| 0   | 1   | 1   |
| 1   | 0   | 1   |
| 1   | 1   | 0   |

NAND takes two inputs, and will 1 unless both inputs are 1, in which case it will return 0.

NAND is an universal logic gate, which means any gate logic can be formed from it. We will use NAND as the building block for all other gates basic gates.

### NOT

![[not_gate.png]](images/not_gate.png)

| in  | out |
| --- | --- |
| 0   | 1   |
| 1   | 0   |

the NOT gate inverts its input.

The NOT gate can be formed by connecting both inputs of a NAND gate to the same source. In the NAND truth table, when both inputs are identical, the output is the inverse of the input.

| a     | b     | out   |
| ----- | ----- | ----- |
| 0     | 0     | 1     |
| ~~0~~ | ~~1~~ | ~~1~~ |
| ~~1~~ | ~~0~~ | ~~1~~ |
| 1     | 1     | 0     |

![[not_gate_impl.png]](images/not_gate_impl.png)

### AND

![[and_gate.png]](images/and_gate.png)

| a   | b   | out |
| --- | --- | --- |
| 0   | 0   | 0   |
| 0   | 1   | 0   |
| 1   | 0   | 0   |
| 1   | 1   | 1   |

the AND gate only outputs true when both inputs are true.

By inverting the output of NAND, we get AND. Since NOT(NAND) is NOT(NOT AND), which simplifies to AND.
![[and_gate_impl.png]](images/and_gate_impl.png)

### OR

![[or_gate.png]](images/or_gate.png)

| a   | b   | out |
| --- | --- | --- |
| 0   | 0   | 0   |
| 0   | 1   | 1   |
| 1   | 0   | 1   |
| 1   | 1   | 1   |

The OR gate outputs true if at least one of its inputs is true.

By inspecting the truth table, the only combination of inputs that results in output=0 occurs when a=0 and b=0.

1. OR is false when NOT a AND NOT b
2. By negating statement 1, OR becomes true when NOT (NOT a AND NOT b)

![[or_gate_impl.png]](images/or_gate_impl.png)

### XOR

![[xor_gate.png]](images/xor_gate.png)

| a   | b   | out |
| --- | --- | --- |
| 0   | 0   | 0   |
| 0   | 1   | 1   |
| 1   | 0   | 1   |
| 1   | 1   | 0   |

The Exclusive Or (XOR) gate outputs 1 (True) only when one of the inputs is 1.

Now that we have formed the {NOT, AND, OR} gates, we can implement any logic gate in an intuitive (albeit mostly suboptimal) manner.

#### the intuitive-but-suboptimal method,

1. Inspect the rows where output=1. The second row in XOR is the first instance of output=1.
2. The second row has inputs (a=0, b=1). (NOT a, b)
3. Since the output (1) requires both of these inputs to be simultaneously present, we can use the AND gate to represent this relationship. (NOT a AND b)
4. Repeating steps 2-3 for other rows where output=1, row 3 gives us: (a AND NOT b)
5. Finally, we observe that **either** of these rows will be sufficient for out=1. Hence, we can chain the rows with OR. (NOT a AND b) OR (a AND NOT b)

![[xor_gate_impl.png]](images/xor_gate_impl.png)

this method gives us an idiot-proof algorithm to implement any desired behaviour given the truth table, but it is often possible to introduce a few optimizations.

Consider this alternative implementation of XOR:
![[xor_gate_impl_optimized.png]](images/xor_gate_impl_optimized.png)
Only 3 gates are required, as supposed to 5 gates derived from the method. However, this optimization would require a little more creativity.

### Multiplexer

![[mux_gate.png]](images/mux_gate.png)

| a   | b   | sel | out |
| --- | --- | --- | --- |
| 0   | 0   | 0   | 0   |
| 0   | 0   | 1   | 0   |
| 0   | 1   | 0   | 0   |
| 0   | 1   | 1   | 1   |
| 1   | 0   | 0   | 1   |
| 1   | 0   | 1   | 0   |
| 1   | 1   | 0   | 1   |
| 1   | 1   | 1   | 1   |

A multiplexer (MUX) takes three inputs: two data inputs (a, b) and a selector bit (sel). sel determines which of the two inputs is passed as the output.

| sel | out |
| --- | --- |
| 0   | a   |
| 1   | b   |

This gives 8 possible combinations of inputs. Applying the intuitive-but-suboptimal method:

1. not A And B And Sel OR
2. A And Not B And Not Sel OR
3. A And B And Not Sel OR
4. A And B And Sel

We could directly implement this with logic gates we already have, but that would be quite tedious. On closer inspection of statements 1 and 4,

1. not A And ==B And Sel== OR
2. A And ==B And Sel==
   We observe that the value of A is inconsequential while B and Sel are present.
   Similarly with statements 2 & 3,
3. A And Not B And Not Sel OR
4. A And B And Not Sel OR
   The value of B does not matter when we have A and Not Sel.

The 4 statements can be reduced to
B and Sel OR A and Not Sel
Looking back at how the Mux gate is supposed to behave, we see that this makes sense intuitively and perhaps the brighter ones among us would have saw this answer immediately.

![[mux_gate_impl.png]](images/mux_gate_impl.png)

### Demultiplexer

![[dmux_gate.png]](images/dmux_gate.png)

| in  | sel | a (out) | b (out) |
| --- | --- | ------- | ------- |
| 0   | 0   | 0       | 0       |
| 0   | 1   | 0       | 0       |
| 1   | 0   | 1       | 0       |
| 1   | 1   | 0       | 1       |

Demultiplexer (DMUX) has two inputs and two outputs. Focusing on each output column in isolation,
a is true when: in And not sel
b is true when: in And sel
![[dmux_gate_impl.png]](images/dmux_gate_impl.png)

### A few more bits: Not16, Or16, And16

![[not16_gate.png]](images/not16_gate.png)

The gates above accept and produce one-bit inputs/outputs. On our journey towards artificial general intelligence, the ability to operate on more than 1-bit per operation would be helpful. So we aim for 16-bits.

The simplest implementations of the 16-bit gates would be to apply the 1-bit gate 16 times, in parallel.
![[not16_gate_impl.png]](images/not16_gate_impl.png)

Kindly exercise your imagination for And16 and Or16..

### Or8Way

Another gate that could be useful later is the ability to check if any of the inputs are true; the implementation is straightforward.
![[or8way_gate_impl.png]](images/or8way_gate_impl.png)

### Mux4Way16

![[mux4way16_gate.png]](images/mux4way16_gate.png)

| inputs |
| ------ |
| a      |
| b      |
| c      |
| d      |
| sel[0] |
| sel[1] |

| sel[0] | sel[1] | out |
| ------ | ------ | --- |
| 0      | 0      | a   |
| 0      | 1      | b   |
| 1      | 0      | c   |
| 1      | 1      | d   |

To extend Mux to support four input choices. our intuitive-but-suboptimal method would be a pain with this many input combinations (6 inputs, $2^6$ possible combinations), so let's endeavor to solve this one with natural language.

First we ignore the fact that we are working 16-bit inputs for a,b,c,d.

#### How can we extend 2 choices (Mux) to 4?

Mux is essentially an if-statement (though it's definitely more appropriate to say that if-statements are Muxes). We observe that for out=a and out=b, sel[0] is 0 in both cases. Choosing between a or b is determined by the sel[1] bit. (A problem that can be solved with 2-choice Mux)

```python
if not sel[0]: # sel: 0?
	if sel[1]: # sel: 01
		return b
	else: # sel: 00
		return a
```

completing the code for c & d,

```python
if not sel[1]: # sel: 0?
	if sel[0]: # sel: 01
		return b
	else: # sel: 00
		return a
else: # sel: 1?
	if sel[0]: # sel: 11
		return d
	else:  # sel: 10
		return c
```

![[mux4way16_gate_impl.png]](images/mux4way16_gate_impl.png)
We can extend this even further, to 8 ways, or $2^x$ ways by repeatedly applying Mux in the same manner.

To support 16-bit inputs, simply replace the gates in each Mux with Not16, And16 and Or16

### DMux4Way, DMux8Way

![[dmux4way_gate.png]](images/dmux4way_gate.png)
Extending Dmux is simpler than Mux as it does not involve nested logic. We write one additional group of 2 ANDs for each new output.

### More Than Just Stacking NANDs

We built all the fundamental logic gates starting from NAND, but that doesn't mean chip designers simply order buckets of NAND gates online and start assembling computers from scratch. In reality, chip designers operate at a higher level of abstraction. Hardware description languages (HDLs) like Verilog enable designers to dictate the desired logic behaviour, and the required logic gates are automatically synthesized.

But this wasn't a futile exercise, an adequate understanding of these foundational blocks will help us build more complex pieces required for practical computer features. In the next chapter, we will arrange the gates to build a key feature in a computer: arithmetic.
