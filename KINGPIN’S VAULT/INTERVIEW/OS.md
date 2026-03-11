1] What is os?

It acts as an interface between the user and the computer hardware.manages computer hardware, software resources, and provides common services

## 2] Types of operating system 

### 8 types:

***BATCH OS***:
-  **Similar jobs/operations are grouped into batches**
- **One batch is selected at a time**
- Inside that batch, **jobs are executed one by one**
- **No interaction** from the user during execution
- After finishing one batch, the **next batch starts**

***Multiprogramming OS****:
- Multiple jobs are loaded in memory.
- CPU switches to another job when one is waiting (e.g., for I/O).
- Keeps CPU always busy.
example : older UNIX systems

***Time-Sharing / Multitasking OS***:
- CPU time is divided into **fixed ==time slots (time quantum)==**
- **Each process gets one time slot at a time**
- During its time slot, **that process executes**
- When the time slot expires, CPU **switches to the next process**
- This happens very fast → feels like **all processes run together**
example: WINDOWS , LINUX MAC, OS 

==what if the process exceeds the time limit - so that current process's state will be saved and the CPU moves to the next process , and the current process is sent to the last of the queue==

##  eg:

- Time quantum = **4 milliseconds** 
- Processes:
    - P1 → needs 10 ms
    - P2 → needs 5 ms
    - P3 → needs 3 ms

### Step-by-step execution:
### 1️⃣ CPU gives P1 → 4 ms
- P1 still needs 6 ms
- Time quantum expires
- OS **preempts P1**
- P1 goes to ready queue

### 2️⃣ CPU gives P2 → 4 ms
- P2 still needs 1 ms
- Time quantum expires
- OS preempts P2

### 3️⃣ CPU gives P3 → 3 ms
- P3 finishes before time quantum
- No preemption needed

### 4️⃣ CPU goes back to P1 → 4 ms
- P1 now needs 2 ms

### 5️⃣ CPU goes to P2 → 1 ms
- P2 finishes

### 6️⃣ CPU goes to P1 → 2 ms
- P1 finishes


## ****Multiprocessing OS****

- Uses more than one CPU for executing processes.
- Improves system throughput.
- **Example:** Modern servers, multicore systems.

> [!note]+ Difference Between Multi-tasking and Multi-processing OS:
> ```
> 🧠 Core Difference (in simple words)
> 🔹 Multitasking OS
> 
> One CPU
> CPU switches between processes using time slots
> Looks like parallel work, but it’s actually one after another very fast
> 
> 👉 Illusion of parallelism
> 
> 🔹 Multiprocessing OS
> 
> Multiple CPUs / cores
> Processes run at the same time
> No need to wait for time slots
> 
> 👉 Real parallelism
> ```

### 🔹 In Multiprocessing OS

Suppose the system has:

- 4 CPU cores
- 10 processes

Does that mean:

- 10 CPUs are required? ❌ No

Instead:

- At most **4 processes can run simultaneously**
- Remaining processes wait in the **ready queue**    

## 🔹 In a Multi-User OS

There is usually **one physical system** with:

- One main **RAM**
- One set of **CPUs**
- One storage device (SSD/HDD)
- One GPU (if present)
    

All users share these **physical resources**.
But they do NOT directly split RAM manually.
The **Operating System manages and allocates resources dynamically**.
## 🔹 How It Actually Works

When multiple users log in:

1. Each user runs their own processes.
2. OS scheduler divides **CPU time** among all processes.
3. OS memory manager allocates **separate memory spaces** for each process.
4. File system enforces **permissions and isolation**.

So users share hardware, but the OS ensures:

- Process isolation
- Memory protection
- Security boundaries

**Example:** UNIX, Linux servers.

****Distributed OS***:*

Its the reverse of single user OS, i.e. multiple systems are connected togather using networks but it appears to a single system [OS]

Imagine 3 computers connected:

- Computer A → 8GB RAM
- Computer B → 8GB RAM
- Computer C → 8GB RAM

Total = 24GB RAM (but physically separate)

User runs a big task.
Distributed OS:
- Breaks task into parts
- Sends part to A, B, and C
- Collects results
- Shows final output as if one system did it
User doesn’t know which machine did what.

