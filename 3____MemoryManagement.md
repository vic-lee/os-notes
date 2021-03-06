# 3. Memory Management

## Table of contents

- [Intro to memory management](#intro-to-memory-management)
- [Memory abstraction](#memory-abstraction)
- [Replacement (Paging, PTE, virtual addresses, TLB)](#replacement)
- [Page Replacement Algorithms](#page-replacement-algorithms)
- [Demand paging systems design issues](#design-issues-for-demand-paging-systems)
- [Demand paging systems implementation issues](#implementation-issues-for-demand-paging-systems)
- [Segmentation](#segmentation)

## Intro to Memory Management 

### Moving up and down

- move data up to a higher level
  - fetch on demand (e.g. **demand paging**)
  - prefetch (read-ahead for I/O)
- when to evict data to a lower level
- simple paging? 
- demand paging? (the book puts simple paging and demand paging together)
- Segmentation? 

### Memory manager 

- the illusion that a process has all the memory to itself 
- MM implements address translation
  - converts virtual address (logical) to physical (real) address
  - ***virtual address***: expressed by the program
  - ***physical address***: actual memory used in hardware 
  - conversion happens every time memory is references 
  - **MMU (Memory Management Unit)** handles the conversion 
    - may be slow due to
      - addition/shift/masking (not too bad)
      - memory references (go to memory to access the mapping) (problematic)
        - solution: create cache translations using a TLB (translation lookaside buffer)

### Loading programs

- **<u>compiler</u>** and **<u>assembler</u>** (returns object module (almost runnable))
- **<u>linker</u>** (linking multiple object modules into one load module)
  - resolve external addresses and adjust relative addresses
- **<u>loader</u>**: load module into memory 

### When is address translation performed? 

1. **At *writing time*** by the programmer (programmer specifies where things go, no longer done)
2. **At *compiler time* by a compiler** 
   1. compiler gives ***physical addresses*** <— we need to know when the compilation unit will be loaded
      1. when the compilation unit is loaded, it goes to the pre-specified loading location, and pushes off content originally at this location. 
   2. Done without a linker; rarely used (MS DOS .COM files)

3. **At *link-edit* time**
   1. Compiler:
      1.  generates ***relative*** (relocatable)
      2. references ***external*** addresses
   2. Linkage editor (at link-edit time)
      1. converts relocatable address [created by compiler] to absolute (relative address relocation)
      2. resolves external references
      3. These by implication requires the linker to know where the program will be loaded 
   3. Cons
      1. a program can only be loaded at where it is specified and cannot be moved 
      2. program cannot be split
      3. loader is trivial; hardware requirements minimal
4. **At *load* time**
   1. similar to link-edit time, **<u>without fixing the starting address</u>**
   2. program can be loaded anywhere
   3. program can be moved, but cannot be split
   4. hardware requirement: base/limit registers
   5. Loader sets the base/limit registers 
   6. no longer common
5. **At *execution* time [Current Solution]**
   1. translated dynamically during execution 
   2. progream <u>can be moved anywhere</u> and can be moved (primary drawback of link- or load-time translation)
   3. program is split into (fix sized) pieces
   4. hardware support for quick virtual to physical address translations
   5. the current model
6. what is the difference between dynamic loading and DLL (dynamic linking)? 
   1. you'd need to recompile for dynamic loading



## Memory abstraction 

- OS in RAM 
- OS in ROM — e.g. embedded systems (no need for flexibility)

### No memory abstraction

- **monoprogramming** — with each request the OS copies the program from disk to memory -> execution 
- no address translation
- new program overrides the old one
- **overlays** are used when memory exceeds the size of physical memory
  - programs broken down to numerous chunks
  - main chunk (root) resides in memory and is called to load / unload various pieces 
  - programmer's responible to ensure a piece is loaded and called 
  - overlays now replaced by dynamic address translations and demand paging 
- using **swapping** (putting an process in and swapping out a process)
  - can support multiple processes to co-exist in physical memory (be careful since each program thinks it has the entire address space but it doesn't, and there's no layer of virtual address for protection)
- **Multiprogramming with fixed partitions [IBM: MFT (Multiprogramming with a Fixed number of Tasks)]** 
  - each partition is monoprogrammed; multiprogramming across partitions
  - <u>partition boundaries not movable</u> 
  - issue:
    - ***large internal fragmentation*** (waste space inside a memory region) because
      - each process is a single segment 
  - no sharing of memory beween processes
  - no dynamic address translation (done during load time)
    - loading should be dine carefully to prevent overriding prior processes
    - read book for more details on storage keys for protection 

### Memory abstraction: address space

- creates an abstract memory for programs to live in 
- facilitates protection and reallocation of processes 

### Base and limit registers

- base and limit registers used to give each process a separate address space 
  - require additional hardware; invisible to programmer 
  - simple version of dynamic reallocation 
- dynamic address translation (add base to relative address)

### Swapping

- Multiprogramming with ***variable partitions*** **(MVP)** (as opposed to MFT's fixed partitions)
- Benefits:
  - No internal fragmentations (since the partition's size can change w/ process size)
  - The number and size of  partitions can change over time
- issues
  - **External fragmentation**: there'll be some space left over (between total memory space and sum(all process size)) (this is because a process is still stored as one piece in memory)
    - solution (Expensive): **memory compaction**: compact all current processes (by moving processes around) to have a contiguous free space
  - program loaded as a whole; process size can change with time  

### Allocation algorithm for swapping

- **placement question**: which partition should we place the process when several partitions are big enough?
  - ![swapping](static/swapping.png)
  - **First fit**: find the first hole that is big enough; break the hole into 2 pieces, one for the process, the other remain as a hole
  - **Next fit**: same as first fit, but next
  - **Best fit**: searches the entire memory to choose the smallest suitable hole (expensive)
  - **Worst fit**: searches the entire memory to choose the largest suitable hole (expensive)
  - **Quick fit**: interesting…see textbook

### 3.2.3 Managing free memory

![bitmap&freelist](static/bitmap&freelist.png)

#### Bitmaps (see b)

- Memory is divided into N blocks; use 1 bit to indicate if that block is occupied / free. 
- Block size (how much memory space does 1 bit represent):
  - too big: internal fragmentation
  - too small: bitmap will be large

#### Free lists (see c)

- Each entry is either a hole or a process (occupied)
- Each entry needs to have 4 attributes
  - Hole / Process
  - Starting address
  - Length
  - Ptr to the next entry
- The linked list is kept in order of starting addresses (i.e. the first entry has starting address 0)
  - Maintaining the list in the same order as the memory makes updating the list easy (except for the head and the tail, an entry will have 2 neighbors and a maximum of 4 scenarios)
    - ![freelist](static/freelist-update.png)

## Replacement

***Replacement***: which process should we swap out when we need to free up some memory? Up to now we have only discussed allocation algorithms (for swapping), but not eviction algorithms.

### Paging

- **Pages**: divide program into fix-sized pieces
- **Page frames**: divide memory space into fix-sized pieces (the same as page size)

### Non-demand paging

- No external fragmentation (since all pages fit perfectly with frames)
- Program is not (need not be) contiguous in memory
- To keep track of where the page is located on the memory (i.e. which frame), we need a **page table**, which in essence maintains a virtual-to-physical mapping
  - ***page number*** = virtual address / page size
  - ***offset*** = virtual address % page size
  - once we looked up the frame number with the page number:
    - ***physical address*** = frame number * frame size (i.e. page size) + offset

#### Properties of non-demand paging (without segmentation)

- the entire process must be in memory (no segmentation)
- physical address is not contiguous
- virtual address remains contiguous
- no external fragmentation
- has internal fragmentation:
  - on average, for each process, *internal fragmentation = 1/2 \* page size*. 

### Virtual memory & Demand paging (fetch on demand)

- only swap in portions of the program that is actively in use (perhaps most of the program is not in memory)
- advantages
  - programs can address memory space that is larger than physical memory
  - the sum of the memory of all running programs can be larger than physical memory (higher multiprogramming capability)
  - more efficient use of memory
    - unused portions of an active program can be evicted to make space for a new program
- disadvantages
  - higher overhead
  - more complicated for the OS

#### Page table 

- Each **page table entry (PTE)** contains:
  - if the page is loaded: frame number 
  - indication that the page is not loaded 
- page table is required for each memory reference:
  - *disadvantages* for putting page tables <u>in memory</u>:
    - ***too slow***: each memory reference requires 2 memory references (1 reference to look up the page table; another for the actual reference).
    - ***too big***: the page table might be monitoring a large chunk of unused virtual memory

#### Page Table Entry (PTE)

![PTE](static/pte.png)

- Components
  - **Page frame number**
  - ***valid bit***: presence / absent in memory
  - ***dirty bit***: if modified
  - ***used bit***: if referenced (read, not necessarily written)
  - ***protection bits***: permission level

- Design issues with paging:
  - mapping from virtual to physical address must be fast 
  - the page table may be large b/c the virtual address space is large 

#### Translation Lookaside Buffer (TLB): solution to quick paging

- ***rationale***: most programs make a ***large*** number of references to a ***small*** number of pages (small virtual memory region)
- an **associative memory** where the *page number* is used as the key to look up PTEs
- **TLB miss** (virtual page is not in the TLB)
  - MMU does an ordinary page table lookup 
  - one of the entries are evicted; if modified (dirty), it is copied back to PTE

### Multi-level page table

revisit…...

## Page replacement algorithms

![PRA Summary](static/pra-summary.png)

### Optimal

### Not recently used (NRU)

- Referenced (M); Modified (M)
- Classes of pages:
  - class 0: R = 0, M = 0
  - class 1: R = 0, M = 1
  - class 2: R = 1, M = 0
  - class 3: R = 1, M = 1
- On *read*: R is set to 1, M is not modified 
- On *write*: R&M are set to 1
- On every clock interrupt, R is reset to 0 (to distinguish between pages recently used and those not recently used)
- **Algorithm**: remove a random page from the lowest nonempty class
  - ***key intuition***: it is better to remove a modified but not recently used class (class 1) than one that is referenced recently but not modified (class 2)

### FIFO

- Maintains a list of pages currently in memory
  - Head is the *least recently arrived* page
  - Tail is the *most recently arrived* page
- **Algorithm**: if page fault (page not found in list), the head page is removed and one new page is added as the tail
- Issue: the removed (oldest) page may still be heavily referenced

### Second Chance

- A modified version of the FIFO that prevents removing oldest page that is still recently referenced
- **Algorithm**: 
  - if the head's R = 1 (referenced in the current clock cycle), 
    - then, set its R to 0, then place at the end of the list as if it has just arrived

### Clock 

![Clock PRA](static/clock-pra.png)

- The pointer points to the oldest page (a simpler implementation of Second Chance (modified FIFO))
- **Algorithm**
  - if R = 0: evict the page (not recently used)
  - else: (R = 1)
    - set R to 0; advance to the next page

### Least Recently Used (LRU)

- When a page fault occurs, remove the page frame that has been unused for the longest time
- Implementation:
  - Either: Keep a timestamp on each PTE tracking when the latest reference time; when evicting, scan the entire page frame table to search for the least recently used one
  - Or: Keep PTEs in a linked list in usage order (tail is least recently used), moving PTE that is referenced to the beginning of the list (according to textbook)
- Why it's impractical in practice
  - It is very costly to, for each memory reference, find the PTE in a list, update it, move it to the front. 

### Not Frequently Used (NFU)

- **Algorithm**
  - Keep a software counter associated with each page
  - At each clock interrupt, for each page, add R (0 or 1) to its counter
  - Reset the R to 0
  - At eviction, remove the page with the lowest count (least frequently used)
- Issue:
  - NFU does not distinguish between old and new references
    - what if a page is just recently swapped in, so it has a relatively low counter value? It has a much higher probability of being swapped out
    - what if there are pages that happen to be in the system for a long time (perhaps they were loaded in first)? Conceivably, their counter becomes higher and higher that it may never be removed

### Aging

![Aging](static/aging-pra.png)

- A modification of the NFU algorithm
- **Algorithm**
  - Keep an `n` bit counter for each page
  - At each clock interrupt,
    - 1) right shift the counter 1 bit
    - 2) add R to the ***left*** most bit of the counter
  - Evict the frame with the lowest counter value
- Benefit: distinguishes old and new references by aging
- Cost: NFU can keep track of 2^n intervals of history; aging can only track n.
  - This, however, is a minor trade-off; say if n = 8 and the counter == 0 (it has not been referenced in 8 clock cycles), the page probably isn't that important

### Working set

### WSClock

## Design issues for demand paging systems

- **Thrashing**: OS is doing very little useful work because it spends a lot of time I/O due to page faults (e.g. spends more time paging than running)
- **Global vs. local policy?**
  - ***global policy*** means we consider all page frames for eviction (not only the frames associated with the process causing the page fault)
  - ***local policy*** means we only evict pages associated with the process causing the page fault; this maintains the same number of pages for each process
  - while local policy may bring about more consistent performances for each process, in general global policy performs better.

### Page Fault Frequency (PFF)

- PFF can be used to approximate the number of frames a process needs
- PFF = #page faults / #references
- When PFF is too low (too many frames), free some frames of this process; 
- When PFF is too high (too little frames), add some frames to this process

### Load Control

- If PFF is too high, we can ***suspend*** some processes (lowering the level of multiprogramming)
  - Swap these processes' frames back to disk to free some new frames, lowering PFF
- if PFF is low, the suspended processes can resume

### Page Size

| | Benefits | Costs |
|--- | -------- | ----- |
| Large Page Size | Lower IO overhead | High internal fragmentation (1/2 page size) |
| Small Page Size | Lower internal fragmentation | Higher IO overhead; bigger page table |

```
overhead = ProcSize / PageSize * PTESize + PageSize / 2			// for large & small

// the 1st term represents IO and space overhead; the smaller the page, the higher this is 
// the 2nd term represents internal fragmentation; the larger the page, the higher this is

Minimizing this function, we find the formula for the optimal page size 
	(considering only IO and internal fragmentation costs):
 
OptimalPageSize = SQRT(2 * ProcSize * PTESize)
```

### Shared Pages

Revisit…...

### Cleaning Policy (Paging Daemon)

- Paging works best if there's an abundant supply of free frames available
- **Paging daemon** is awakened periodically to ensure that:
  - if too little frames are free, it evicts some pages using PRA
  - if an evicted page is needed again before being being overwritten in the frame, the daemon can simply mark the frame as occupied
  - all free frames are clean (un-modified); so the OS would not need to write to disk in a hurry if it runs short of free frames

#### Implementation (in conjunction with PRA)

- **A two-handed clock**
  - the first hand is for ***paging daemons***
    - if it points to a dirty page, it writes the page back to the disk
    - if it points to a clean page, it is just advanced
  - the second hand is for ***PRA***
    - the likelihood of encountering a clean page increases b/c of paging daemon —> faster eviction

## Implementation issues for demand paging systems

### OS involvement w/ Demand Paging

- ***Process creation***	
  - allocate memory for page table; allocate disk space for pages that are not in memory
  - load a few pages from the process
- ***Ready —> Running***
  - MMU and TLB is reset to remove traces of previously executed processes
  - Some hardware registers must be set to point to the page table for the active process
- ***On page fault***
  - 1) determine which viraddr causes the fault
  - 2) find which page needs to be loaded from disk
  - 3) find free frame OR evict a frame (PRA)
  - 4) load the needed page into page frame
  - 5) back up program counter to before the page fault; execute again
- ***On process exit***
  - if certain pages are shared with another process, the page cannot be released until all dependent processes are terminated
  - release the page table
  - release the swap area on disk

### Page Fault Handling (PFH)

1. The hardware traps to the kernel, saving the program counter on stack.
2. An assembly routine starts to save general registers and other volatile information to keep the OS from destroying them. The routine then calls the OS.
3. The OS finds out a PF has occurred —> need to know the virtual address causing the fault. Either a register contains this address; or the OS fetches the instruction causing the fault, then parses for the address.
4. Upon knowing the virtual address, the OS checks if the VA is valid and protection is consistent with the access. If not, the process is killed. If yes, the OS looks for a free frame. If no frame is free, PRA starts.
5. If the page selected for eviction is dirty, the page is scheduled to be written to disk. A context switch happens, blocking the faulting process and letting another one run until the transfer is complete. The frame is marked as `busy` to prevent other processes from modifying it. 
6. Once the page is clean, the OS looks up for the needed page, and schedules a disk operation to bring it in. The faulting process is still suspended. 
7. A disk interrupt notifies the OS that the page has arrived. The page table updates to include the page's location, and the frame is marked as `normal` (resume from `busy`).
8. Reset the program counter to where the fault happened. 
9. The faulting process is scheduled (block —> ready); the OS returns to the assembly routine that called it. 
10. The routine reloads the registers and other state information to continue execution, as if no PF had happened. 

### Locking Pages in Memory

- Consider a process that issued a syscall for I/O purposes: writing to a buffer within that process's address space
- this process is `blocked` and another process is running; the other process may have a page fault
- It is possible for the eviction frame to contain the first process's I/O buffer
- **solution**: locking (pinning) a page that is engaged in I/O activity

### Backing Store

- The process needs to know where the disk address of its swapped partitions are
  - while program text is fixed, the data portion can grow —> must be prepared to grow space on disk
- Or, we can allocate nothing in advance; allocate only when a page is swapped out and deallocate when it is swapped in. 
  - This requires tracking every page on disk (may need a disk map mapping)

## Segmentation

- a **segment** is an independent address space
- segments have different sizes; they can grow and shrink during execution
- Segments are visible to user-level programs; pages are not
- Segments usually do not contain a mixture of types
- Benefits of segmentation (why):
  - to free programmers from managing expanding and contracting tables for the program (what if the symbol table turns out to be much larger than expected, and there's no space for expansion since it is followed by source text?)
  - aid sharing and protection (a segment can be executable; a program can share 1 segment to another but keep the other segments private)
  - faster compilation
- Introduces ***external fragmentation***

#### Addresses

```
[ segment # ][      Offset      ]			// offset < segmentLimit
[ segment # ][ page # ][ offset ]			// segmentation + paging
```

- If segmentation + paging: 
  - Each STE points to the page table for that segment
  - the ***physical size*** of the segment is a multiple of the page size
    - incurs ***internal fragmentation*** in the last page
  - the ***logical size*** of the segment is the limit value in the STE
  - Requires 3 memory references
    - STE, PTE, memory reference