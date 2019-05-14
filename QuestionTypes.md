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
`Wait time: time in ready state`

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
| P1  | 30          | 8         | 26              |
| P2  | 21          | 4         | 17              |

Two mistakes:

1. Time is not 0-indexed; we start from 1
2. When a process becomes ready (from blocked), it is added to the **_end_** of the queue. That's why, at `t=19`, P0 switches to P2, not to P1 (P1 just finished IO and is added to the end of the list)


## Disk Scheduling Algorithms

## Page Replacement Algorithms (PRAs)

### > What is the differenc between Shortest Process Next, Shortest Job First (SJF), and Shortest Remaining Time Next (SRTN)?

## Virtual to Physical Address Translation

Two types of questions:

1. VA --> [    Page Frame #    ][  Offset  ]
2. VA --> [ Seg # ][ Page Frame #][ Offset ]

## List of Concepts and Explainers

- Semaphores
- Mutexes
- The producer-consumer problem
- The dining philosopher problem
- The ......