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

_Example (sample midterm):_

| Process | CPU Time | Arrival Time | Blocks after | Blocks for |
| ------- | -------- | ------------ | ------------ | ---------- |
| P0      | 10       | 0            | 5            | 9          |
| P1      | 11       | 4            | 4            | 6          |
| P2      | 9        | 4            | n/a          | n/a        |

If FCFS (this is non-preemptive):

> Schedule: P0, P1, P2

> P0 runs for 5 cycles, then blocks for 9 cycles, then runs for 10 - 5 = 5 cycles.
>   Runtime = 19. Finish time = 19. Wait time = 0. Turnaround time = 19.
> 
> P1 runs for 4 cycles, then blocks for 6 cycles, then runs for 11 - 4 = 7 cycles. 
>   Runtime = 17. Finish time = 36. Wait time = 19. Turnaround time = 32.
>
> P2 runs for 9 cycles. 
>   Runtime = 9. Finish time = 45. Wait time = 36. Turnaround time = 41.

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