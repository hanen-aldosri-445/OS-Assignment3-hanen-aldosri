# Assignment 3 - Complete Documentation

**Student Name**: [Hanen Abdullah Aldosri]  
**Student ID**: [445052141]  
**Date Submitted**: [7 May]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [30 April, 10:00 PM]
**What I implemented**: I started the project by implementing the basic idea of Round Robin scheduling. I created processes (threads), assigned burst time, and added them to the ready queue.

**Challenges encountered**: I had difficulty understanding how processes move in the queue and how they return after execution.

**How I solved it**: I reviewed examples and followed the process flow step by step until I understood it.

**Testing approach**: I ran the program and observed the execution order of processes.


**Time spent**: About 3 hours.

---

### Entry 2 - [1 May, 4:30 AM]
**What I implemented**: I added a Semaphore to control access to the CPU so only one process runs at a time

**Challenges encountered**: There was a conflict between processes (race condition).

**How I solved it**:I used a Semaphore to make sure only one process accesses the CPU. 

**Testing approach**:  tested with multiple processes running at the same time.

**Time spent**: About 2 hours

---

### Entry 3 - [2 May, 7:00 PM]
**What I implemented**: I added a ReentrantLock to protect shared data like the ready queue and process states.

**Challenges encountered**: Shared data was getting incorrect due to multiple threads accessing it.

**How I solved it**: I used a lock before modifying shared data and released it after finishing

**Testing approach**:I monitored values during execution to make sure they were correct. 

**Time spent**:About 2 hours. 

---

### Entry 4 - [6 May, 2:00 AM]
**What I implemented**:I completed the project by adding statistics like completed processes, waiting time, and context switching.
 
**Challenges encountered**:It was difficult to calculate some values accurately during execution 

**How I solved it**:I modified the code several times and tested with different inputs until results were correct. 

**Testing approach**:I compared the output with expected Round Robin behavior. 

**Time spent**: About 2–3 hours.

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[One race condition occurs when multiple threads try to update the ready queue at the same time. The shared resource here is the ready queue, and concurrent access can cause inconsistent order or lost processes. For example, two threads may try to add or remove processes at the same time, leading to incorrect scheduling behavior.

Another race condition happens when updating shared variables like remaining time or counters. The shared resource is the process state or counters, and concurrent access may result in incorrect values. For example, one thread may overwrite another thread’s update, causing wrong execution results or statistics.
]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[ReentrantLock is used to protect shared data by allowing only one thread to access a critical section at a time, while Semaphore is used to control access to a resource by multiple threads. In my code, I used Semaphore to control access to the CPU so only one process runs at a time. I used ReentrantLock to protect shared resources like the ready queue and process data. I chose this because Semaphore is suitable for resource control, while Lock is better for protecting critical sections.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock is a situation where two or more threads are waiting for each other indefinitely and none can continue execution. One prevention technique is using a consistent lock ordering, so threads always acquire locks in the same order. Another technique is using try-finally blocks to ensure locks are always released. In my code, I used locks carefully and released them in finally blocks to avoid holding locks forever. This helped prevent deadlocks during execution.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I used separate locks for each counter (fine-grained locking). I chose this approach because the three counters are independent, so each one can be updated without affecting the others. This improves concurrency because multiple threads can update different counters at the same time.

The trade-off is that fine-grained locking is more complex to manage compared to using one lock for all counters. Coarse-grained locking is simpler but reduces performance because only one thread can access all counters at once. Since the counters are independent, fine-grained locking provides better concurrency and efficiency in this case.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount
completedProcessCount
totalWaitingTime

**Why they need protection**: These variables are shared among multiple threads and are updated concurrently inside methods like:
incrementContextSwitch()
incrementCompletedProcess()
addWaitingTime()
Without synchronization, race conditions may occur, leading to incorrect results.

**Synchronization mechanism used**: ReentrantLock
(contextSwitchLock, completedProcessLock, waitingTimeLock)

**Code snippet**:
```java
// Paste your implementation here
```contextSwitchLock.lock();
try { contextSwitchCount++; }
finally { contextSwitchLock.unlock(); }

completedProcessLock.lock();
try { completedProcessCount++; }
finally { completedProcessLock.unlock(); }

waitingTimeLock.lock();
try { totalWaitingTime += time; }
finally { waitingTimeLock.unlock(); }

**Justification**: Each variable is protected by its own lock to ensure thread-safe updates and better concurrency (fine-grained locking).

---

### Critical Section #2: Execution Log

**What resource**: executionLog (ArrayList)

**Why it needs protection**:
Multiple threads call:
executionLog.add(message);
and since ArrayList is not thread-safe, this may cause inconsistent or 
corrupted data. 

**Synchronization mechanism used**: 
ReentrantLock (logLock)

**Code snippet**:
```java
// Paste your implementation here
```
logLock.lock();
try { executionLog.add(message); }
finally { logLock.unlock(); }

**Justification**: 
The lock ensures that only one thread writes to the log at a time, preserving data integrity.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To control access to the CPU so that only one process executes at a time.

**Number of permits and why**: 
public static final Semaphore cpuSemaphore = new Semaphore(1);
1 permit → represents a single CPU

**Where implemented**: 
Inside run() method in Process class

**Code snippet**:
```java
// Paste your implementation here
```
SharedResources.cpuSemaphore.acquire();
try {
    // process execution
} finally {
    SharedResources.cpuSemaphore.release();
}

**Effect on program behavior**: 
Ensures that only one process uses the CPU at a time, preventing conflicts and simulating real CPU scheduling.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: 
Running the program multiple times to verify consistent results

**Testing procedure**: 
I ran the program 5 times using:

javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

**Results**: 
In all runs:
The program executed correctly without errors
The number of completed processes was always correct
No missing or duplicated log entries
Values like contextSwitchCount and totalWaitingTime were reasonable and consistent (no abnormal values)

**Why synchronization is necessary**: 
Even if errors did not appear every time, race conditions could occur without synchronization:
Multiple threads may update contextSwitchCount, completedProcessCount, and totalWaitingTime at the same time → leading to incorrect values
Multiple threads writing to executionLog (ArrayList) could corrupt the data or cause runtime errors
Without cpuSemaphore, more than one process could access the CPU simultaneously, breaking the scheduling logic

**Conclusion**: 
Synchronization ensures stable, correct, and consistent results across multiple executions.
---

### Test 2: Exception Testing
**What I tested**: 
Checking for ConcurrentModificationException.
**Testing procedure**: 
I ran the program multiple times under normal conditions
Observed logging behavior where multiple threads write to executionLog

**Results**: 
No ConcurrentModificationException occurred
The program ran smoothly every time

**What this proves**: 
Using ReentrantLock (logLock) successfully protects the executionLog, ensuring thread-safe operations and preventing concurrent modification issues.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values such as:
Total burst time
Number of completed processes
Context switches

**Expected values**: 
All processes should complete execution
completedProcessCount should equal total number of processes
contextSwitchCount should reflect actual switching behavior
totalWaitingTime should be a positive and logical value

**Actual values**: 
All processes completed successfully
Counters matched expected behavior
No negative or incorrect values observed

**Analysis**: 
The synchronization mechanisms (locks + semaphore) ensured accurate updates of shared variables, resulting in correct final outputs.
---

### Test 4: Different Scenarios
**Scenario tested**: [Changing time quantum and number of processes.]

**Purpose**: 
To verify that the scheduler works correctly under different conditions and workloads.
**Results**: 
The program adapted correctly to different inputs
Scheduling behavior changed as expected (more context switches with smaller quantum)
No errors or inconsistencies occurred
**What I learned**: 
The synchronization design is robust and works correctly under different scenarios, ensuring safe concurrent execution and accurate scheduling.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[Through this assignment, I learned that synchronization is very important when multiple threads or processes access shared resources at the same time. Without proper synchronization, problems like race conditions can occur, which lead to incorrect results. I understood that critical sections must be protected to ensure that only one thread can access them at a time. I also learned about different synchronization mechanisms such as mutexes and semaphores and how they help in controlling access to shared data. One challenge I faced was identifying which parts of the code actually need protection. Another insight I gained is that too much synchronization can slow down performance, so it should be used carefully. Overall, synchronization helps maintain data consistency and program correctness.
]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking systems: When multiple users try to withdraw or deposit money at the same time, synchronization ensures that the account balance is updated correctly without errors.

**Example 2**: 
Printing systems: When several users send print jobs to the same printer, synchronization ensures that the jobs are handled one by one without mixing or crashing.
---

### How I would explain synchronization to others:

[I would explain synchronization as a way to organize the use of a shared resource. For example, if there is only one printer in an office and multiple people want to print, they cannot all send their print jobs at the same time without order. There must be a system that allows each job to be processed one at a time. In programming, synchronization works in the same way by controlling how processes or threads access shared resources and preventing conflicts between them. This ensures that each task is executed correctly without errors or interference.
]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 
14 commits
**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 
Around 8–10 hours
**Key takeaways**: 
1.Importance of synchronization
2.How to avoid race conditions
3.Using mutex and semaphores


**Most challenging aspect**: 
Identifying critical sections in the code
**What I'm most proud of**: 
Successfully implementing synchronization without errors

---

**End of Documentation**
