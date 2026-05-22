# Real-Time CPU Scheduler Simulator

A desktop graphical user interface (GUI) application developed to simulate and visualize the behavior of fundamental CPU scheduling algorithms in real time. The system calculates core scheduling metrics—including **Average Waiting Time** and **Average Turnaround Time**—and renders live Gantt charts to visually demonstrate how processes are managed by the operating system kernel.

---

## 👥 Team Members & Roles

| Name | ID | Core Contributions & Project Roles |
| :--- | :---: | :--- |
| **Lobna Ahmed Abd Elhamid** | 2300360 | Implemented Non-Preemptive SJF & automated Gantt chart idle-case detection. |
| **Mohamed Amgad** | 2300459 | Designed & implemented the Qt Graphical User Interface and header file architecture. |
| **Anas Ashraf Badawy** | 2300044 | Implemented FCFS algorithm, performance calculation pipelines, and metric averages. |
| **Mariam Ahmed Ali** | 2300506 | Developed Round Robin algorithm, enhanced input validation, and compiled the `.exe`. |
| **Youssef Ahmed Hassan Fakhry** | 2300551 | Implemented Preemptive SJF and Preemptive Priority scheduling algorithms. |
| **Youssef Mohamed Ahmed Eladawy** | 2300578 | Handled live Gantt chart rendering, optimized time complexity, and built Non-Preemptive Priority. |

---

## Project Overview

This simulator provides cross-platform desktop visualization built on the **Qt framework**. It supports microsecond-level precision by allowing process parameters to be entered as floating-point values. The UI adapts dynamically based on the selected algorithm, revealing or locking parameter input boxes as needed to ensure an optimal user experience.

### Supported Scheduling Algorithms
1. **First Come First Serve (FCFS):** Traditional non-preemptive ordering based strictly on arrival queues.
2. **Shortest Job First (SJF):** Supports both **Preemptive (SRTF)** and **Non-Preemptive** variants.
3. **Priority Scheduling:** Supports both **Preemptive** and **Non-Preemptive** modes (Lower priority number indicates higher execution precedence).
4. **Round Robin (RR):** Implements time-sliced fair execution with a configurable time quantum.

---

## Core Operations & Modes

The scheduling engine operates in two operational execution modes:

### 1. Live Scheduling Mode
The scheduler executes in simulated real time ($1 \text{ tick} = 1 \text{ second}$), updating dynamically via timer events. The user interface updates the following states in real time:
* **Process State Tracking:** Evaluates if a process is *Waiting*, *Ready*, *Running*, or *Completed*.
* **Time Tracking:** Displays real-time changes in remaining burst times.
* **Gantt Chart Animation:** Renders live execution timelines block-by-block.

### 2. Batch Mode (Non-Live)
Executes all processes in the job queue instantly without real-time delay. The application calculates the complete lifecycle immediately and displays the finished Gantt chart and average statistics directly.

---

## Input Validation Matrix

To avoid computational exceptions or layout issues, inputs undergo strict validation filters:
* **Burst Time Validation:** Rejects values $\le 0$.
* **Arrival Time Validation:** Rejects negative time bounds.
* **Priority Validation:** Enforced only during Priority selection.
* **Quantum Time Validation:** Enforced only during Round Robin selections; rejects values $\le 0$.
* **Empty Process List Validation:** Disables execution commands if the queue contains no valid instances.

---

## UI Engineering & Interface Controls

The Qt graphical user interface coordinates the simulation lifecycle by mapping interactive widgets directly to core engine queues:

* **Queue Handlers:** Validated additions are pushed onto the `Job Queue` ordered by arrival times and tracked via a dynamic process overview table grid.
* **Control Buttons:**
  * `Start`: Triggers real-time simulations via internal timers or evaluates batch sequences instantly.
  * `Pause`: Halts active execution loops without destroying current states.
  * `Reset`: Clears the process matrix table, flushes queues, and returns timers to zero.
* **Dynamic Form Locking:** The application enables input parameters selectively (e.g., the Time Quantum field is accessible only during Round Robin configuration). To prevent runtime corruption, configuration selectors are strictly disabled once simulation execution begins.

---

## Algorithmic Implementations

### 1. First Come First Serve (FCFS)
* **Design Pattern:** Uses a non-preemptive first-in, first-out (FIFO) pipeline.
* **Execution Flow:** Processes inside the `Job Queue` transfer onto the `Ready Queue` as the elapsed simulation timer reaches their arrival threshold. The CPU executes the head element to completion without interruption, recording start and teardown times sequentially. If the `Ready Queue` runs dry, the system shifts into a verified *Idle* state.

### 2. Shortest Job First (SJF)
* **Design Pattern:** Utilizes two prioritized lookup structures: a standard arrival-ordered `Job Queue` and an execution-priority `SJF Ready Queue` sorted by remaining burst time. Equal burst lengths fallback to FCFS logic for tie-breaking.
* **Non-Preemptive Mode:** The selected shortest job retains absolute CPU access until completion.
* **Preemptive Mode:** Checks conditions at every arrival step. If a newly available process exhibits a shorter remaining burst time than the current executing task, the running process is preempted and pushed back to the `Ready Queue`. The scheduler resets its execution index to evaluate the new shortest job on the next tick.

### 3. Priority Scheduling
* **Design Pattern:** Mimics the structural implementation of the SJF priority layout, swapping out burst-time sorting in favor of absolute priority values (where smaller integer keys indicate higher execution priority). The UI dynamically opens priority input boxes upon selection.
* Supports both **Preemptive** and **Non-Preemptive** structural modes based on relative queue rankings.

### 4. Round Robin (RR)
* **Design Pattern:** Enforces preemption via cyclic time-slice allocation.
* **Execution Flow:** Arriving processes enter a FIFO `Ready Queue`. The execution engine tracks context switching against a configurable Time Quantum limit. If a process does not complete within its time slice, its remaining burst time is updated, and it is appended to the back of the queue.
* **Boundary Condition Fix:** If an external process enters the system at the exact instant a running process completes its time slice, the newly arrived process is pushed into the `Ready Queue` *before* the preempted task to preserve fairness.

---

## Performance Metrics

The engine evaluates scheduling performance using standard operating system criteria:

$$\text{Turnaround Time (TAT)} = \text{Completion Time} - \text{Arrival Time}$$

$$\text{Waiting Time (WT)} = \text{Turnaround Time} - \text{Burst Time}$$

Final calculation fields output the aggregate average ratings ($\sum \text{WT} / n$ and $\sum \text{TAT} / n$) directly on the interface window screen upon terminal task completion.
