## The Tale of the Three Threads and Two Cores: A Threading Dilemma
 
## Overview
This project focuses on optimizing the execution of three threads (T_0, T_1, and T_2) on a system with two physical CPU cores, each supporting two-way simultaneous multithreading (SMT). The goal is to improve performance by setting optimal thread-to-core affinity based on performance metrics collected via the `perf` tool.
 
## Project Structure
The project consists of the following files:
 
- **main.cpp**: Contains the main code logic with modifications in the start_monitoring_for_thread(), stop_monitoring_for_thread(), and get_thread_affinity() functions for monitoring thread performance, analyzing data, and setting thread affinities.
- **work.h**: Header file with required function prototypes and structures.
- **Makefile**: Builds and runs the program.
- **analyze.py**: Python script that parses performance data generated by `perf`, calculates Instruction Per Cycle (IPC) for each thread, and determines the best core affinity.
- **affinity.txt**: File generated by `analyze.py` containing the optimal core assignments for each thread.
- **perf_thread_<index>.txt**: Output files for each thread containing performance data logged by `perf`.
 
 
## Code Components
1. start_monitoring_for_thread()
This function initiates performance monitoring for each thread using perf. It saves the monitoring data into a separate file (perf_thread_<index>.txt) for each thread.
 
2. analyze.py

The analyze.py script determines the optimal thread-to-core affinity. Here’s how it works:
 
-Parse Perf Data: The script reads each perf_thread_<index>.txt file to extract IPC (Instructions per Cycle) values for each thread. 
-It sorts the threads by IPC in ascending order.
-If least two IPC are same then thread with max IPC is unpinned and remaining two threads are on two separate physical cores.
-Else, thread with minimum IPC is given a separate physical core and other two threads share the other physical core.
-Save Results: Finally, it writes these assignments to affinity.txt in the format Thread <index>: <core_id>.
-Run this script directly within main.cpp.

3. get_thread_affinity()
This function reads affinity.txt to retrieve and assign the best core for each thread based on the analysis. It returns the core ID for a given thread or -1 if no specific affinity is needed.
 
## Usage
Run the program using the command:

$ SEED=2xxxx make run

- The program will:
 
Start perf monitoring for each thread.
Run the workload and collect performance data.
Call analyze.py to parse the data and calculate optimal affinities.
Set thread affinities based on affinity.txt.
You can check affinity.txt to see the final affinity assignments:

- Example Output 

Thread 1: 0
Thread 0: 2
Thread 2: 3

## Conclusion
This project illustrates the process of optimizing thread affinity based on performance analysis. By balancing IPC across physical cores, the program aims to reduce contention and improve throughput.
 
## Dependencies
perf: Used for collecting hardware performance data.
python3: Required to run analyze.py.
 
