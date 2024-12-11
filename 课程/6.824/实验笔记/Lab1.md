# Lab1 MapReduce

## master/worker通信机制

1. Master 启动并等待 RPC 请求。(server, 框架已提供)
2. Worker 启动并连接到 Master。
3. Master 分配任务给 Worker。
4. Worker 执行任务并报告结果。
5. Master 更新任务状态。

重复上述过程直到所有任务完成。

## 心跳机制

当worker上次心跳时间过长时, 判定为worker die(设置worker状态)

- 写状态

创建一个线程, 遍历worker状态, 当发现worker die时, 将其对应的task设置为idle

- 读状态, 写task

RPC方法: Task&TaskCompletion

- 读写task

InitTask: 写Task

**注意**: 线程遍历worker状态时会读写共享的worker状态(progressing / die)和task状态(idleTasks / inProgressTasks 切片), 注意数据竞争&一致性

锁的粒度:

- 线程A循环扫描Workers, 按照心跳时间去写Worker的status, 多个RPC请求线程写Worker的LastHeartbeat
- 如果对整个Workers上锁
  - 缺点: 多个RPC需要相互等待
  - 优点: 减少了线程A的Lock/UnLock次数
- 对Worker分别上锁
  - 缺点: A需要多次Lock/UnLock, 消耗一些空间去存锁(小事)
  - 优点: 多个RPC不需要同步

## 锁

好复杂 要疯了 随便记点吧

```go
  type Task struct {
 TaskNum    int
 TaskType   string   // "map" or "reduce" or "wait" or "done"
 TaskStatus string   // "未分配" or "进行中" or "已完成"
 InputFiles []string // reduce的时候是多个文件
 OutputFile string
}

type WorkerHeartbeat struct {
 Status        string
 LastHeartbeat time.Time
 mu            sync.Mutex
}

type Coordinator struct {
 Workers             []WorkerHeartbeat
 Tasks               []Task
 WorkerID_TaskID_Map map[int]int
 JobStatus           string // "map" or "reduce" or "done"
 NMap                int
 NReduce             int

 // 待分配任务
 idleTasks []int
 // 正在进行的任务
 inProgressTasks []int
 // 已完成的任务
 completedTasks []int

 // 锁
 jobStatusRWMu sync.RWMutex
 taskRWMu      sync.RWMutex
 workersMu     sync.Mutex
}
```

`jobStatusRWMu`锁`JobStatus`

`workersMu`锁 影响整个`Workers`的操作(增删)

`WorkerHeartbeat.mu` 独立的锁 针对结构体内部字段的读写

`taskRWMu`锁 task相关业务数据 (一把大锁保平安)

- 三个状态切片: `idleTasks`, `inProgressTasks`, `completedTasks`
- `Tasks`的读写
- `WorkerID_TaskID_Map`的读写

对`WorkerID_TaskID_Map`的访问只锁

### LoggingMutex && LoggingMutexMap

> 为了检查上锁情况的日志打印

增加功能: 统计上锁, 释放锁的时机

实现方式: 定义了一个`mytool.LoggingMutex`结构体, 内部封装了`sync.Mutex`, 重写`Lock`和`Unlock`方法, 在其中调用`sync.Mutex`的`Lock`和`Unlock`方法, 并在调用前后打印日志

### 一些锁使用的教训

最好将需要上锁的数据结构封装成类似树的节点结构, 每个需要上锁的节点(子结构)都设置一个锁, 方便管理
不然就像上面要记忆很多锁

但这样隐藏锁到结构体内部, 可能会在相互调用时出现死锁, 需要注意
