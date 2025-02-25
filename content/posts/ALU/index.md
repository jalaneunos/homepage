+++
date = '2025-02-24T21:12:52+08:00'
draft = true
title = 'ALU'
+++


### Half Adder
The first step is to add two 1-bit numbers. The largest possible result is 2 (1+1). However, computers only recognize the digits 0 and 1. To handle this, we follow a process similar to how we count in decimal. For example, when counting, we go from 1, 2, 3, ..., 9, and then to **10**, adding a 1 to the tens place and resetting the ones place to 0 because we’ve run out of unique digits. In binary, the same principle applies. We count 0, 1, **10**.

![[half_adder.png]](images/half_adder.png)

We'll refer to the result in the **ones place** as the **sum**. Since we're working in binary, the **carry** represents the **twos place** instead of the tens place, as it would in decimal.


| a   | b   | sum | carry |
| --- | --- | --- | ----- |
| 0   | 0   | 0   | 0     |
| 0   | 1   | 1   | 0     |
| 1   | 0   | 1   | 0     |
| 1   | 1   | 0   | 1     |

By focusing on the **sum** and **carry** columns in isolation, we can immediately identify previously built logic gates that correspond to these results. **sum** can be represented with **XOR**, and **carry** can be represented with **AND**.

![[half_adder_impl.png]](images/half_adder_impl.png)
### Full Adder
The full adder takes three 1-bit inputs.

![[full_adder.png]](images/full_adder.png)

The maximum result is now 3 (11 in binary).

| a   | b   | c   | sum | carry |
| --- | --- | --- | --- | ----- |
| 0   | 0   | 0   | 0   | 0     |
| 0   | 0   | 1   | 1   | 0     |
| 0   | 1   | 0   | 1   | 0     |
| 0   | 1   | 1   | 0   | 1     |
| 1   | 0   | 0   | 1   | 0     |
| 1   | 0   | 1   | 0   | 1     |
| 1   | 1   | 0   | 0   | 1     |
| 1   | 1   | 1   | 1   | 1     |

A **full adder** can be constructed using two **half adders**. Here's how:

1. **half adder 1**
    - Use a half adder to add the first two inputs, **a** and **b**.
    - The output will give us two values:
        - **sum**: Represents the **ones place**.
        - **carry**: Represents the **twos place**.
2. **half adder 2**
	    - Next, we add the third input, **c**, to the **sum** obtained from the first half adder (**sum + c**).
    - We add these together because they belong to the same place value (**ones place**).

![[full_adder_impl_1.png]](images/full_adder_impl_1.png)

#### Identifying the correct carry
the above logic leaves us with **two carry outputs**—one from each half adder. 
Question is: **Which carry should we use?**

consider the following cases:
#### **case 1: carry1 = 1**
- If the first half adder’s carry output (**carry1**) is 1, it means the initial sum (**sum1**) must be 0 (refer to half adder's truth table).
- When the second half adder processes **sum1 + c**, it must be **0 + c**. This combination can never produce a binary result of `10`, so **carry2** must be 0.
- **In summary:** If **carry1 = 1**, then **carry2 = 0**.

#### **case 2: carry1 = 0**
- If **carry1** is 0, the intermediate sum (**sum1**) could be either 0 or 1.
- Consequently, the second half adder (**sum1 + c**) might produce a carry (**carry2**) of 0 or 1.

The takeaway is that **carry1** and **carry2** will **never** both be 1 at the same time. Therefore, the final carry output for the full adder can be determined by taking the **OR** of these two values.

![[full_adder_impl.png]](images/full_adder_impl.png)

### Adder
Now we wish to extend our addition capabilities from 1-bit values to **n-bit** values. 
Let's try 8-bits.
But how?

Let's examine how we would add two 8-bit values by hand.

```
  01010101
+ 00001111
----------
```

The first step to addition would be to add the digits in the ones-place, giving us

```
		1
  01010101
+ 00001111
----------
		 0
```

The two 1s add together to give a **sum** of 0 and **carry** of 1. We then repeat these steps again for "twos" place

```
	   11
  01010101
+ 00001111
----------
		00
```
Resulting in another **sum**=0 and **carry**=1. But notice how this addition involved the carry from the previous addition, totaling to 3 inputs (full adder?) while the first addition only needed 2 inputs (half adder?). 

It follows that every addition other than the first can be done with full adders, and these additions can be computed sequentially.

![[adder_impl.png]]

#### How long will it take? 
This setup allows us to add two 8-bit numbers, and we could extend this (1 half adder, n-1 full adders) strategy to add any n-bit numbers. However, one jarring flaw would be that the result would have to be generated **sequentially**. i.e. sum1 has to wait for carry0, sum2 for carry1, and so forth.

What does *waiting* for the previous carry entail? 

In reality, voltage are the inputs to these circuits. For example, the 0 and 1 input values above could be 0V and 1.2V respectively.

To give a rough idea about how modern circuits work: [CMOS](https://en.wikipedia.org/wiki/CMOS)

When voltage is applied, imagine water flowing through a narrow pipe into a water tank. When this water tank is filled sufficiently, a switch is flipped, and the output of the gate will change. Similarly, when no charge is applied, water flows out of the tank through the narrow pipe. When the water level is sufficiently low, the switch flips and the output changes again.

Water Tank: Capacitor
Water Flow: Electrical Charge
Narrow Pipe: Resistance in the circuit
"Sufficiently filled": Logic Threshold Voltage

Switching the output of one CMOS gate may take up to 120 picoseconds. One Full Adder that consists of 2 Half Adders and 1 OR gate requires 360 picoseconds.

Our 8-bit Adder needs: 
120 + 360 x 7 = 2640 picoseconds = 2.64 nanoseconds
