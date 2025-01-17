# MIPS Cheat Sheet
The essential knowledge for MIPS (hy225)

## 32 MIPS Registers

```
$0	=	0       Immediate 0					                               
$1	=	$at     assembler temporary			                               
$2	=	$v0 \	Procedure					                         
$3	=	$v1 /	Return Values				                          
$4	=	$a0 \								                          
$5	=	$a1 |	Procedure					                             
$6	=	$a2 |	Arguments					                             
$7	=	$a3 /								                             
$8	=	$t0 \								                           
$9	=	$t1 |								                          
$10	=	$t2 |								                          
$11	=	$t3 |	Temporary					                           
$12	=	$t4 |	Registers					                          
$13	=	$t5 |								                          
$14	=	$t6 |								                           
$15	=	$t7 /								
$16	=	$s0 \
$17	=	$s1 | 
$18	=	$s2 |     
$19	=	$s3 |	Saved    
$20	=	$s4 |   Registers 
$21	=	$s5 | 
$22	=	$s6 |  
$23	=	$s7 /  
$24	=	$t8 \   Temporary 
$25	=	$t9 /   Registers 
$26	=	$k0 \   Kernel   
$27	=	$k1 /   Reserved 
$28	=	$gp     Global Pointer 
$29	=	$sp     Stack Pointer 
$30	=	$fp     Stack Frame
$31	=	$ra    	Return Address        
```

## Instruction Formats
```
Type	    31-26(6)    25-21(5)  20-16(5)  15-11(5)  10-06(5)   05-00(6)
R-Type      opcode        $rs       $rt	      $rd      shamt      funct
I-Type	    opcode  	  $rs	    $rt	      imm
J-Type	    opcode  	 target
```

## MIPS Instructions (used in hy225)

```
add     $rd,  $rs,  $rt   // $rd = $rs + $rt | R-format
addi    $rd,  $rs,  Imm   // add Immediate (can take negative values, no need for subi) | I-format   
sub     $rd,  $rs,  $rt   // $rd = $rs - $rt  | R-format

sll     $rd,  $rs,  Imm   // shift less logical (shift 2 means adding x2 zeros in the end
                             resulting in multiplying the number by 2^2=4) | R-format  

beq     $rd,  $rs,  label // branch if equal | I-format  
bne     $rd,  $rs,  label // branch if not equal | I-format  

lw      $rd,  Imm($rx)    // $rd,  $rx,  Imm -> $rd = $rx[Imm] | I-format  
sw      $rs,  Imm($rx)    // $rs,  $rx,  Imm -> $rx[Imm] = $rs | I-format  

slt     $rd,  $rs,  $rt   // set less than | R-format
slti    $rd,  $rs,  Imm   // set less than Immediate | I-format   

j       target            // jump target | I-format
jr      $rs               // jump register | R-format
jal     target            // jump and link (essential instruction of MIPS, core 
                             of calling procedures and returning back to caller)
                             | I-format
                             
lui     $t,   Imm         // load upper immediate, immediate value is shifted left 16
                             bits and stored in the register. The lower 16 bits are zeroes
                             | I-format
```

## MIPS code

instructions starting with "." are directives to the compiler:
```
.asciiz declares that what follows, is an ASCII string
```
```
.data   declares that what follows, is data and not commands
```
```
.text   declares that what follows, are commands and not data
```
```
.globl  declares that what follows, are labels that should
            be stored in the global array
```
```
.align  declares that what follows, is the power to which
        2 must be raised in order to find the alignment 
        (e.g .align 2 means 2^2 = 4 bytes, which means that
        every address must be a multiple of 4, so it must 
        end with two zeros 00)
```
```
.space  declares that what follows, is the number of bytes we
        want the Assembler to give to our program to store data
```

## MIPS Memory alignments

Addresses on MIPS, are **Byte Addressable**, which means that addresses
are referring to **Bytes** in memory.

## Big-Endian και Little-Endian Machines

Big-Endian and Little-Endian Machines have both (left) MS -> LS (right), they also store number in this fashion (left to right) but Big saves strings in that manner, whereas Little in the opposite direction. 

### This is the byte order of Big and Little endian
word | BIG | LITTLE 
--- | --- | --- 
0 | *0-1-2-3* | *3-2-1-0*  
4 | *4-5-6-7* | *7-6-5-4* 
8 | *8-9-10-11* | *11-10-9-8* 
12 | *k-a-t-e* | *e-t-a-k* 
16 | *v-e-n-i* | *i-n-e-v* 
20 | *s-\0- -*    | *- -\0-s* 

## How do jump and branch instructions work ?

Jump and Branch instructions are I-format and the basic execution is as follows:<br>
**PC_new := (PC_br + 4) + 4 * ImmOffset (signed)**

**PC_new:** *is the new address the processor will execute next*<br>
**PC_br:** *is the address same as the address of the jump/branch instruction*<br>
**ImmOffset:** *is the the standart 16 bits of the I-format*<br>

## Procedure calls

The **caller** function and the **callee** function are connected as one calls the other.<br>
Who's responsibility is it to save variables before and after procedure calls ?<br>

There are two types of variables:<br>
**Temporary registers:** $t0-$t9 ($8-$15 και $24-$25), *it's the caller's responsibility to save the useful value before calling the new procedure ("callersaved")*<br>
**Saved registers:** $s0 - $s7 ($16-$23), *it's the callee's responsibility to save the old value each of all the saved registers before it changes them ("callee-saved")*<br>

## Register File (RF) Πολύπορτο Αρχείο Καταχωρητών
Although called a "file", a register file is not related to disk files. 
A register file is a small set of high-speed storage cells inside the CPU.
With a 32 registers of 32 bit size each.

# Pipeline
There are 5 states of MIPS's pipeline:
1. **IF:** **I**nstruction **F**etch
2. **ID:** **I**nstruction **D**ecode
3. **EX:** **Ex**ecute or address calculation
4. **MEM:** Data **Mem**ory
5. **WB:** **W**rite **B**ack

Although the flow is going from left to right, there are two exceptions:
1. **Write Back** which writes data back to the Register File (RF) mid flow
2. **Next PC calculation** which is decided between the increased PC or the branch address from the Data Memory stage.

For a pipeline to be implemented in a processor architecture, state memories have to be added, so that every state of the five above, can work individually. 
1. **IF/ID** 
2. **ID/EX** 
3. **EX/MEM** 
4. **MEM/WB**
These memories, work as checkpoints of the pipeline, for every single state to a percentile of the work, of one instruction.

## [Pipeline Hazards](https://en.wikipedia.org/wiki/Hazard_(computer_architecture) "Hazard (computer architecture)")
1. **Structural** hazard is the impotence of the hardware to support a certain combination of istructions.
2. **Data** hazard is when the pipeline has to wait, a certain amount of cycles, because it's waiting for another instruction to finish.
3. **Control** hazard is when the pipeline has to take a decision upon the outcome of an instructions whilst it is being executed.

## [Dependencies](https://en.wikipedia.org/wiki/Hazard_(computer_architecture)#Read_after_write_(RAW) "wiki examples")
1. **RAW:** **R**ead **A**fter **W**rite *(or write and then read)*.<br>
   **R2** <- R1 + R3<br>
   R4 <- **R2** + R3<br>
2. **WAW:** **W**rite **A**fter **W**rite *(or write and then write)*.<br>
   **R2** <- R1 + R3<br>
   **R2** <- R1 + R3<br>
3. **WAR:** **W**rite **A**fter **R**ead *(or read and then write)*.<br>
   R4 <- R1 + **R5**<br>
   **R5** <- R1 + R2<br>

1. **RAW:**<br>
   *in this **dependency you have to wait and there is no way to speed it up***
2. **WAW:**<br>
   *to solve this dependency you have to **cancel the first write in order to only keep the lader one***
3. **WAR:**<br>
   *to solve this dependency you have to **keep a copy of the old value in order to give it to the one will come later***

## Pipeline Jumps and Branches
Our pipeline will execute instructions although having fetched a branch or jump instruction. Given the probability of correctly executing the instruction on PC+4, although unaware if it has to get executed, saves us 2 clock cycles, while executing them wrongfully as the same effect as doing nothing at all (the only downside to not doing anything at all is the increase in power consumption).

If the branch is true and we have to jump (something that will be determined on the third part of our pipeline after the ALU has given its output), our pipeline will cancel the last two instructions by making all the control bits to null (0) in order to skip everything they made (this is possible because the instruction or not on the store phase of the pipeline so they have not stored nor altered any register or memory values).

## Pipeline cost per false prediction
instruction | execution<br>time (cc *)
--- | --- 
general | 1~
load that is followed by<br>dependent instruction | 2~
failed branches (1/3) | 1~
successfull branches ** (2/3) | 3~
jump *** | 2~
```
*  clock cycles
** success means that our prefetched PC+4 instructions are wrongfully executed
** as jump instructions are basically true branches you will always lose one cycle.
   Knowing that it is a jump withought the use of the ALU to determine it we can 
   know it one cycle prior to a successfull branch so one less cycle is lost
```

## Pipeline Branch Target Buffer (BTB)
```
Is a small memory (cache-like catalog) that keeps a registry of where the program flow will go to.
Making branch predictions more accurate as, memory of previous jumps and branches are stored.
It basically stores branches that it believes will be seccessfull with a probability of over 50%.
```
*This way of storing data has been refined after years of research and now achieves 95-98% accuracy.*
 *This means that 95-98% of the branches will take 1 cc, and 2-5% will take 3cc*

# Processor performance (CPI)

t*exec* = N*instructions* * CPI*average* * T*clock*

>* **t*exec***: time of execution<br> 
>* **N*instructions***: Number of instructions<br>
>* **CPI*average***: Clocks per instruction on average<br>
>* **T*clock***: Time it takes for on clock cycle to finish<br>

In the real world an architecture has more than instructions that may take more or less clocks to execute, lets take an example of an architecture with two sets of instructions A and B (which take diffrent ammounts of clocks to finish). 

> texec = ( N*A* * CPI*A* + N*B* * CPI*B* ) * Tclock = Ninstructions * CPIaverage * Tclock 

The problem here lands in the fact that you may have more than two diffrent CP instructions. For this reason we take an estimated average and use the same equation as before.

> CPIaverage = ( N*A* / N*instructions* ) * CPI*A* + ( N*B* / N*instructions* ) * CPI*B*

And through simple mathematics we get: **t*exec* = N*instructions* * CPI*average* * T*clock***

# Memories

## Memory Hierarchy

Name | Meaning | Postitive | Negative
--- | --- | --- | ---
**SRAM** | **S**tatic **R**andom **A**ccess **M**emory | faster than DRAM | consumes more power and costs more
**DRAM** | **D**ynamic **R**andom **A**ccess **M**emory |slower than SRAM | consumes less power and costs less
**SDRAM** | **S**yncronous **D**ynamic **R**andom **A**ccess **M**emory | double the speed of DRAM | -
**DDRRAM** | **D**ouble **D**ata **R**ate  **R**andom **A**ccess **M**emory | synchronized to a specific clock speed | -

*In todays market when you buy a DDRAM you buy a **S**yncronous **D**ynamic **D**ouble **D**ata **R**ate **R**andom **A**ccess* **M**emory*

## Memories have what we call locality which is split in to two categories:
>* **Temporal**: *refers to the reuse of specific data, and/or resources, within a relatively small time duration* 
>* **Special**: *refers to the use of data elements within relatively close storage locations*

# Cache Memory 

Cache level | Size | Speed
--- | --- | --- 
*Registers* | 128 Bytes | 0.3 ns
*L1* | 16-64 KB | 2 ns
*L2* | 256KB-2MB |3 ns
*L3* | 2-16MB | 10 ns

> Assebmly programmers do only know about the 32 registers not the caches,<br> caches are processor controlled and not assembler controlled.
> A cache must always now the source of the data it has stored but also the data itself.

A cache has two parts<br>

**Valid Bit** | ***1 bit*** | **Tells us if current cache line/block is valid**
--- | --- | --- 
**Tag (Address)** | ***18 bits*** | **holds the address of the data**
**Data** | ***32 bits*** | **holds the data of a word**

> * **The hash function used is the following: (place in cache) = (address of main memory) modulo (size of cache).**
> * **We have to find a way to match the main memory to the cache. Our processor is a 32 bit one, and as the level 1 cache is 16KB in size there is a set amount of values important to us. This is done by using a hash function that uses modulo to break the system memory in (MAIN MEM SIZE / CACHE SIZE) fragments. As the 2 most LS bits refere to the byte in word, we don't store them, after that we need the following 12 LS bits as they are the same as the physical address of the cache and are unique for every line in current modulo**
> * **Pay attention to the Tag size being *18 bits*. Having used the 14 most LS bits to determine the address of the cache line in correlation to the main memory it's not time to save the rest of the main address as the tag in the current line 32-14=18**
> * **As you may have noticed the tag size is 19 bits not 18 bits, this is because of the added information we need for the valid bit
to know if the data is correct and not noise in order to not lose any of the 2^18 in addressing capacity.**
> * **The following L1 cache is of size 16KB (4 words) and as you may have understant the 32 bits data reffer to the whole 16KB os size without the tags.**

## Average Memory Access
t*eff* = hit_ratio * t*hit* + miss_ratio * t*miss* =><br>
t*eff* = t*hit* + miss_ratio * t*miss_penalty*

>* **t*eff***: *effective access time*<br> 
>* **hit_ratio**: *percentage of correct accesses*<br>
>* **t*hit***: *the time it takes for a hit to get the data*<br>
>* **miss_ratio**: *percentage of incorrect accesses*<br>
>* **t*miss***: *the time it takes for a miss to get the data*<br>
>* **t*miss_penalty***: *tmiss - thit, the extra time it takes<br>
>to access the data after a miss (miss penalty)*<br>

## Techniques used to speedup cache efficiency and accesstime
> * **Blocks are multi-line word lines, in our case a block consists of 4 words.**
> * **When data is not found in current level cache, the request sent to the higher level is of a block containing the desired data. By doing this we have in mind the spacial locality of the programs, as the next line may be accessed shortly after the current one.**
> * **16KB Direct Mapped with a block size of 4 words (16 bytes) which means 4K words are 1K blocks this means that the number of tags are less, which speedsup operations. Not only that but getting a block is much faster than getting two separate accesses of 2 words (as gettings the near by data is fast)**
> * **Blocks as everything in computer science is aligned, so for example 16-17, 18-19, ... are some pairs, this means that in general you get a speedup but there is a downside, which is that rather than replacing one cache line at a time we replace two.**

As we have made our cache to store blocks rather than lines, accessing it will have to be modified as well:

> * ***In comparison to before the diffrence lies in the simple fact that we now have 1K blocks rather than 4K lines.* 
    As the 2 most LS bits refere to the byte in word, we still have no use for them, but this time rather than useing the whole  12 LS  bits we separate them to the 10 MS and the 2 LS. This change is made because we have less number of blobks than cache lines and need less bits to access them. To find the cache block address we use the 10 MS bits and to find the word in the block the 2 LS**

18 bits tag value |MS| | | | | | | | | | |LS|DC|DC|
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

18 bits tag value |MS| | | | | | | | |LS|X|X|DC|DC|
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

>* **MS-LS**: cache address<br> 
>* **DC**: don't care bits<br> 
>* **X**: are the bits that tell us which word in block 2^2 = 4 words<br> 

## Set Associative Cache
* Our cache after the modifications we made to it, has become a 1K blocks in size. As we wanted to speed it up even more we introduced set associativity. Set associative cache can be of multiple ways, 2-way, 4-way, 8-way, etc (at 8-ways we reach a limit in speedup as after that we see a decline in performance as program sizes tend to be smaller for such a big associativity). 
* Lets take for example the 2-way associativity, what we basically do is, we break the cache in two diffrent caches with their only diffrence being one bit. 
* As you understand one cache block can now be stored in two diffrent places rather than one. This means that if a new block want to come to the cache, and the one way is occupied but not the other, it will be stored in the other way. 
In this way, we increase the spacial locality with the tradeoff being the smaller size, as 2-way associativity halves our cache size from 1K Blocks to 512  Blocks. 
* These pairs are called sets, and in our case there are 512 sets

> Using this kind of organization means wee have to change the way we access the cache once again. The corallation between memory and cache is now done by the 9 most LS bits for the address as the 1 MS bit gives us the two diffrent ways two go, so we ignore it.

18 bits tag value |MS| | | | | | | | | | |LS|DC|DC|
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

18 bits tag value |MS| | | | | | | | |LS|X|X|DC|DC|
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

19 bits tag value |MS| | | | | | | |LS|X|X|DC|DC|
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

## Replacement Policy
A set assiociative cache is a cache that has one place for more than one blocks (set) which means that we need a replacement Policy if congestion occurs. To choose what to replace, there are many techniques, one famous and well established is to look at the past, which is a very good inticator of what will happen, this is called Least Recently Used or LRU. 

LRU
> **2-way**
>>  To achive this a single bit is used, to indicate **0 as left** and **1 as right**. So that the next time I come here and get a miss I will replace the !(bit) which will be the older of the two.

> **4-way**
>> For a 4-way associative it is not that simple, as 2! = 2 which can be done with 1 bit, but where we have to have 4! = 24 for which we need a whole circuit.

#### A technque that is proven to be very effective aswell is random access, which is used for >2-way. Lets take the 4-way for example, in this case it would basically choose one of the two 2-way sets in random and perform an LRU on that one.  
#### In direct-mapped cache, each location in main memory can go in only one entry in the cache. Therefore, a direct-mapped cache can also be called a "one-way set associative" cache.

## Fully Associative 

In a Fully associative cache, the cache is organized into a single cache set with multiple cache lines. A memory block can occupy any of the cache lines.

### Advantages
> * ***Fully associative cache structure provides us the flexibility of placing memory block in any of the cache lines and hence full utilization of the cache.*** <br>
> * ***The placement policy provides better cache hit rate.*** <br>
> * ***It offers the flexibility of utilizing a wide variety of replacements algorithms if a cache miss occurs.*** <br>

### Advantages
> * ***The placement policy is slow as it takes time to iterate through all the lines.*** <br>
> * ***The placement policy is power hungry as it has to iterate over entire cache set to locate a block.*** <br>
> * ***The most expensive of all methods, due to the high cost of associative-comparison hardware.*** <br>

## Writing Policies
* **Write-Through:** *write is done synchronously both to the cache and to the backing store.*
* **Write-back:** *initially, writing is done only to the cache. The write to the backing store is postponed until the modified content is about to be replaced by another cache block.*

## Virtual Memory
In computer science Virtual Memory is a memory management technique that provides an 
* ***idealized abstraction of the storage resources that are actually available on a given machine** which*
* ***creates the illusion to users of a very large (main) memory, that is not existant or if it is, it is not theirs exclusively***.

Although being another part of the memory hierarchy, communication (data transfer) between Main Memory and Disk is not done with blocks, but with pages. Pages for blocks are what blocks are for words. As Memory becomes bigger and slower we want to make as little transfers as possible, so bigger chunks of memory have to be tranfered at once as the cost of spacially close data is transfered faster than in separate parts. 

> ***Block line:*** *32-256Bytes* <br>
> ***Page:*** *4-16KBytes*

###### TMYN: although increasing, page size has come to a stoll as manufacturers, want to have backwards compatebility and don't increase the page size in retrospect to todays hardware

## Virtual Addresses 

All programs work wth virtual addresses (a processor is not capable of creating because the hardware doesn't allow it). A virtual address is 32 bits in size. <br>
> * **12 LS bits** *tell me **where** in the page I am* **2^12 = 4096 virtual blocks in virtual page**
> * **20 MS bits** *tell me **which** page* **2^20 = 1048576 virtual pages**
 	
MS 20 | 12 LS   
--- | --- 

Physical Addresses are 32 bits aswell, with the 2 LS bits being the byte in the word, so for us only 32 MS bits are of importance.<br>
> * **12 LS bits** *tell me **where** in the page* **2^12 = 4096 physical blocks in physical page**
> * **18 MS bits** *tell me **which** page* **2^18 = 262144 physical pages**

MS 18 | 12 LS
--- | --- 

* The 12 LS bits are the same between the virtual and physical addresses, but the MS bits have to go through a **Page Table** that is of size 2^20 = 1048576 = 1MB in size, and find the physical address it referees to. <br>
* This Table is 18 bits in size with extra bits being the Valid, Protection, Dirty and Reference (LRU) bits <br>
> * **Valid**: *tells us if the current address is valid or not (if it exists or not)*
> * **Protection**: *tells us what kind of access we have rwx*
> * **Dirty**: *tells us if the processor changed the value*
> * **Reference**: *gives us information for the LRU to swap or not from memory for a new value*
* But a table of size 1MB in the main memory is very slow, so we created a cache called **Translation Lookaside Buffer (TLB)** which has a soul purpose of caching a small amount *~ 16-64* of **entries** (pairs) of pairs, from virtual addresses to physical addresses.

Process ID | Virtual Address | Physical Address
--- | --- | --- 
| ... | ... | ... 
| A | 13 | 7
| B | 13 | 3
| ... | ... | ... 

##### This cache is fully associative as it is really small, and we want it to be fast and with a small miss ratio

## Page size >= Way size 

Our page size has to be >= Way size which is the reason we want bigger page sizes. If not done you can have different pointers pointing to the same physical memory. If you have two different pointers, pointing to the same physical memory, but pointing to different entries in the cache, then you will be in trouble. This happens because memory mapping is done page by page. So both pointers will have the same offset into a page, but the page numbers can be different. One pointer can have an even page number, one an odd page number. If your cache is bigger than a page, for example two pages, then two pointers with an even and an odd page numbers would point to different cache entries, exactly what we want to avoid.

## Safety between users and kernel
Processors have a bit to tell them whether they are in Kernel Mode or User Mode. This is especially important as we have to separate user from kernel code and not let the user run kernel code. Users between themselfs cannot run as other processes as he cannot change his Page table to another Page table (for he has not a Kernel Access to do so).

### Communication between processes
Communication between processes lets say linux pipes, is achived by mapping one virtual address from both processes to the same physical address. 

## Multilevel Page Table
As processes vary in size smaller processes would use alot memory they don't need, this problem is solved by multilevel page tables.
Those tables work in a very strict manner as they use multiple hash tables. 
1. From the 32 bits we take the first 10 MS bits to look in the first level 1024 size hash table, if they are legal.
2. If it is legal I take the next 10 MS bits to check on the next level 1024 size hash table, to which I was directed from the previous level.
3. If found this page table will point to the physical address for us to access it.

## Memory speed
On all memory architectures, it is a good practise to request, more information close to each-other as spacial locality is present.
There are two values which express these exact observations:
> * **Start-up cost**: *which is the time spent from when the request was sent, processed by the ram and finally sent to the bus.*
> * **Throughput**: *which is the amount of data that can be sent on certain request.*

###### It is more cost-efficient to request more as 4 individual request will take 40ns+40ns+40ns+40ns = 160ns whereas 4 combined will take 40ns+10ns+10ns+10ns = 70ns to be delivered thus increasing the throughput.

### Interleaving 
One technique of parallel memory reads/writes is interleaving which is a design made to compensate for the relatively slow speed of dynamic random-access memory (DRAM), by spreading memory addresses evenly across memory banks. That way, contiguous memory reads and writes are using each memory bank in turn, resulting in higher memory throughputs due to reduced waiting for memory banks to become ready for desired operations.
###### Interleaving is a technique not only used in memories (like SDRAM) but also on PCI-X bus which has made communication between processors-memory-I/O faster and more efficient keeping the bus busy at all times. This technique on the bus was called Split Transaction. Nowadays networks are used and not busses.

# I/O

## Memory-Mapped I/O
Memory-mapped I/O uses the same address space to address both memory and I/O devices. The memory and registers of the I/O devices are mapped to (associated with) address values. So when an address is accessed by the CPU, it may refer to a portion of physical RAM, or it can instead refer to memory of the I/O device. Thus, the CPU instructions used to access the memory can also be used for accessing devices.

## Communication between processors memory and I/O
Communication between devices with diffrent speeds inderlies many problems which have to be solved.
* **state and data registers** 
> Communication with non-cachebale I/O like the keyboard is done with **state and data registers** (**and not memory**), the processor reads from the keyboard only when the keyboard writes *true* in the status register. When the processor read the data it turns the states register to the *false*, and waits until the status beomes *true* again. This is done to solve the problem of writing the word "aaron" for wxample as the first two letters are *a*.
* **Polling** 
> Here the processor is the one asking the I/O if anything new has come. System timers are being used to interupt the I/O, those timers are operating on a 50-120 Hz frequency which equals to 20-8ms. This is the time every program runs until it is stopped so that another one runs.
>> (This frequency is not very long for the cpu nor is it distinguishable by a human observer, in order to give the user the illusion of multiple programs running simultaneously)
* **Busy Wait** 
> With this technique the computer is always in a loop, watching whether anything changes. This is not very efficient as I/O as the keyboard may write several words in the span of minutes, which in cpu time is equivalent to years, thus making it a very bad practice for most practises except some who have to communicate very fast like lock on multiprocessor programs.
* **Interrupt** 
> In this technique the I/O makes an interupt to the processor, telling it it has new data for him to process.

## Direct Mapped Memory (DMA)
DMA is a technique used between I/O and processor to make communication between them more efficient.<br>
As I/O devices are operating in a much slower speed than the processor, they have buffers (4KBytes) and a controller called DMA.
1. They take requests from the processor, of what and where to store it in memory
> * physical address to write to <br>
> * size of data you want to copy <br>
> * address from I/O buffer <br>
> * go status <br>
>> This DMA is ambidrome as data can be ordered to flow from or to an I/O device or to memory 
2. When go_status bit is turned to true by the processor, cpy is initiated, without the processor
4. And then the processor can read the whole chunk uninterupted at his higher speed of operation.
###### As memory from I/O devices is not cacheble the speedup is large

### Problems with multilevel memories and DMAs
The problem is that the DMA talks to main memory (DRAM) but if data is being edit in cache the copy in main memory is out-of-date, this is a problem and has to be solved. **This problem is called cache coherence.** This problem is not only present between DMAs and memory but caches themselves. <br>
Many protocols are used to keep cache coherent accros multiple processors with multiple caches, but the most famous are: <br>

1. **M***odified* **S***hared* **I***nvalid* **( MSI )** <br>
2. **M***odified* (**O***wmed*) **E***xclusive* **S***hared* **I***nvalid* **( MoESI )** 

* **Modified** : *Dirty* & *Exclusive, which means that multiple have it but only I have the latest value because I altered it*
> **write-invalidate** *means that when one processor writes a shared value he has to invalidate the other processors copies, by telling them to invalidate it* <br>
> **write-update** *means that when one processor writes a shared value he has to update the other processors copies, by telling them the new value so they update their copies* 
* **Shared** : *Clean* & *Shared, which means that I have data that is shared but all of us have the same copy* 
* **Invalid** : *Nothing* 
***
* **Owned** : *Dirty* & *Shared, which means that it is dirty only towards the main memory*
* **Exclusive** : *Clean* & *Exclusive, which means that only I have a copy of this value*

###### *ΜΑΝΟΛΗΣ ΚΑΤΕΒΑΙΝΗΣ QUOTES*
> *"Σκάσε και μέτρα"*<br>
> *"Των φρονίμων τα παιδιά πριν πεινάσουν μαγειρεύουν"*<br>
> *"Μεγάλη μνήμη άρα και αργή"*<br>
> *"Είναι σταγόνα εν το ωκεανό"*<br>
> *"Δεν περνάς κυρά Μαρία"*<br>
> *"Τι κάνει νιάου νιάου στα κεραμίδια"*<br>
> *"Και τι διαβάζει; Μπαρμπούτσαλα"*<br>
> *"Μην αναβάλεις ποτέ για run-time αυτό που μπορείς να κάνεις σε compile-time"*<br>
