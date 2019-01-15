


### jps 查看Java进程

### jmap

```
# 查看JVM内存堆情况
> jmap -head PID

# 查看JVM对象实例信息
> jmap -histo PID

```


### 编译器相关

```
VM arguments:
-server -XX:[+-]DoEscapeAnalysis   # 逃逸分析

-server -XX:[+-]TieredCompilation  # 分层编译

-XX:CompileThreshold = 10000       # 设置执行JIT阈值

-XX:[+-]PrintCompilation           # 打印编译过程


```


### 查看内存回收情况

```
> jstat -gcutil PID [interval]  # jstat -gcutil 129413 1000
output:
  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT   
 25.00   0.00  86.72  89.83  67.47   3834   14.079     1    0.282   14.361
 25.00   0.00  86.72  89.83  67.47   3834   14.079     1    0.282   14.361
 25.00   0.00  86.95  89.83  67.47   3834   14.079     1    0.282   14.361
 25.00   0.00  87.17  89.83  67.47   3834   14.079     1    0.282   14.361
  0.00  25.00  64.70  89.83  67.47   3835   14.083     1    0.282   14.364
  0.00  25.00  64.72  89.83  67.47   3835   14.083     1    0.282   14.364

description:
    S0/S1: survivor
    E: eden
    O: 老年代
    P: 永久代
    YGC: 年轻代GC次数
    YGCT: 年轻代GC共消耗时间(s)
    FGC: Full GC次数
    FGCT: Full GC共消耗时间(s)
    GCT: GC共消耗时间(s)

```

### 定位异常代码

```

# CPU 100%
#> jps # top
#> top -Hp PID
#> printf '%x\n' TID
#> jstack PID >> 0.dump
#> vim 0.dump
$> /TID

```