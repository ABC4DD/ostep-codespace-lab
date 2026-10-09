# Chapter7 CPU Scheduling Assignment Analysis
Metrics definition:
- Response Time = T_firstrun − T_arrival
- Turnaround Time = T_completion − T_arrival

## Q1 Jobs: 8,4,4,4; all arrival time = 0
### FIFO
Manual prediction:
Execution order: Job0(8) → Job1(4) → Job2(4) → Job3(4)
Completion time: 8, 12, 16, 20
Turnaround for each job: 8, 12,16,20
Avg turnaround = (8+12+16+20)/4 = 14.00
Avg response time = 0 (all jobs start immediately at time 0)

Simulator output stored: data/q1_fifo.txt

### SJF (non‑preemptive)
Manual prediction:
Execution order: Job1(4) → Job2(4) → Job3(4) → Job0(8)
Completion time:4,8,12,20
Turnaround:4,8,12,20
Avg turnaround = (4+8+12+20)/4 = 11.00
Avg response = 0

Simulator output stored: data/q1_sjf.txt

Observation Q1:
SJF achieves better(a smaller) average turnaround time compared to FIFO,
because it schedules shorter jobs first when all jobs arrive at the same time.

## Q2 Jobs: lengths 8,4,4,4 ; arrival times: 0,1,1,1
Job0 len=8 arrives t=0; Job1‑3 len=4 arrive t=1
### FIFO
Manual prediction:
Job0 starts running at t=0 and runs until t=8.
After t=8, run job1, job2, job3 sequentially.
Completion:8, 12,16,20
Turnaround:
Job0:8‑0=8
Job1:12‑1=11
Job2:16‑1=15
Job3:20‑1=19
Avg turnaround = (8+11+15+19)/4 = 13.25

### SJF non‑preemptive
Manual prediction:
Non‑preemptive scheduler: once Job0 starts at t=0, it cannot be interrupted,
even though shorter jobs arrive at t=1. Job0 keeps running until finished at t=8.
After t=8, schedule three length‑4 jobs.
Result is exactly same as FIFO in this scenario.
Avg turnaround =13.25.

Simulator files: data/q2_fifo.txt , data/q2_sjf.txt

Observation Q2 (Convoy Effect 车队效应):
SJF is non‑preemptive. A long job already running cannot be swapped out for newly‑arrived short jobs.
Long early‑running jobs make short jobs wait for a long time, degrading overall performance.
This phenomenon is called convoy effect.

## Q3 RR Scheduler, jobs 1,2,3, all arrive t=0
Test quantum size: 1, 2,3,10
Files:
- q3_rr_q1.txt quantum=1
- q3_rr_q2.txt quantum=2
- q3_rr_q3.txt quantum=3
- q3_rr_q10.txt quantum=10

### Manual reasoning
1. Quantum =1: Very small time slice. Each job switches frequently.
Avg response time is very good(low). But many context switches lead to higher average turnaround time.
2. Quantum =2: Larger slice, response time rises slightly, turnaround improves a little.
3. Quantum =3: Time slice equals max job length(3). Less context switch overhead.
4. Quantum =10: Quantum larger than every job burst time. RR will behave exactly like FIFO.
Each job will run to full completion once it gets CPU. Response time becomes worse, turnaround same as FIFO.

Observation Q3:
- Small quantum → better(lower) average response time (good for interactive system), higher turnaround time.
- Large quantum → response time gets worse; when quantum >= maximum job length, RR degenerates into FIFO scheduling.
Trade‑off: quantum size balances response time and turnaround / context‑switch overhead.

## Q4 RR jobs:3,5,9 ; quantum=1,2,4
Files: q4_rr_q1.txt q4_rr_q2.txt q4_rr_q4.txt
Observation:
As quantum increases: average response time increases; average turnaround time decreases.
Every context switch brings invisible overhead in real hardware. Too tiny quantum causes heavy overhead.

## Q5 Convoy‑effect test: jobs 20,1,1,1,1 all arrive t=0
Files: q5_fifo.txt q5_sjf.txt
FIFO order: long job runs first; four tiny jobs wait behind it, bad turnaround.
SJF will re‑order: run four short jobs first then run job of length 20.
Average turnaround time of SJF is significantly better than FIFO here.

### Overall Summary
1. FIFO: simple, no preemption. Suffers from convoy effect. Poor average turnaround when mixed long/short jobs.
2. SJF non‑preemptive: optimal turnaround if all jobs are known in advance. Still suffers convoy‑effect for jobs arriving later.
3. RR preemptive: great response time for interactive workload. Trade‑off: higher turnaround time and context‑switch overhead.
Quantum is critical tuning parameter for RR scheduler.
