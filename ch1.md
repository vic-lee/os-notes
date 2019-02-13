**2/6/19 — Transfer of Control, Syscall <key module, review this>**
**System call**
* Interface b/t user progs (user mode) and os 
* **System call dispatcher (envelope)**: forward syscall to relevant os components 
* On one hand: *~envelope~* (from user programs); on the other: *~interrupts~* (from hardware)
* **Executing syscall**
	* Calls I/O Library routine (e.g. scanf())
	* Library routine: 
		* calls a small assembler routine that: 
			* moves args and sys call-number to places expected by the OS (e.g. registers)
			* Execute TRAP instruction (user => kernel)

		* OS Carries out work involved with the call

	* Return to user program

		* OS issues RTI (return-from-interrupt) (kernel => user)

		* Assembly routine moves result to where the library routine expects it

		* Assembler routine => library routine 

		* Library routine => user program 

**System calls for process management (fork, etc.)**
* fork() creates complete duplicate of parent process

* Distinguish parent and child process by return value (pid)

	* 0 if child process (usually: if fork() == 0 {} else {})

	* Child’s PID in parent process 

**System calls for directory management**
* Removing files is removing file from the tree 

**Transfer of control** (note that this module does not appear to be in the textbook)
* (syscall, hardware interrupts, page faults)

* Save state, go somewhere else, come back and resume state

* Transfer of controls in user space (**user-level procedure calls**)

	* Synchronous operations (predictable): push on a stack, execute, pop

* Transfer of control in kernel mode (**kernel-level procedure calls**)

	* TRAP instruction for transfer; next instruction is in kernel mode

	* Trap-number: jump to a location in the OS determined by the trap-number 

	* ~Actions by the OS after TRAP:~

		* Jump to actual code 

		* Check all arguments passed 

		* Allocate required space (decrement kernel stack pointer (SP))

		* Start execution from jumped-to location 

	* ~Action by the OS on returning to user:~

		* Store returned value if applicable in the conventional space

		* Deallocate space (increment kernel SP)

		* Execute RTI

			* Kernel mode => user mode 

			* Transfer control back to returned location (saved by TRAP)

	* ~Action by P on return from the OS~

		* Reclaim space used by arguments 

		* Restore value of registers

		* Move to instructions after TRAP

		* Continue P’s execution, using returned values from TRAP, if any

	* Synchronous behavior (transfers from user => kernel => user; predictable)

	* Next instructions executed by P follows TRAP (although other processes may execute *between TRAP and P’s next instruction*)


**2/11/19**
**Operating system structure**
* **I. Monolithic structure — One big program**

	* Structure 

		* Main program that invokes requested service procedure 

		* Service procedures that carry out syscalls 

		* Utility procedures that help service procedures 

	* Issue 

		* Any bug in kernel mode may stop the OS from running 

* **II. Layered System **

	* Layers

		* 5) The operator 

		* 4) user programs 

		* 3) I/O management 

		* 2) Operator-process communication 

		* 1) Memory and drum management

		* 0) Processor allocation and multiprogramming 

	* Idea / benefit 

		* Separation of concerns: each layer does its own job and the other layers assume other layers are functional 

* **III. Micro kernels **

	* Kernel should only handles 1) interrupts, 2) processes, 3) scheduling, 4) interprocess communication

	* Pro: 

		* Minimize kernel-space modules -> minimize crashes 

		* Well defined modules: kernel does **~mechanism~**, user-space does **~policy~**

	* Con: 

		* Process switching is expensive (TRAP, state-saving, etc.)

* **IV. Client-Server Model**

	* For a distributed system (multiple machines)

* **VI. Virtual Machines (not part of this course, FYI)**

	* Hypervisors (Virtual Machine Monitor (VMM)) to switch between multiple OSes 

	* Type 1 vs. Type 2 Hypervisors 

**The Model of Run Time**
* **Text segment**: program code <immutable>

* **Data segment**: 

	* Starts with a certain size 

	* Initialized with initial values 

	* Grow under program control 

* **Stack**: Initial zero, grows and shrinks based on function calls 

