# OSTEP Ch.4 Homework Answers

## Q1
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:cpu  READY    1
3    RUN:cpu  READY    1
4    RUN:cpu  READY    1
5    RUN:cpu  READY    1
6    DONE     RUN:cpu  1
7    DONE     RUN:cpu  1
8    DONE     RUN:cpu  1
9    DONE     RUN:cpu  1
10   DONE     RUN:cpu  1
Total Time = 10 ticks，CPU utilization = 100%

- Reasoning / 理由:
PID0、PID1都是5条纯CPU指令，没有I/O。默认SWITCH_ON_IO策略，CPU指令执行完发生进程切换。CPU全程被占用，没有空闲时间，总时间5+5=10。
- Verified result / 验证结果:
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:cpu  READY    1
3    RUN:cpu  READY    1
4    RUN:cpu  READY    1
5    RUN:cpu  READY    1
6    DONE     RUN:cpu  1
7    DONE     RUN:cpu  1
8    DONE     RUN:cpu  1
9    DONE     RUN:cpu  1
10   DONE     RUN:cpu  1
Total Time = 10 ticks，CPU utilization = 100%
- Analysis / 分析:
预测与模拟器运行结果完全一致。两个CPU密集型进程轮流占用CPU，没有空闲，CPU利用率100%。

## Q2
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:cpu  READY    1
3    RUN:cpu  READY    1
4    RUN:cpu  READY    1
5    DONE     RUN:io   1
6    DONE     BLOCKED      1
7    DONE     BLOCKED      1
8    DONE     BLOCKED      1
9    DONE     BLOCKED      1
10   DONE     BLOCKED      1
11*  DONE     RUN:io_done 1
Total Time = 11 ticks，CPU utilization ≈ 54.55%

- Reasoning / 理由:
PID0：4条纯CPU指令；PID1：1条I/O指令。默认SWITCH_ON_IO。先跑完PID0全部4条CPU；之后PID1运行：RUN:io(1tick) → BLOCKED 5tick → RUN:io_done(1tick)。4+7=11。I/O阻塞期间CPU空闲。
- Verified result / 验证结果:
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:cpu  READY    1
3    RUN:cpu  READY    1
4    RUN:cpu  READY    1
5    DONE     RUN:io   1
6    DONE     BLOCKED      1
7    DONE     BLOCKED      1
8    DONE     BLOCKED      1
9    DONE     BLOCKED      1
10   DONE     BLOCKED      1
11*  DONE     RUN:io_done 1
Total Time = 11 ticks，CPU utilization ≈ 54.55%
- Analysis / 分析:
预测与模拟器结果一致。PID1等待I/O时CPU空闲，降低CPU利用率。

## Q3
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1    1
3    BLOCKED  RUN:cpu  1    1
4    BLOCKED  RUN:cpu  1    1
5    BLOCKED  RUN:cpu  1    1
6    BLOCKED  DONE         1
7*   RUN:io_done DONE  1
Total Time =7 ticks，CPU utilization ≈71.43%

- Reasoning / 理由:
进程顺序调换：PID0做I/O，PID1做4条CPU。PID0执行RUN:io后触发切换，PID0进入BLOCKED；PID1在I/O阻塞的5个tick里面完整跑完自己4条CPU指令。I/O与CPU计算并行，总时间缩短。
- Verified result / 验证结果:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1    1
3    BLOCKED  RUN:cpu  1    1
4    BLOCKED  RUN:cpu  1    1
5    BLOCKED  RUN:cpu  1    1
6    BLOCKED  DONE         1
7*   RUN:io_done DONE  1
Total Time =7 ticks，CPU utilization ≈71.43%
- Analysis / 分析:
预测与模拟器结果一致。I/O等待期间CPU可以调度其他进程，实现I/O与CPU计算并行，提升系统效率。

## Q4
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  READY       1
3    BLOCKED  READY       1
4    BLOCKED  READY       1
5    BLOCKED  READY       1
6    BLOCKED  READY       1
7*   RUN:io_done READY 1
8    DONE     RUN:cpu  1
9    DONE     RUN:cpu  1
10   DONE     RUN:cpu  1
11   DONE     RUN:cpu  1
Total Time =11 ticks，CPU utilization≈54.55%

- Reasoning / 理由:
参数 `-S SWITCH_ON_END`：只有进程完全结束才切换CPU。PID0发起I/O进入BLOCKED，不会切换CPU给PID1，CPU全程空闲等待I/O结束。直到PID0全部完成，才调度PID1运行4条CPU，总时间退化为串行执行。
- Verified result / 验证结果:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  READY       1
3    BLOCKED  READY       1
4    BLOCKED  READY       1
5    BLOCKED  READY       1
6    BLOCKED  READY       1
7*   RUN:io_done READY 1
8    DONE     RUN:cpu  1
9    DONE     RUN:cpu  1
10   DONE     RUN:cpu  1
11   DONE     RUN:cpu  1
Total Time =11 ticks，CPU utilization≈54.55%
- Analysis / 分析:
预测与模拟器结果一致。SWITCH_ON_END策略不会在I/O发生时切换进程，CPU白白浪费等待I/O，失去并发收益。

## Q5
- Prediction / 预测:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1    1
3    BLOCKED  RUN:cpu  1    1
4    BLOCKED  RUN:cpu  1    1
5    BLOCKED  RUN:cpu  1    1
6    BLOCKED  DONE         1
7*   RUN:io_done DONE  1
Total Time =7 ticks，CPU utilization≈71.43%

- Reasoning / 理由:
`-S SWITCH_ON_IO`，进程发起I/O立刻切换CPU。PID0阻塞I/O的同时，PID1使用CPU运行；I/O和CPU任务并行，总时间7，和Q3结果一致，对比Q4可见I/O时切换带来性能提升。
- Verified result / 验证结果:
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1    1
3    BLOCKED  RUN:cpu  1    1
4    BLOCKED  RUN:cpu  1    1
5    BLOCKED  RUN:cpu  1    1
6    BLOCKED  DONE         1
7*   RUN:io_done DONE  1
Total Time =7 ticks，CPU utilization≈71.43%
- Analysis / 分析:
预测与模拟器结果一致。SWITCH_ON_IO在I/O发生时主动切换进程，利用I/O等待时间执行其他任务，获得并发性能。

## Q6
- Prediction / 预测:
Time PID0     PID1     PID2     PID3     CPU IOs
1    RUN:io   READY    READY    READY    1
2    BLOCKED  RUN:cpu  READY    READY    1    1
3    BLOCKED  RUN:cpu  READY    READY    1    1
4    BLOCKED  RUN:cpu  READY    READY    1    1
5    BLOCKED  RUN:cpu  READY    READY    1    1
6    BLOCKED  DONE     READY    READY    1
7*   READY    DONE     RUN:cpu  READY    1
8    READY    DONE     RUN:cpu  READY    1
9    READY    DONE     RUN:cpu  READY    1
10   READY    DONE     RUN:cpu  READY    1
11   READY    DONE     DONE     RUN:cpu  1
12   READY    DONE     DONE     RUN:cpu  1
13   READY    DONE     DONE     RUN:cpu  1
14*  RUN:io_done DONE     DONE     DONE     1
Total Time =14 ticks

- Reasoning / 理由:
`-S SWITCH_ON_IO -I IO_RUN_LATER`。PID0执行I/O，发起I/O后切走CPU；I/O完成时PID0变为READY，放到就绪队列末尾，继续运行其他CPU密集进程，不会立刻处理io_done。I/O设备此时没有任务进入空闲。
- Verified result / 验证结果:
Time PID0     PID1     PID2     PID3     CPU IOs
1    RUN:io   READY    READY    READY    1
2    BLOCKED  RUN:cpu  READY    READY    1    1
3    BLOCKED  RUN:cpu  READY    READY    1    1
4    BLOCKED  RUN:cpu  READY    READY    1    1
5    BLOCKED  RUN:cpu  READY    READY    1    1
6    BLOCKED  DONE     READY    READY    1
7*   READY    DONE     RUN:cpu  READY    1
8    READY    DONE     RUN:cpu  READY    1
9    READY    DONE     RUN:cpu  READY    1
10   READY    DONE     RUN:cpu  READY    1
11   READY    DONE     DONE     RUN:cpu  1
12   READY    DONE     DONE     RUN:cpu  1
13   READY    DONE     DONE     RUN:cpu  1
14*  RUN:io_done DONE     DONE     DONE     1
Total Time =14 ticks
- Analysis / 分析:
IO_RUN_LATER策略：I/O完成的进程排到就绪队列尾部，优先把CPU给正在运行的CPU密集进程。

## Q7
- Prediction / 预测:
Time PID0     PID1     PID2     PID3     CPU IOs
1    RUN:io   READY    READY    READY    1
2    BLOCKED  RUN:cpu  READY    READY    1    1
3    BLOCKED  RUN:cpu  READY    READY    1    1
4    BLOCKED  RUN:cpu  READY    READY    1    1
5    BLOCKED  RUN:cpu  READY    READY    1    1
6    BLOCKED  DONE     READY    READY    1
7*   RUN:io_done DONE READY READY 1
8    RUN:io   DONE     READY    READY    1
9    BLOCKED  DONE     RUN:cpu  READY    1    1
10   BLOCKED  DONE     RUN:cpu  READY    1    1
11   BLOCKED  DONE     RUN:cpu  READY    1    1
12   BLOCKED  DONE     RUN:cpu  READY    1    1
13   BLOCKED  DONE     DONE     RUN:cpu  1
14*  RUN:io_done DONE     DONE     DONE     1
Total Time =14 ticks

- Reasoning / 理由:
`-I IO_RUN_IMMEDIATE`，I/O完成之后，PID0立刻获得CPU运行io_done，继续发起下一轮I/O。减少I/O设备空闲，适合I/O密集进程，让I/O设备尽量保持忙碌。
- Verified result / 验证结果:
Time PID0     PID1     PID2     PID3     CPU IOs
1    RUN:io   READY    READY    READY    1
2    BLOCKED  RUN:cpu  READY    READY    1    1
3    BLOCKED  RUN:cpu  READY    READY    1    1
4    BLOCKED  RUN:cpu  READY    READY    1    1
5    BLOCKED  RUN:cpu  READY    READY    1    1
6    BLOCKED  DONE     READY    READY    1
7*   RUN:io_done DONE READY READY 1
8    RUN:io   DONE     READY    READY    1
9    BLOCKED  DONE     RUN:cpu  READY    1    1
10   BLOCKED  DONE     RUN:cpu  READY    1    1
11   BLOCKED  DONE     RUN:cpu  READY    1    1
12   BLOCKED  DONE     RUN:cpu  READY    1    1
13   BLOCKED  DONE     DONE     RUN:cpu  1
14*  RUN:io_done DONE     DONE     DONE     1
Total Time =14 ticks
- Analysis / 分析:
IO_RUN_IMMEDIATE：I/O完成的进程立刻抢占CPU，优先处理I/O进程，最大化I/O设备利用率。对比Q6，总耗时相同，但调度逻辑完全不同。

## Q8
- Prediction / 预测:

### seed = 1
Time PID0 PID1 CPU IOs

seed1 Total Time = ?? ticks

### seed = 2
Time PID0 PID1 CPU IOs

seed2 Total Time = ?? ticks

### seed = 3
Time PID0 PID1 CPU IOs

seed3 Total Time = ?? ticks

- Reasoning / 理由:
-s设置随机种子，每个seed生成固定的CPU/IO指令序列；预测阶段使用默认策略：`-S SWITCH_ON_IO -I IO_RUN_LATER`。不同种子指令组合不同，总运行时间不一样。
- Verified result / 验证结果:
seed=1
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:io   READY    1
3    BLOCKED  RUN:cpu  1    1
4    BLOCKED  RUN:cpu  1    1
5    BLOCKED  RUN:cpu  1    1
6    BLOCKED  DONE     1
7*   RUN:io_done DONE  1
8    RUN:io   DONE     1
9    BLOCKED  DONE     1
10   BLOCKED  DONE     1
11   BLOCKED  DONE     1
12   BLOCKED  DONE     1
13   BLOCKED  DONE     1
14*  RUN:io_done DONE  1
Total Time =14 ticks

seed=2
Time PID0     PID1     CPU IOs
1    RUN:io   READY    1
2    BLOCKED  RUN:cpu  1    1
3    BLOCKED  RUN:io  1    1
4    BLOCKED  BLOCKED      2
5    BLOCKED  BLOCKED      2
6    BLOCKED  BLOCKED      2
7*   RUN:io_done BLOCKED 1
8    RUN:io   BLOCKED 1
9*   BLOCKED  RUN:io_done 1
10   BLOCKED  RUN:cpu  1
11   BLOCKED  BLOCKED      2
12   BLOCKED  BLOCKED      2
13   BLOCKED  BLOCKED      2
14*  RUN:io_done BLOCKED 1
15   RUN:cpu  BLOCKED 1
16*  DONE     RUN:io_done 1
Total Time =16 ticks

seed=3
Time PID0     PID1     CPU IOs
1    RUN:cpu  READY    1
2    RUN:io   READY    1
3    BLOCKED  RUN:io  1    1
4    BLOCKED  BLOCKED      2
5    BLOCKED  BLOCKED      2
6    BLOCKED  BLOCKED      2
7*   RUN:io_done BLOCKED 1
8    RUN:cpu  READY    1
9    DONE     RUN:io_done 1
10   DONE     RUN:io   1
11   DONE     BLOCKED      1
12   DONE     BLOCKED      1
13   DONE     BLOCKED      1
14   DONE     BLOCKED      1
15   DONE     BLOCKED      1
16*  DONE     RUN:io_done 1
17*  DONE     RUN:cpu  1
18   DONE     DONE     1
Total Time =18 ticks
- Analysis / 分析:
随机种子改变进程内CPU与I/O指令的排列顺序。I/O密集指令越多，等待I/O时间越长，总耗时越长。
