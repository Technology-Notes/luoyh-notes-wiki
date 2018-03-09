


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