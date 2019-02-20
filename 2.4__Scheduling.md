# 2.4 Scheduling

Module covered on 02/20/19

## Table of contents

- [What is scheduling](#what-is-scheduling)
- [When to make scheduling decisions](#when-to-make-scheduling-decisions)
- [Categories of scheduling algorithms](#categories-of-scheduling-algorithms)
- [Goals of scheduling algorithms](#goals-of-scheduling-algorithms)
- [Scheduling in batch systems](#scheduling-in-batch-systems)
- [Scheduling in interactive systems](#scheduling-in-interactive-systems) 

## Notes 

### What is scheduling 

- early computing: computer systems were monoprogrammed 
- modern PCs are multiprogrammed 
  - most of the time there's only one active process
  - scheduling not critical (one user)
- scheduling-intensive situations
  - a bunch of users and/or if the demands of the jobs differ (e.g. P1 accepts user requests P2 calculates numbers)
  - batch servers
  - Time sharing machines 
- process behavior
  - *CPU bound (CPU burst)* (computing)
  - *I/O bound (I/O bursts)*: process enters a blocked states waiting for external device to complete work 

### When to make scheduling decisions

- **new process creation**
  - e.g. fork(); scheduling is *desirable* as it takes the new process inot account 
- **when a process exits**
  - e.g. exit(); scheduling is *necessary* because the previous process no longer exists
- **a process blocked** (Running —> Blocked)
  - e.g. read(); scheduling is *necessary* previous processs is blocked 
- **a process unblocked** 
  - e.g. I/O interrupts happen (to let process know that I/O has finished) scheduling is *desirable* as the blocked processs is now in Blocked —> Ready state 
- **process is preempted** (Running —> Ready)
  - scheduling is *necessary* 
  - done by scheduler itself; requires a clock interrupt (every 50-60Hz) to make scheduling possible 
- **Preemptive scheduling**
  - shceudler lets the process run for a maximum amount of time based on policy
  - Running —> Ready without process request 
  - needs a *clock interrupt* to occur at the end of the time interval to give control back to the scheduler 
  - expensive 

- **Nonpreemptive scheduling** 
  - scheduler picks a process, lets it run until: 
    - it blocks (by I/O, waiting for another process)
    - voluntarily releases the CPU
  - If there's no clock, nonpreemptive scheduling is the only available option

### Categories of scheduling algorithms

- **Batch**
  - systems doing accounts receivable, payroll, etc. used in banks, insurance companies, etc.
  - no waiting users 
  - Nonpreemptive, or preemptive w/ long time periods 
    - reduces process switching 
- **Interactive systems**
  - preemption is critical for rapid response time to short requests. 
- **Real time (deadlines)**
  - preemption is not that important 
  - processes operate within their approved time window 
  - critical systems

### Goals of scheduling algorithms

1. fairness 
2. respecting priority
3. efficiency 
4. low turnaround time
5. High throughput
6. low response time 
7. degrade gracefully under load

Note: *low turnaround time*:  every job coming in [submission] spend as little time in the system as possible [termination]

### Scheduling in batch systems

#### First-Come, First-Served (FCFS)

- Essentially FIFO
- easy to implement 
- processes assigned CPU time in order they request it 
  - single queue for ready processes 
  - a Blocked —> Ready process joins the end of the queue 
- <u>Nonpreemptive</u> (scheduler won't preempt and force running process to ready)
- won't work for a varied workload (what if the first process takes an hour to run but the second takes only seconds?)

#### Shorted Job First

- sort jobs by execution time needed and run the shortest first 
- jobs' runtime in advance (problematic, if not impossible, in practice)
  - may be able to project runtime given prior runs 
- jobs need to be available simultaneously
- <u>Nonpreemptive</u>
- Provably optimal: proven to have lowest avg. wait time, given runtimes as inputs 
  - Scratch proof (Assume these jobs won't run into blocks): 
    - 4 jobs with runtimes of a, b, c, d
    - first finishes at a, second a + b, third a + b + c, fourth a + b + c + d
    - mean turnaround time is `(4a + 3b + 2c + d) / 4`
    - `MIN(Turnaround Time)` ==> has to satisfy `a < b < c < d`

#### Shortest Remaining Time Next

- Picks job with shortest time to execute next
- <u>Preemptive</u>: the current running process has been proven to have the shortest remaining time left (among the current processes), the only variable that may change the situation is a new process coming in
- need to know runtime in advance 

### Scheduling in interactive systems 

#### 1. Round robin 

- Each process is assigned a CPU running time interval (referred to as <u>quantum</u>)
- If the process uses up quantum
  - CPU preempts at the end of the quantum
- If process is blocked or finished before quantum 
  - CPU switches to the next process 
- Preemptive version of FCFS (FIFO)
- Preemption requires a <u>clock interrupt</u>
- Does not require run times in advance 
- Determining the duration of quantum is critical
  - Quantum too short
    - system is less efficient <== too many process switches (state saving, transfer-of-control, etc.)
  -  Quantum too long
    - system is less responsive ==> poor responses to short interactive requests 

#### 2. Process sharing 

- merge the ready and running; permit all jobs to run at once
- i.e. if there are N jobs in the queue
  - every job runs at a `1/N` speed (compared to its speed if it were running alone)
- Theoretical (in reality one processor runs one process at a time)

#### 3. Priority scheduling 

#### 4. Selfish RR

#### 5. Multiple queues

#### 6. Shortest process next

#### 7. Highest penalty radio next

#### 8. Guaranteed scheduling 

#### 9. Lottery scheduling 

#### 10. Fair share scheduling 





