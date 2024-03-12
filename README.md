# Apollo Guidance Computer (AGC) Block III

1. [Peripheral Features](#1-peripheral-features)
2. [General Description](#2-general-description)
3. [Architectural Overview](#3-architectural-overview)
    1. [Registers](#3.1-registers)
        1. [Common registers](#3.1.1-common-registers)
        2. [Hidden registers](#3.1.2-hidden-registers)
            1. [Memory registers](#3.1.2.1-memory-registers)
            2. [Return Address registers](#3.1.2.2-return-address-registers)
            3. [One complement registers](#3.1.2.3-one-complement-registers)
            4. [Adder registers](#3.1.2.4-adder-registers)
            5. [CoreSet registers](#3.1.2.5-coreset-registers)
            6. [Data registers](#3.1.2.6-data-registers)
            7. [Instruction registers](#3.1.2.7-instruction-registers)
4. [Memory Organization](#4-memory-organization)
    1. [Fixed Memory Organization](#4.1-fixed-memory-organization)
    2. [Erasable Memory Organization](#4.2-erasable-memory-organization)
        1. [Special Function erasable](#4.2.1-special-function-erasable)
            1. [VAC area](#4.2.1.1-vac-area)
            2. [Special function](#4.2.1.2-special-function)
            3. [CoreSet](#4.2.1.3-coreset)
5. [I/O Channels](#5-io-channels)
6. [Instruction Set Sumarry](#8-instruction-set-sumarry)
    1. [Native-Mode Instructions](#8.1-native-mode-instructions)
        1. [Data handling operations that use Erasable addresses for direct or indirect](#8.1.1-data-handling-operations-that-use-erasable-addresses-for-direct-or-indirect)
            1. [Load instructions](#8.1.1.1-load-instructions)
            2. [Store instructions](#8.1.1.2-store-instructions)
            3. [Exchange instructions](#8.1.1.3-exchange-instructions)
            4. [Test & Compare instructions](#8.1.1.4-test--compare-instructions)
            5. [Add & Subtract instructions](#8.1.1.5-add--subtract-instructions)
            6. [Multiply & Devide instructions](#8.1.1.6-multiply--devide-instructions)
            7. [Add to Memory instructions](#8.1.1.7-add-to-memory-instructions)
            6. [Increment & Reduce instructions](#8.1.1.8-increment--reduce-instructions)
            9. [Misc. instructions](#8.1.1.9-misc-instructions)
        2. [Data handling operations that use bits 12-01 for special purposes](#8.1.2-data-handling-operations-that-use-bits-12-01-for-special-purposes)  
            1. [Shift Instructions](#8.1.2.1-shift-instructions)
            2. [Input & Output Instruction](#8.1.2.2-input--output-instruction)
            3. [No-address ops implemented in hardware](#8.1.2.3-no-address-ops-implemented-in-hardware)
                1. [Clear register instructions](#8.1.2.3.1-clear-register-instructions)
                2. [Absolute value of register instructions](#8.1.2.3.2-absolute-value-of-register-instructions)
                3. [Complement register instructions](#8.1.2.3.3-complement-register-instructions)
                4. [Index register instructions](#8.1.2.3.4-index-register-instructions)
                5. [Negate register instructions](#8.1.2.3.5-negate-register-instructions)
                6. [Round register instructions](#8.1.2.3.6-round-register-instructions)
                7. [Normalize register instructions](#8.1.2.3.7-normalize-register-instructions)
                8. [Test register instructions](#8.1.2.3.8-test-register-instructions)
                9. [Test register overflow instructions](#8.1.2.3.9-test-register-overflow-instructions)
                10. [Test Angle](#8.1.2.3.10-test-angle)
                11. [Misc. Unsigned value instructions](#8.1.2.3.11-misc-unsigned-value-instructions)
                12. [Misc. Signed value instructions](#8.1.2.3.12-misc-signed-value-instructions)
                13. [Register transfer instruction](#8.1.2.3.13-register-transfer-instruction)
                14. [Block transfer instructions](#8.1.2.3.14-block-transfer-instructions)
                15. [Control instructions](#8.1.2.3.15-control-instructions)
                16. [Condition code instructions](#8.1.2.3.16-condition-code-instructions)
            4. [No-address ops implemented in software](#8.1.2.4-no-address-ops-implemented-in-software)
        3. [Immediate data and transfers of control](#8.1.3-immediate-data-and-transfers-of-control)
            1. [DATA= derivated pseudo-instructions](#8.1.3.1-data-derivated-pseudo-instructions)
            2. [DATAA derivated pseudo-instructions](#8.1.3.2-dataa-derivated-pseudo-instructions)
            3. [DATA= & DATAA derivated pseudo-instructions](#8.1.3.3-data-&-dataa-derivated-pseudo-instructions)
            4. [IF derivated pseudo-instructions](#8.1.3.4-if-derivated-pseudo-instructions)
    2. [Interpretive Instructions](#8.2-interpretive-instructions)
    3. [Pseudo instruction](#8.3-pseudo-instruction)
    1. [Appendix A: Instruction Description](#appendix-a-instruction-description)
    1. [Appendix B: Pulse Sequences](#appendix-b-pulse-sequences)
    1. [Appendix C: Pulse Description](#appendix-c-pulse-description)

## Naming convention

| Apollo Era                    | Modern Era                                    |
| -                             | -                                             |
| Fixed Memory                  | Read Only Memory (ROM)                        |
| Erasable Memory               | Random Access Memory (RAM)                    |
| Central Store                 | Central Processing Unit (CPU) **registers**   |
| Control Pulse Matrix (CPM)    | Diode Matrix<br />CPU **Microcode**           |
| IO Channels                   | IO Ports                                      |

Sequence Generator
Control Pulses

## 1. Peripheral Features

* 6 Timers
    
* Optics subsystem orientation (OPT)
* Receiver (DOWNLINK) & Transmitter (UPLINK)
* 3 axis Rotational Hand controller (RHC) Pitch, Yaw, Roll
* Inertial Measurement Unit (IMU)
    * Pulsed Integrating Pendulous Accelerometer (PIPA)
    * Control Data Unit (CDU)
    * Coarse alignment to drive stable platform
    * Fine alignment to torque the gyro to the the precise alignment
* Reaction Control System (RCS), Pitch, Roll
* Display Keyboard (DSKY)

## 2. General Description

| Memory Type       | Count x Size          | Structure (bit) | 
| -                 | -                     | -               |
| Fixed Memory      | 32K Words x 20 bits   | 19 + 1 parity   |
| Erasable Memory   |  4K Words x 16 bits   | 15 + 1 parity   |

## 3. Architectural Overview

### 3.1. Registers

#### 3.1.1. Common registers

| Name  | Description       | Size      | 
|-      | -                 | -         |
| A     | Accumulator       | 16 bits   |
| L     | Lower Accumulator | 16 bits   |
| AL    | A and L together  | 32 bits   |
| Q     | Quick-turnover    | 16 bits   |

**A** (16 bits)
* Accumulator for single precision
* Upper half of double precision

**L** (16 bits)
* Lower accumulator for double precision
* Lower half of product (**MP** and **MPAD**)
* Lower half of dividend, then Remainder (**DV**) 
* Mostly unsigned, has rounding bit

**Q** (16 bits)
* Quick-turnover register for short-term data
* Word counter in copy-blocks

#### 3.1.2. Hidden registers

##### 3.1.2.1. Memory registers

| Name  | Description           | Size      | 
|-      | -                     |-          |
| E     | Erasable              | 16 bits   |
| SE    | Select Erasable       | 12 bits   |
| F     | Fixed                 | 20 bits   |
| SF    | Select Fixed          | 15 bits   |

**E** (16 bits)
* Memory local register for *Erasable Memory*;
* Parity processing both directions

**SE** (12 bits )
* Select Erasable: holds address to drive *Erasable Memory*

**F** (20 bits)
* Memory local register for *Fixed Memory*
* Parity check when filled from memory

**SF** (15 bits) 
* Select Fixed: holds address to drive *Fixed Memory*
* Incremented content readable

##### 3.1.2.2. Return Address registers

| Name  | Description                   | Size      | 
|-      | -                             | -         |
| RA    | Return Address                | 15 bits   |
| RAPO  | Return-Address POP pointer    | 5 bits    |
| RAPU  | Return-Address POP pointer    | 5 bits    |

**RA** (15 bits)
* Latest Return Address, for instant return from subroutine

**RAPO** (5 bits)
* Return-Address POP pointer

**RAPU** (5 bits)
* Return-Address POP pointer

##### 3.1.2.3. One complement registers

| Name  | Description           | Size      | 
|-      | -                     |-          |
| B     | Internal Buffer       | 16 bits   |
| C     | One complement of B   | 16 bits   |

**B** (16 bits)
* Buffer for multiple internal purposes

**C** (16 bits)
* Not a distinct register: Just the ones-complement outputs of B

##### 3.1.2.4. Adder registers

| Name  | Description   | Size      |  
|-      | -             |-          |
| X     | Augend        | 16 bits   |
| Y     | Addend        | 16 bits   |
| U     | Adder output  | 17 bits   |
| CO    | Carry out     | 1 bit     |

**X** (16 bits)
* First latch for augend as input to adder

**Y** (16 bits)
* Second latch for addend as input to adder

**U** (17 bits)
* Not a distinct register: just the adder output
* 17<sup>th</sup> bit is **CO**

**CO** (1 bit)
* Not a distinct register: 17<sup>th</sup> bit of **U**
* Carry out from 16-bit adder sum

##### 3.1.2.5. CoreSet registers

| Name  | Description           | Size      |  
|-      | -                     |-          |
| CS    | CoreSets index        | 4 bits    |
| V     | VAC index             | 3 bits    |

**CS** (4 bits)
* Select one of 16 CoreSets as active
* Qddressed as a channel

**V** (3 bits)
* Select one of 8 VAC areas as active
* Adressed as a channel

##### 3.1.2.6. Data registers

| Name  | Description           | Size      |  
|-      | -                     |-          |
| D     | Data holder           | 15 bits   |
| DACT  | Data folder flag      | 1 bit     |
| DEXD  | DEXD flag             | 1 bit     |

**D** (15 bits)
* Short-term register for immediate data and other internal purposes

**DACT** (1 bit)
* Discrete to signal that D has been set up but not yet used

**DEXD** (1 bit)
* Discrete to signal that D is specially active for a DEXEC sequence

##### 3.1.2.7. Instruction registers

| Name  | Description           | Size      |  
|-      | -                     |-          |
| IR    | Interrupt flags       | 14 bits   |
| Z     | Instruction Address   | 15 bits   |
| SQ    | Instruction Sequence  | 19 bits   |

**IR** (15 bits)
* Interrupt Requests, a prioritized array of discretes;
* Addressed as a channel

**Z** (15 bits)
* Instruction location counter, *Fixed Memory* address of (usually) next instruction

**SQ** (19 bits)
* Instruction to execute

| Name  | Description           | Size      |  
|-      | -                     |-          |
| SC    | Channel Address       | 9 bits    |

**SC** (9 bits)
* Select Channel: holds address to identify active channel [no local register req’d]

## 4. Memory Organization

6µs Memory Cycle Time (MCT) 

### 4.1. Fixed Memory Organization

32K Word of 20 bits
* `F..F` is 19 data bits
* `P` is 1 parity bit
* addressed as 00000-77777 octal (15 bits)

```
MSB                                                  LSB
19 18 17 16 15 14 13 12 11 10  9  8  7  6  4  3  2  1  0
 F  F  F  F  F  F  F  F  F  F  F  F  F  F  F  F  F  F  P
```

### 4.2. Erasable Memory Organization

4K Word of 16 bits
* `E..E` is 15 data bits
* `P` is 1 parity bit
* addressed as 0000-7777 octal (12 bits)

```
MSB                                      LSB
15 14 13 12 11 10  9  8  7  6  4  3  2  1  0
 E  E  E  E  E  E  E  E  E  E  E  E  E  E  P
```

| Start (Octal) | End (Octal)   | Size                  | Function          |
| -             | -             | -                     | -                 |
| 0000          | 0777          | 512 Words (8 x 64)    | VAC area          |
| 1000          | ?             | ?                     | Special Function  | 
| ?             | 7337          | 3072 - ? Words        | General Usage     | 
| 7400          | 7777          | 256 Words (16 x 16)   | CoreSets          |


##### 4.2.1. Special Function erasable

###### 4.2.1.1. VAC area

Maintain the up to 16 job’s state
* 8 VAC areas of 64 Words
* Erasable Memory location 0000-0777
* 3 bits **V** register selects the active VAC area


| Start (Octal) | End (Octal)   | Size      | **V** register    |
| -             | -             | -         | -                 |
| 0000          | 0077          | 64 Words  | 0                 |
| 0100          | 0177          | 64 Words  | 1                 |
| 0200          | 0377          | 64 Words  | 2                 |
| 0300          | 0377          | 64 Words  | 3                 |
| 0400          | 0477          | 64 Words  | 4                 |
| 0500          | 0577          | 64 Words  | 5                 |
| 0600          | 0677          | 64 Words  | 6                 |
| 0700          | 0777          | 64 Words  | 7                 |

###### 4.2.1.2. Special function

| Address (Octal)   | Function  | Comment   |
| -                 | -         | -         |
| ????              | SAMPTIME  |           |
| ????              | TIME2     |           |
| ????              | TIME1     |           |
| ????              | TIME3     |           |
| ????              | TIME4     |           |
| ????              | TIME5     |           |
| ????              | TIME6     |           |
| ????              | CDUX      | IMU, Control Data Unit, inner gimbal angle          |
| ????              | CDUY      | IMU, Control Data Unit, middle gimbal angle         |
| ????              | CDUZ      | IMU, Control Data Unit, outer gimbal angle          |
| ????              | OPTY      | Optics subsystem orientation, trunnion angle          |
| ????              | OPTX      | Optics subsystem orientation, shaft angle          |
| ????              | PIPAX     | IMU, Pulsed Integrating Pendulous Accelerometer, X axis, 5.85cm/s (or fine is 1cm/s)          |
| ????              | PIPAY     | IMU, Pulsed Integrating Pendulous Accelerometer, Y axis, 5.85cm/s (or fine is 1cm/s)          |
| ????              | PIPAZ     | IMU, Pulsed Integrating Pendulous Accelerometer, X axis, 5.85cm/s (or fine is 1cm/s)          |
| ????              | Q-RHCCTR  | Rotational Hand Controller, Pitch            |
| ????              | P-RHCCTR  | Rotational Hand Controller, Yaw          |
| ????              | R-RHCCTR  | Rotational Hand Controller, Roll          |
| ????              | INLINK    | Receive digital uplink data from a ground station |
| ????              | RNRAD     |           |
| ????              | GYROCTR   | IMU, fine alignment to torque the gyro to the the precise alignment          |
| ????              | CDUXCMD   | IMU, coarse alignment to drive the IMU stable platform           |
| ????              | CDUYCMD   | IMU, coarse alignment to drive the IMU stable platform          |
| ????              | CDUZCMD   | IMU, coarse alignment to drive the IMU stable platform          |
| ????              | CDUXCMD   |           |
| ????              | OPTYCMD   |           |
| ????              | THRUST    |           |
| ????              | LEMONM    |           |
| ????              | OUTLINK   | Transmit digital downlink data from a ground station |
| ????              | ALTM      |           |

> **Bruno Muller**:  
> TODO, Check RUPT and register,  

###### 4.2.1.3. CoreSet

Maintain the up to 16 job’s state
* 16 CoreSets of 16 Words
* Erasable Memory location 7400-7777 
* 4 bits **CS** register selects the active CoreSet
* CoreSet 17 octal permanently assigned to Idle job

| Start (Octal) | End (Octal)   | Size      | **CS** register   | Comment                                               |
| -             | -             | -         | -                 | -                                                     |
| 7400          | 7417          | 16 Words  | 00                | Address range is always mapped to **Active CoreSet**  |
| 7420          | 7437          | 16 Words  | 01                | Memory is directly addressable                        |
| 7440          | 7457          | 16 Words  | 02                | Memory is directly addressable                        |
| 7460          | 7477          | 16 Words  | 03                | Memory is directly addressable                        |
| 7500          | 7517          | 16 Words  | 04                | Memory is directly addressable                        |
| 7520          | 7537          | 16 Words  | 05                | Memory is directly addressable                        |
| 7540          | 7557          | 16 Words  | 06                | Memory is directly addressable                        |
| 7560          | 7577          | 16 Words  | 07                | Memory is directly addressable                        |
| 7600          | 7617          | 16 Words  | 10                | Memory is directly addressable                        |
| 7620          | 7637          | 16 Words  | 11                | Memory is directly addressable                        |
| 7640          | 7657          | 16 Words  | 12                | Memory is directly addressable                        |
| 7660          | 7677          | 16 Words  | 13                | Memory is directly addressable                        |
| 7700          | 7717          | 16 Words  | 14                | Memory is directly addressable                        |
| 7720          | 7737          | 16 Words  | 15                | Memory is directly addressable                        |
| 7740          | 7757          | 16 Words  | 16                | Memory is directly addressable                        |
| 7760          | 7777          | 16 Words  | 17                | Direct addressing is possible, reserved to **Idle job**   |

> **Bruno Muller**:  
> Open question,  
> As 7400-7417 has no direct access, it could be interessting to map the **Idle Job** to **CoreSet 0** instead of **CoreSet 17**

## 5. I/O Channels

| Address (Octal)   | Usage                     |
| -                 | -                         |
| 000               | Mapped to **IR** register |
| 001               | Mapped to **L** register  |
| 002               | Mapped to **Q** register  |
| 003               | Mapped to **CS** register |
| 004               | HISCALAR                  |
| 005               | LOSCALAR                  |
| 006               | PYJETS, RCS Pitch control |
| 007               | ROLLJETS, RCS Roll control|
| 010               | DSKY, 7 segments display  |
| 011               | DSKY, alarm lights        |
| 012               | Misc, vessel control      |
| 013               | DSKY, lights              |
| 014               | Gyro, OCDU, IMU CDU       |
| 015               | DSKY, Keyboard            |
| ...               |                           |
| 032               | DSKY, PRO key             |

## 6. Timers

...

## 7. CPU

### 7.1. Reset

...

### 7.2. Interrupts

...

## 8. Instruction Set Sumarry

### 8.1. Native-Mode Instructions

#### 8.1.1. Data handling operations that use Erasable addresses for direct or indirect (*)

``` 
Op Code   19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01
(gen’l)    0  C  C  C  C  C Ci  E  E  E  E  E  E  E  E  E  E  E  E
0 0 C AAA  LDA(*) & LDL(*)  & DLD(*) & LDQ(*)
0 1 C AAA  STA(*) & STL(*)  & DST(*) & STQ(*)
0 2 C AAA  XCA(*) & XCL(*)  & DXC(*) & XCQ(*)
0 3 C AAA  T(*)   & DT(*)   & C(*)   & DC(*)
0 4 C AAA  AD(*)  & DAD(*)  & SB(*)  & DSB(*)
0 5 C AAA  MP(*)  & MASK(*) & DV(*)  & MPAD(*)
0 6 C AAA  ADM(*) & DADM(*) & INC(*) & RED(*)
0 7 C AAA  NDX(*) & STD(*)  & STZ    & PRET*
``` 

##### 8.1.1.1. Load instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `LDA  e`              | Load A                            | ?         | `000 eeee`                | ?                |
| `LDA* e`              | Load A Indirect                   | ?         | `001 eeee`                | ?                |
| `LDL  e`              | Load L                            | ?         | `002 eeee`                | ?                |
| `LDL* e`              | Load L Indirect                   | ?         | `003 eeee`                | ?                |
| `DLD  e`              | Load Double Precision             | ?         | `004 eeee`                | ?                |
| `DLD* e`              | Load Double Precision Indirect    | ?         | `005 eeee`                | ?                |
| `LDQ  e`              | Load Q                            | ?         | `006 eeee`                | ?                |
| `LDQ* e`              | Load Q Indirect                   | ?         | `007 eeee`                | ?                |

##### 8.1.1.2. Store instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `STA  e`              | Store A                           | ?         | `010 eeee`                | ?                |
| `STA* e`              | Store A Indirect                  | ?         | `011 eeee`                | ?                |
| `STL  e`              | Store L                           | ?         | `012 eeee`                | ?                |
| `STL* e`              | Store L Indirect                  | ?         | `013 eeee`                | ?                |
| `DST  e`              | Store Double Precision            | ?         | `014 eeee`                | ?                |
| `DST* e`              | Store Double Precision Indirect   | ?         | `015 eeee`                | ?                |
| `STQ  e`              | Store Q                           | ?         | `016 eeee`                | ?                |
| `STQ* e`              | Store Q Indirect                  | ?         | `017 eeee`                | ?                |

##### 8.1.1.3. Exchange instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `XCA  e`              | Exchange A                        | ?         | `020 eeee`                | ?                |
| `XCA* e`              | Exchange A Indirect               | ?         | `021 eeee`                | ?                |
| `XCL  e`              | Exchange L                        | ?         | `022 eeee`                | ?                |
| `XCL* e`              | Exchange L Indirect               | ?         | `023 eeee`                | ?                |
| `DXC  e`              | Exchange Double Precision         | ?         | `024 eeee`                | ?                |
| `DXC* e`              | Exchange Double Precision Indirect| ?         | `025 eeee`                | ?                |
| `XCQ  e`              | Exchange Q                        | ?         | `026 eeee`                | ?                |
| `XCQ* e`              | Exchange Q Indirect               | ?         | `027 eeee`                | ?                |

##### 8.1.1.4. Test & Compare instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `T   e`               | Test Single Precision             | ?         | `030 eeee`                | ?                |
| `T*  e`               | Test Single Precision Indirect    | ?         | `031 eeee`                | ?                |
| `DT  e`               | Test Double Precision             | ?         | `032 eeee`                | ?                |
| `DT* e`               | Test Double Precision Indirect    | ?         | `033 eeee`                | ?                |
| `C   e`               | Compare Single Precision          | ?         | `034 eeee`                | ?                |
| `C*  e`               | Compare Single Precision Indirect | ?         | `035 eeee`                | ?                |
| `DC  e`               | Compare Double Precision          | ?         | `036 eeee`                | ?                |
| `DC* e`               | Compare Double Precision Indirect | ?         | `037 eeee`                | ?                |

##### 8.1.1.5. Add & Subtract instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `AD   e`              | Add Single Precision              | ?         | `040 eeee`                | ?                |
| `AD*  e`              | Add Single Precision Indirect     | ?         | `041 eeee`                | ?                |
| `DAD  e`              | Add Double Precision              | ?         | `042 eeee`                | ?                |
| `DAD* e`              | Add Double Precision Indirect     | ?         | `043 eeee`                | ?                |
| `SB   e`              | Subtract Single Precision         | ?         | `044 eeee`                | ?                |
| `SB*  e`              | Subtract Single Precision Indirect| ?         | `045 eeee`                | ?                |
| `DSB  e`              | Subtract Double Precision         | ?         | `046 eeee`                | ?                |
| `DSB* e`              | Subtract Double Precision Indirect| ?         | `047 eeee`                | ?                |

##### 8.1.1.6. Multiply & Devide instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `MP    e`             | Multiply                          | ?         | `050 eeee`                | ?                |
| `MP*   e`             | Multiply Indirect                 | ?         | `051 eeee`                | ?                |
| `MASK  e`             | Logical Multiply (boolean AND)    | ?         | `052 eeee`                | ?                |
| `MASK* e`             | Logical Multiply (boolean AND) Indirect| ?    | `053 eeee`                | ?                |
| `DV    e`             | Divide                            | ?         | `054 eeee`                | ?                |
| `DV*   e`             | Divide Indirect                   | ?         | `055 eeee`                | ?                |
| `MPAD  e`             | Multiply and And                  | ?         | `056 eeee`                | ?                |
| `MPAD* e`             | Multiply and And Indirect         | ?         | `057 eeee`                | ?                |

##### 8.1.1.7. Add to Memory instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `ADM   e`             | Add to Memory                     | ?         | `060 eeee`                | ?                |
| `ADM*  e`             | Add to Memory Indirect            | ?         | `061 eeee`                | ?                |
| `DADM  e`             | Add to Memory Double Precision    | ?         | `062 eeee`                | ?                |
| `DADM* e`             | Add to Memory Double Precision Indirect| ?    | `063 eeee`                | ?                |

##### 8.1.1.8. Increment & Reduce instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `INC  e`              | Increment                         | ?         | `064 eeee`                | ?                |
| `INC* e`              | Increment Indirect                | ?         | `065 eeee`                | ?                |
| `RED  e`              | Reduce and And                    | ?         | `066 eeee`                | ?                |
| `RED* e`              | Reduce and And Indirect           | ?         | `067 eeee`                | ?                |

##### 8.1.1.9. Misc. instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `NDX  e`              | Index                             | ?         | `070 eeee`                | ?                |
| `NDX* e`              | Index Indirect                    | ?         | `071 eeee`                | ?                |
| `STD STZ e`           | Store                             | ?         | `072 eeee`                | ?                |
| `PRET* e`             | Interpretive Program Fetching     | ?         | `073 eeee`                | ?                |

#### 8.1.2. Data handling operations that use bits 12-01 for special purposes

``` 
Op Code    19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01
(spc’l)     0  C  C  C  C  C  C  X  X  X  X  X  X  X  X  X  X  X  X
0 74 XXXX   shifts of several types
0 75 XXXX   input/output using bits 09-01 as a channel address
0 76 XXXX   no-address ops implemented in hardware
0 77 XXXX   no-address ops implemented in software
``` 

##### 8.1.2.1 Shift Instructions

* `d` is shift count
* `DDD` is negative `ddd`

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `SAL d`               | Shift signed A left               | ?         | `0740 ddd`                | ?                |
| `SAR d`               | Shift signed A Right              | ?         | `0740 DDD`                | ?                |
| `SALU d`              | Shift usigned A left              | ?         | `0741 ddd`                | ?                |
| `SARU d`              | Shift usigned A Right             | ?         | `0741 DDD`                | ?                |
| `CYCAL d`             | Cycle usigned A left              | ?         | `0742 ddd`                | ?                |
| `CYCAR d`             | Cycle usigned A Right             | ?         | `0742 DDD`                | ?                |
| `SLL d`               | Shift usigned L left              | ?         | `0743 ddd`                | ?                |
| `SLR d`               | Shift usigned L Right             | ?         | `0743 DDD`                | ?                |
| `DSL d`               | Shift usigned L left              | ?         | `0744 ddd`                | ?                |
| `DSR d`               | Shift usigned L Right             | ?         | `0744 DDD`                | ?                |
| `DSLU d`              | Shift Double Precision left       | ?         | `0745 ddd`                | ?                |
| `DSRU d`              | Shift Double Precision Right      | ?         | `0745 DDD`                | ?                |
| `DCYCL d`             | Cycle Double Precision left       | ?         | `0746 ddd`                | ?                |
| `DCYCR d`             | Cycle Double Precision left       | ?         | `0746 DDD`                | ?                |
| `SQL d`               | Shift signed Q left               | ?         | `0747 ddd`                | ?                |
| `SQR d`               | Shift signed Q Right              | ?         | `0747 DDD`                | ?                |

##### 8.1.2.2. Input & Output Instruction

* `c` is channel address

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `READ c`              | Read                              | ?         | `0750 ccc`                | ?                |
| `ROR c`               | Read                              | ?         | `0751 ccc`                | ?                |
| `RAND c`              | Read                              | ?         | `0752 ccc`                | ?                |
| `RXOR c`              | Read                              | ?         | `0753 ccc`                | ?                |
| `WRITE c`             | Write                             | ?         | `0754 ccc`                | ?                |
| `WOR c`               | Write                             | ?         | `0755 ccc`                | ?                |
| `WAND c`              | Write                             | ?         | `0756 ccc`                | ?                |
| `WXOR c`              | Write                             | ?         | `0757 ccc`                | ?                |

##### 8.1.2.3. No-address ops implemented in hardware

``` 
Op Code 19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01
(spc’l)  0  1  1  1  1  1  0  x  x  x  x  x  P  P  P  P  R  R  R
0 76 x'00   Clear register
0 76 x'01   Absolute value of register
0 76 x'02   Complement register
0 76 x'03   Index register
0 76 x'04   Negate register
0 76 x'05   Round register
0 76 x'06   Normalize register
0 76 x'07   Test register
0 76 x'10   Test register overflow
0 76 x'11   Test Angle
0 76 x'12   Misc. Unsigned value
0 76 x'13   Misc. Signed value
0 76 x'14   Misc. Signed value
0 76 x'15   Register transfer
0 76 x'16   Block transfer
0 76 x'17   Control & Condition code 
``` 

###### 8.1.2.3.1. Clear register instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `ZA`                  | Zero A                            | ?         | `076x000`                 | ?                |
| `ZL`                  | Zero L                            | ?         | `076x001`                 | ?                |
| `ZQ`                  | Zero Q                            | ?         | `076x002`                 | ?                |
|                       | Reserved                          | ?         | `076x003`                 | ?                |
| `ZL21`                | Zero bits 2 and 1 of L            | ?         | `076x004`                 | ?                |
|                       | Reserved                          | ?         | `076x005`                 | ?                |
|                       | Reserved                          | ?         | `076x006`                 | ?                |
|                       | Reserved                          | ?         | `076x007`                 | ?                |

###### 8.1.2.3.2. Absolute value of register instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `ABA`                 | ABsolute value of b(A) to A       | ?         | `076x010`                 | ?                |
|                       | Reserved                          | ?         | `076x011`                 | ?                |
| `ABQ`                 | ABsolute value of b(Q) to Q       | ?         | `076x012`                 | ?                |
| `DAB`                 | ABsolute value in b(AL) to AL     | ?         | `076x013`                 | ?                |
|                       | Reserved                          | ?         | `076x014`                 | ?                |
|                       | Reserved                          | ?         | `076x015`                 | ?                |
|                       | Reserved                          | ?         | `076x016`                 | ?                |
|                       | Reserved                          | ?         | `076x017`                 | ?                |

###### 8.1.2.3.3. Complement register instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `COMA`                | Complement A                      | ?         | `076x020`                 | ?                |
| `COML`                | Complement L                      | ?         | `076x021`                 | ?                |
| `COMQ`                | Complement Q                      | ?         | `076x022`                 | ?                |
|                       | Reserved                          | ?         | `076x023`                 | ?                |
|                       | Reserved                          | ?         | `076x024`                 | ?                |
|                       | Reserved                          | ?         | `076x025`                 | ?                |
|                       | Reserved                          | ?         | `076x026`                 | ?                |
|                       | Reserved                          | ?         | `076x027`                 | ?                |

###### 8.1.2.3.4. Index register instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `IXA`                 | NDX A                             | ?         | `076x030`                 | ?                |
| `IXL`                 | NDX L                             | ?         | `076x031`                 | ?                |
| `IXQ`                 | NDX Q                             | ?         | `076x032`                 | ?                |
|                       | Reserved                          | ?         | `076x033`                 | ?                |
|                       | Reserved                          | ?         | `076x034`                 | ?                |
|                       | Reserved                          | ?         | `076x035`                 | ?                |
|                       | Reserved                          | ?         | `076x036`                 | ?                |
|                       | Reserved                          | ?         | `076x037`                 | ?                |

###### 8.1.2.3.5. Negate register instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `NA`                  | Negate c(A)                       | ?         | `076x040`                 | ?                |
|                       | Reserved                          | ?         | `076x041`                 | ?                |
| `NQ`                  | Negate c(Q)                       | ?         | `076x042`                 | ?                |
| `DN`                  | Double precision Negate c(AL)     | ?         | `076x043`                 | ?                |
|                       | Reserved                          | ?         | `076x044`                 | ?                |
|                       | Reserved                          | ?         | `076x045`                 | ?                |
|                       | Reserved                          | ?         | `076x046`                 | ?                |
|                       | Reserved                          | ?         | `076x047`                 | ?                |

###### 8.1.2.3.6. Round register instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `R`                   | Round c(A)                        | ?         | `076x050`                 | ?                |
|                       | Reserved                          | ?         | `076x051`                 | ?                |
|                       | Reserved                          | ?         | `076x052`                 | ?                |
| `DR`                  | Double precision Round c(AL)      | ?         | `076x053`                 | ?                |
|                       | Reserved                          | ?         | `076x054`                 | ?                |
|                       | Reserved                          | ?         | `076x055`                 | ?                |
|                       | Reserved                          | ?         | `076x056`                 | ?                |
|                       | Reserved                          | ?         | `076x057`                 | ?                |

###### 8.1.2.3.7. Normalize register instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `NORM`                | NORMalize c(A)                    | ?         | `076x060`                 | ?                |
|                       | Reserved                          | ?         | `076x061`                 | ?                |
|                       | Reserved                          | ?         | `076x062`                 | ?                |
| `DNORM`               | Double precision NORMalize c(AL)  | ?         | `076x063`                 | ?                |
|                       | Reserved                          | ?         | `076x064`                 | ?                |
|                       | Reserved                          | ?         | `076x065`                 | ?                |
|                       | Reserved                          | ?         | `076x066`                 | ?                |
|                       | Reserved                          | ?         | `076x067`                 | ?                |

###### 8.1.2.3.8. Test register instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `TA`                  | Test A                            | ?         | `076x070`                 | ?                |
| `TL`                  | Test L                            | ?         | `076x071`                 | ?                |
| `TQ`                  | Test Q                            | ?         | `076x072`                 | ?                |
|                       | Reserved                          | ?         | `076x073`                 | ?                |
|                       | Reserved                          | ?         | `076x074`                 | ?                |
|                       | Reserved                          | ?         | `076x075`                 | ?                |
|                       | Reserved                          | ?         | `076x076`                 | ?                |
|                       | Reserved                          | ?         | `076x077`                 | ?                |

###### 8.1.2.3.9. Test register overflow instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `TOVA`                | Test for Overflow in A            | ?         | `076x100`                 | ?                |
|                       | Reserved                          | ?         | `076x101`                 | ?                |
| `TOVQ`                | Test for Overflow in Q            | ?         | `076x102`                 | ?                |
|                       | Reserved                          | ?         | `076x103`                 | ?                |
|                       | Reserved                          | ?         | `076x104`                 | ?                |
|                       | Reserved                          | ?         | `076x105`                 | ?                |
|                       | Reserved                          | ?         | `076x106`                 | ?                |
|                       | Reserved                          | ?         | `076x107`                 | ?                |

###### 8.1.2.3.10. Test Angle

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
|                       | Reserved                          | ?         | `076x110`                 | ?                |
| `TANGL`               | Test ANGle in L, comparing vs. pi | ?         | `076x111`                 | ?                |
|                       | Reserved                          | ?         | `076x112`                 | ?                |
|                       | Reserved                          | ?         | `076x113`                 | ?                |
|                       | Reserved                          | ?         | `076x114`                 | ?                |
|                       | Reserved                          | ?         | `076x115`                 | ?                |
|                       | Reserved                          | ?         | `076x116`                 | ?                |
|                       | Reserved                          | ?         | `076x117`                 | ?                |

###### 8.1.2.3.11. Misc. Unsigned value instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `CAU`                 | Convert b(A) to unsigned          | ?         | `076x120`                 | ?                |
| `PASU`                | Prepare A for Storage as Unsigned | ?         | `076x121`                 | ?                |
|                       | Reserved                          | ?         | `076x122`                 | ?                |
|                       | Reserved                          | ?         | `076x123`                 | ?                |
|                       | Reserved                          | ?         | `076x124`                 | ?                |
|                       | Reserved                          | ?         | `076x125`                 | ?                |
|                       | Reserved                          | ?         | `076x126`                 | ?                |
|                       | Reserved                          | ?         | `076x127`                 | ?                |

###### 8.1.2.3.12. Misc. Signed value instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `LASN`                | L to A as Signed Number           | ?         | `076x130`                 | ?                |
|                       | Reserved                          | ?         | `076x131`                 | ?                |
|                       | Reserved                          | ?         | `076x132`                 | ?                |
|                       | Reserved                          | ?         | `076x133`                 | ?                |
|                       | Reserved                          | ?         | `076x134`                 | ?                |
|                       | Reserved                          | ?         | `076x135`                 | ?                |
|                       | Reserved                          | ?         | `076x136`                 | ?                |
|                       | Reserved                          | ?         | `076x137`                 | ?                |

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `SINCOS`              | Sine and Cos of b(A)              | ?         | `076x140`                 | ?                |
| `SQRT`                | SQuare RooT of AL                 | ?         | `076x141`                 | ?                |
|                       | Reserved                          | ?         | `076x142`                 | ?                |
|                       | Reserved                          | ?         | `076x143`                 | ?                |
|                       | Reserved                          | ?         | `076x144`                 | ?                |
|                       | Reserved                          | ?         | `076x145`                 | ?                |
|                       | Reserved                          | ?         | `076x146`                 | ?                |
|                       | Reserved                          | ?         | `076x147`                 | ?                |

###### 8.1.2.3.13. Register transfer instruction

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `A2Q`                 | Copy A to Q                       | ?         | `076x150`                 | ?                |
|                       | Reserved                          | ?         | `076x151`                 | ?                |
| `Q2A`                 | Copy Q to A                       | ?         | `076x152`                 | ?                |
|                       | Reserved                          | ?         | `076x153`                 | ?                |
|                       | Reserved                          | ?         | `076x154`                 | ?                |
|                       | Reserved                          | ?         | `076x155`                 | ?                |
|                       | Reserved                          | ?         | `076x156`                 | ?                |
|                       | Reserved                          | ?         | `076x157`                 | ?                |

###### 8.1.2.3.14. Block transfer instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
|                       | Reserved                          | ?         | `076x160`                 | ?                |
|                       | Reserved                          | ?         | `076x161`                 | ?                |
|                       | Reserved                          | ?         | `076x162`                 | ?                |
|                       | Reserved                          | ?         | `076x163`                 | ?                |
| `XCEB`                | Exchange Erasable Blocks          | ?         | `076x164`                 | ?                |
| `CPEB`                | Copy Erasable Block               | ?         | `076x165`                 | ?                |
| `CPFB`                | Copy Fixed Block                  | ?         | `076x166`                 | ?                |
|                       | Reserved                          | ?         | `076x167`                 | ?                |

###### 8.1.2.3.15. Control instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `NOP`                 | No OPeration                      | ?         | `076x170`                 | ?                |
| `RESUME`              | Resume from interrupt             | ?         | `076x171`                 | ?                |
| `RUPT`                | Take all requested interRUPTs     | ?         | `076x172`                 | ?                |
| `PRA`                 | Push previous Return Address      | ?         | `076x173`                 | ?                |
| `EXRA+`               | EXecute word RA points to         | ?         | `076x174`                 | ?                |
|                       | Reserved                          | ?         | `076x175`                 | ?                |

###### 8.1.2.3.16. Condition code instructions

| Mnemonics, Operand    | Description                       |  Cycles   | 19-Bits opcode (Octal)    | Status Affected  |
|-                      |-                                  |:-:        |-                          |-                 |
| `RSTCC`               | ReSTore Condition Code            | ?         | `076x176`                 | ?                |
| `SAVCC`               | SAVe Condition Code               | ?         | `076x177`                 | ?                |

##### 8.1.2.4. No-address ops implemented in software

```
Octal       Op Code     Description
077 ----                Reserved
```

#### 8.1.3. Immediate data and transfers of control

* `DDDDD` is immediate data
* `AAAAA` is address, Fixed only

``` 
Octal       Op Code     19 18 17 16 15 14 13 12 11 10 09 08 07 06 05 04 03 02 01
1 0 DDDDD   DATA=        1  0  0  0  D  D  D  D  D  D  D  D  D  D  D  D  D  D  D
1 1 DDDDD   DATAA        1  0  0  1  D  D  D  D  D  D  D  D  D  D  D  D  D  D  D
1 2 AAAAA   EXEC         1  0  1  0  A  A  A  A  A  A  A  A  A  A  A  A  A  A  A
1 3 AAAAA   DEXEC        1  0  1  1  A  A  A  A  A  A  A  A  A  A  A  A  A  A  A
1 4 AAAAA   CALL         1  1  0  0  A  A  A  A  A  A  A  A  A  A  A  A  A  A  A
1 5 AAAAA   IF<0         1  1  0  1  A  A  A  A  A  A  A  A  A  A  A  A  A  A  A
1 6 AAAAA   IF=0         1  1  1  0  A  A  A  A  A  A  A  A  A  A  A  A  A  A  A
1 7 AAAAA   IF>0         1  1  1  1  A  A  A  A  A  A  A  A  A  A  A  A  A  A  A
``` 

##### 8.1.3.1. DATA= derivated pseudo-instructions

These pseudo-instructions assemble as DATA= hardware-instruction.  
DATA= is not used directly by the programmer, DATA= is not a assembly instruction.

```
Octal       Op Code     Description
1 0 DDDDD   OCT         Octal integer (range 0-77777), with optional prefixed ‘~’ for ones-complementing, no E or B scaling.
1 0 DDDDD   DEC         Decimal number with optional prefixed ‘-’ to negate by twos-complementing
1 0 DDDDD   DEG         Decimal integer with optional prefixed ‘-’ to negate by twos-complementing; representing degrees of angle
1 0 DDDDD   CIR         Decimal fraction with optional prefixed ‘-’ to negate by twos-complementin
1 0 DDDDD   RAD         Decimal real number with optional prefixed ‘-’ to negate by twos-complementin
1 0 DDDDD   ADR         Any address-field format usable with instructions
1 0 DDDDD   PARM        A single symbol which must refer to a “pseudo-constant” whose value becomes the 15-bit immediate data
```

##### 8.1.3.2. DATAA derivated pseudo-instructions

These pseudo-instructions assemble as DATAA hardware-instruction.  
DATAA is not used directly by the programmer. DATAA is not a assembly instruction.

```
Octal       Op Code     Description
1 1 DDDDD   OCTA        Octal integer (range 0-77777), with optional prefixed ‘~’ for ones-complementing, no E or B scaling.
1 1 DDDDD   DECA        Decimal number with optional prefixed ‘-’ to negate by twos-complementing
1 1 DDDDD   DEGA        Decimal integer with optional prefixed ‘-’ to negate by twos-complementing; representing degrees of angle
1 1 DDDDD   CIRA        Decimal fraction with optional prefixed ‘-’ to negate by twos-complementin
1 1 DDDDD   RADA        Decimal real number with optional prefixed ‘-’ to negate by twos-complementin
1 1 DDDDD   ADRA        Any address-field format usable with instructions
1 1 DDDDD   IMMA        A single symbol which must refer to a “pseudo-constant” whose value becomes the 15-bit immediate data
```

##### 8.1.3.3. DATA= & DATAA derivated pseudo-instructions

These pseudo-instructions assemble as DATA= and DATAA hardware-instructions.

```
Octal       Op Code     Description
1 0 DDDDD   2OCTAL      Octal integer
1 1 DDDDD

1 0 DDDDD   2DECAL      Decimal number
1 1 DDDDD

1 0 DDDDD   DLD=        Load a double precision
1 1 DDDDD
```
##### 8.1.3.4. IF derivated pseudo-instructions

```
IF<
IF=
IF>
```

```
IF<PI
IF=PI
IF>PI
```

```
IFNEG
IFNOT
IFPOS
```

##### 8.1.3.5. GOTO derivated pseudo-instructions

```
RETURN assembles as GOTO 0000
```

## 8.2. Interpretive Instructions

## 8.3. Pseudo instruction

```
=OCT
=DEC
=DEG
=CIR
=RAD
=ADR
```

## 8.4. Alias instruction

...

## Appendix A: Instruction Description

SAMPLE PREVIEW

[//]: <> (START Instruction Description)

### LDA

| LDA | Load A |
| - | - |
| Sytax | `[label] LDA e` |
| Operands | 0000 ≤ `e` ≤ 7777 |
| Operation | (e) → (A) |
| Status affected | |
| Encoding | `000 eeee` |
| Description | The content of erasable memory 'e' is loaded to A register |
| Words | 1 |
| Cycles | 1 |

<u>Example</u> 

```agc_assembly
        LDA     VAR1
``` 

After instruction  
A= value in VAR1 erasable memory location

### LDA*

 | LDA* | Load A (indirect addressing) |
| - | - |
| Sytax | `[label] LDA* e` |
| Operands | 0000 ≤ `e` ≤ 7777 |
| Operation | ((e)) → (A) |
| Status affected | CC |
| Encoding | `001 eeee` |
| Allow parallel Counter Increment | No |
| Immediate Data | Yes |
| Indexable |  |
| Description | The content of erasable memory pointed by 'e' is loaded to A register.<br />Load Erasable or immedate Data into A.<br />Set CC accordingly to the new value of A (Overflow, Positive, Negative, Zero)|
| Words     | 1 |
| Cycles    | 2 |

<u>Example</u> 
 
```agc_assembly
PTR1    =ADR    VAR1
        LDA*    PTR1
``` 

After instruction  
A= value in VAR1 erasable memory location

[//]: <> (END Instruction Description)

## Appendix B: Pulse Sequences  

[//]: <> (START Pulse Sequences)

SAMPLE PREVIEW

### Indirect Stage
Stage 0 of `LDA*` instruction

| Sequence  | Pulse Time    | Condition Code    | Control Pulses            | Comment   |
| -         | -             | -                 | -                         | -         |
| ST0       | PT1           |                   | `rF wSE`                  |           |
| ST0       | PT3           |                   | `rE wY B2X sCIcDAS`       |           |
| ST0       | PT6           |                   | `iSF2Z ST1 NDXT`          |           |

### LDA
Stage 1 of `LDA`, `LDA*` instructions

| Sequence  | Pulse Time    | Control Pulses            |
| -         | -             | -                         |
| LDA1      | PT1           | `rFxUc wSE ZxD2SFc zDEXD` |
| LDA1      | PT3           | `rDxEc wA wCC`            |
| LDA1      | PT6           | `iSF2Z zDACT NISQ`        |

[//]: <> (END Pulse Sequences)

## Appendix C: Pulse Description

SAMPLE PREVIEW

[//]: <> (START Pulse Description)

**rSE**  
_**R**ead **SE**_
```
Read SE15.15..1 to WL16.15..1
```

**rF**  
_**R**ead **F**_
```
Read F15.15..1 to WL16.15..1
```

**rFxUc**  
_**R**ead **F** **or** **U** **c**onditional_
```
If not IND 
    Read F15.15..1 to WL16.15..1
else
    Read U16..1 to WL16..1
```

**wSE**  
_**W**rite **SE**_
```
Write SE12..1 from WL12..1
```
[//]: <> (END Pulse Description)