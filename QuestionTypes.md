# Question Types

## Process Scheduling algorithms

Given a list of processes, what will the _schedule_ be like? What's the _turnaround time_ for each process?

Attributes for a process

- Arrival time  [essential]
- CPU time (or runtime if there's no IO block)  [essential]
- IO-CPU switch [possible]
- IO block duration and time [possible]
- Priority      [possible]

Potential algorithms:

- FCFS
- SJF
- SRTN
- SPN
- RoundRobin
- Priority Scheduling
- Highest Penalty Ratio Next

**_The lab 2 rule (on how to break ties):_**

- If two processes have the same priority:
  - Favor the one with the earlier arrival time
  - Favor the one listed earlier in the input (if they have the same arrival time)

`Turnaround time = Finish Time - Arrival Time     // note that arrival time is NOT start time`
`Wait time: time in ready state     // Don't forget the wait time b/t arrival & first run`

_Example (sample midterm):_

| Process | CPU Time | Arrival Time | Blocks after | Blocks for |
| ------- | -------- | ------------ | ------------ | ---------- |
| P0      | 10       | 0            | 5            | 9          |
| P1      | 11       | 4            | 4            | 6          |
| P2      | 9        | 4            | n/a          | n/a        |

If FCFS (this is non-preemptive):

```
TIME  1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30
P0    X X X X X b b b b  b  b  b  b  b              X  X  X  X  X  DONE
P1              X X X X  b  b  b  b  b  b                          X  X  X  X  X  X  X  DONE
P2                       X  X  X  X  X  X  X  X  X  DONE
```

|     | Finish time | Wait time | Turnaround time |
| --- | ----------- | --------- | --------------- |
| P0  | 23          | 10        | 23              |
| P1  | 30          | 15        | 26              |
| P2  | 18          | 0         | 26              |

If RR (dependent var: quantum = 3):

Sample _**mistake**_:
```
TIME  0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29
P0    x x x x x b b b b b  b  b  b  b     x  x  x                    x  x
P1              x x x         x  b  b  b  b  b  b  x  x  x                 x  x  x  x
P2                    x x x      x  x  x                    x  x  x
```

Correct:
```
TIME  1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30
P0    x x x x x b b b b  b  b  b  b  b     x  x  x           x  x
P1              x x x          x  b  b  b  b  b  b                 x  x  x  x  x  x  x
P2                    x  x  x     x  x  x           x  x  x
```

|     | Finish time | Wait time | Turnaround time |
| --- | ----------- | --------- | --------------- |
| P0  | 23          | 4         | 23              |
| P1  | 30          | 10        | 26              |
| P2  | 21          | 9         | 17              |

Two mistakes:

1. Time is not 0-indexed; we start from 1
2. When a process becomes ready (from blocked), it is added to the **_end_** of the queue. That's why, at `t=19`, P0 switches to P2, not to P1 (P1 just finished IO and is added to the end of the list)

If Highest Penalty Ratio Next:

```
Penalty ratio = T / t
    T: time in system: current time - arrival time
    t: time running (initially 1 to avoid DIV/0!)
```

HPR is non-preemptive: scheduling only happens after a burst (IO or CPU) ends.

```
TIME  1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31
P0    X X X X X b b b b  b  b  b  b  b                 X  X  X  X  X  DONE
P1              X X X X  b  b  b  b  b  b                             X  X  X  X  X  X  X  DONE
P2                       X  X  X  X  X  X  X  X  X  X  DONE
```

| Arrival Time |                  | T = 6       | T = 9        | T = 20     |
| ------------ | ---------------- | ----------- | ------------ | ---------- |
| 0            | P0 penalty ratio | 6 / 5 = 1.2 | 9 / 5 = 1.8  | 20 / 5 = 4 |
| 4            | P1 penalty ratio | 2 / 1 = 2   | 5 / 4 = 1.25 | 16 / 4 = 4 |
| 4            | P2 penalty ratio | 2 / 1 = 2   | 5 / 1 = 5    |            |

|     | Finish time | Wait time | Turnaround time |
| --- | ----------- | --------- | --------------- |
| P0  | 24          | 5         | 24              |
| P1  | 31          | 10        | 27              |
| P2  | 19          | 5         | 15              |

Notes:

- Because the algorithm is non-preemptive, once P2 starts running at T=10 (because P0 & 1 are both blocked) it runs until it finishes

### Difference between Shortest Process Next, Shortest Job First (SJF), and Shortest Remaining Time Next (SRTN)

- **Shortest process next [non-preemptive]**: schedules based on the estimated time of a process; shortest process given highest priority
  - `new estimate = A * old estimate + (1 - A) * last burst   // initial est. given; 1 < A < 1`
- **Shortest job first [non-preemptive]**: when processes arrive, they come with their CPU time. Processes with the shortest runtime given priority.
- **Shortest remaining time next [preemptive]**: When a new process arrives, its runtime (provided) is compared w/ the remaining runtime [runtime - time ran] of the current process. If new process has a shorter remaining time, the current process is preempted.

## Disk Scheduling Algorithms

## Page Replacement Algorithms (PRAs)

1kb = 2^10 bytes
1 byte = 2^3 bits

```
NumPage = AddresseSpace / PageSize      // also NumPTE
PageTableSize = NumPTE * PTESize
```

Question format:

input: PRA, NumPageFrame, NumPage, ReferenceString
output: NumPageFaults

Solution:

Implement a page frame table where # columns = NumPageFrame.
At each page fault, record a new row indicating the page replaced, based on PRA. The page being replaced is the one directly above the incoming page.

_example: Q28 Ch3:_

If FIFO page replacement is used with four page frames and eight pages, how many page faults will occur with the reference string 0172 3271 03 if the four frames are initially empty? Now repeat this problem for LRU.

_Do not confuse FIFO with LRU._

_For FIFO:_

**0**
0 **1**
0 1 **7**
0 1 7 **2**
**3** 1 7 2
3 1 7 **0**

6 faults.

_For LRU:_

**0**
0 **1**
0 1 **7**
0 1 7 **2**
**3** 1 7 2
**0** 1 7 2
0 1 7 **3**

7 faults.

### Difference between LRU and NRU

- **LRU (least recently used)** simply keeps track of the times at which a page has been referenced lately (_alternatively, the scheduler can keep track of a linked list of all pages that moves a page to the end when referenced._) The one with the oldest latest reference time is the viction.
- **NRU (not recently used)** is quite different from LRU, despite the similarity in their naming. Using R and M bits, the algorithm separates pages into 4 categories. The lowest numbered page from the first non-empty class is the viction. Below are the classes, in order of preference:
  - Class 0:  R = 0, M = 0
  - Class 1:  R = 0, M = 1
  - Class 2:  R = 1, M = 0    // prefers recently R-ed to M-ed
  - Class 3:  R = 1, M = 1    // last resort

### Why is LRU not used in practice, and what algorithms have been proposed as an approximation for LRU

The LRU is not used in practice because it is expensive: memory references happen very frequently, recording a timestamp _every time_ a reference occurs incurs a significant performance overhead.

To approximate LRU, two algorithms have been proposed to keep track of reference recency. One is **NFU**, which counts a page's cumulative R (it adds the R bit and resets it every cycle). An issue with this algorithm is that it does not distinguish between old and new references, thereby discriminates against younger residents. Another is **Aging**, which addresses NFU's issue by evicting page frames with older references. 

### Difference between NFU and NRU

- **NFU (not frequently used)** attempts to evict based reference frequency. The algorithm approximates reference frequency by keeping track of the a page's sum of Rs. The page with the lowest total reference count is evicted.
  - Aside: what is the issue with this algorithm?
- **NRU (not recently used)** attempts to evict track of recency. It determines a page's reference recency based on its R and M bits (see above). Pages referenced recently but not modified is considered a more ideal resident than pages modified but not referenced recently.

_**Note that total reference count does not equate to the sum of Rs**_. R == 1 merely indicates that a page has been referenced in a clock cycle; it could be referenced once or multiple times, and R does not record this information. 

### What is the biggest issue with NFU and what algorithm attempts to address it

The issue with NFU, which keeps track of each page's total R, is that it favors old residents and discriminates against new residents. Conceivably, page frames which have not been swapped out for a long time likely has a high total reference count, giving it a low priority level for eviction. New pages come and go (chosen for eviction), because every time a page fault occurs, it is difficult for the relatively new page to compete with the old page, given its long-standing record of memory references. This makes older pages' total references count even higher. The situation hence becomes a vicious cycle.

**The aging PRA** copes with this issue. Instead of adding the R bit at every clock cycle, aging right shifts the counter and prepends the R bit, thereby giving most recent references a much higher weighting. While one of aging's compromises is that it can only look back `n` cycles (the length of the counter) while NFU looks back `2^n`, in practice, if a page has not been referenced in `n` cycles it probably is not that important.

### Why would one prefer WSClock over the Working Set algorithm

Working Set refers to `W(k, t)` for any given process, where k is the number of most recent references and t is the current time. While the idea is great in theory, keeping track of the working set by updating it upon every memory reference is prohibitly expensive. One could imagine a system where the OS performs a right shift of a bit string of pages referenced when there's a new page referenced.

WSClock is much less expensive because it does not require such right shifting. The algporithm approximates working sets by using time instead of K number of references: if a page is referenced more than 𝜏 time ago, it is outside of the working set.

### Why is second-chance preferable to FIFO

Second-chance provides a substantial improvement to FIFO because it tries to avoid evicting old page frames that are nonetheless referenced _recently_. Frames referenced recently are likely to be referenced again soon (the locality of reference), the thinking goes.

Specifically, second-chance checks whether the head of a FIFO queue of page frames satisfies R == 1. If so, the R bit is reset and the page placed at the end of the queue ("second chance": as if the page has just arrived). The page evicts the first R == 0 page frame it finds.

## Deadlock Recovery

### Name the necessary conditions for a deadlock to occur

1. **_Mutual exclusion_**: only one process can have access to a resource
2. **_Hold & wait_**: a resource is allowed to request more resources when it already holds one
3. **_Non-preemptive_**: the OS cannot preemptively take back a resource from a process
4. **_Circular Wait_**: there exists a circular waiting list

### How can we prevent / recover from a deadlock now that we know the 4 pre-conditions for deadlock

1. For mutual exclusion: we allow multiple processes to access a resource at the same time. This is readily achievable for read-only resources (e.g. reading a file); for writing access, a spooling system such as that for the printer can be introduced.
2. For hold & wait: processes must request _all_ the resource they need when they start. This is often not realistic because processes may not know what they need.
3. For non-preemptive: we give OS the ability to take back a resource. This is often not realistic because it may not make sense for the OS to stop a process from using a resource at a random time.
4. For circular wait: we order resources with a numerical code. Requests must be made in consistency with this order (e.g. if printer is 3 and disk is 11, then a process must always request printer first, then disk).

## Deadlock Prevention -- Safety State & the Banker's Algorithm

A state is _**safe**_ if and only if there exists a sequence in which every process can request up to its maximum claim and terminate.

Algorithm implementation:

- Implement three matrices: **CurrentResourceOwnership**, **MaxUnmetDemands**, and **ResourceAvailability**. For CurrentResourceOwnership and MaxUnmetDemands, the columns are resources and rows are processes.
- While CurrentResourceOwnership != 0:
  - Find a row in MaxUnmetDemands that is < than ResourceAvailability
    - pretend that row is finished by releasing its CurrentResourceOwnership to ResourceAvailability
  - Else, there's no remaining process that can be satisfied by ResourceAvailability; break
- Repeat this process for all possible entry points; remember, we only need _one_ successful sequence for the state to be safe



## Virtual to Physical Address Translation

Two types of questions:

1. VA --> [    Page Frame #    ][  Offset  ]
2. VA --> [ Seg # ][ Page Frame #][ Offset ]

## List of Concepts and Explainers

- Semaphores
- Mutexes
- The producer-consumer problem
- The dining philosopher problem
- The readers writers problem