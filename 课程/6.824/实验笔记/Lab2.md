# Lab 2: Key/Value Server

## Introduction

在本次 Lab 中，你将在单机上构建一个键/值服务器，以确保即使网络出现故障，每个操作也只能执行一次，并且操作是可线性化的。

客户端可以向键/值服务器发送三个不同的 RPC： `Put(key, value)`、 `Append(key, arg)` 和`Get(key)` 。服务器在内存中维护键/值对的map。键和值是字符串。 `Put(key, value)`设置或替换map中给定键的值，`Append(key, arg)` 将 arg 附加到键的值并返回旧值，`Get(key)` 获取键的当前值。不存在的键的 Get请求应返回空字符串；对于不存在的键的 Append 请求应该表现为现有值是零长度字符串。每个客户端都通过结构体`Clerk`的 Put/Append/Get 方法与服务器进行通信。 `Clerk` 管理与服务器的 RPC 交互。

你的服务器必须保证应用程序对`Clerk` Get/Put/Append 方法的调用是线性一致的。 如果客户端请求不是并发的，每个客户端 Get/Put/Append 调用时能够看到之前调用序列导致的状态变更。 对于并发的请求来说，返回的结果和最终状态都必须和这些操作顺序执行的结果一致。如果一些请求在时间上重叠，则它们是并发的：例如，如果客户端 X 调用 `Clerk.Put()` ，并且客户端 Y 调用 `Clerk.Append()`，然后客户端 X 的调用 返回。 一个请求必须能够看到已完成的所有调用导致的状态变更。

一个应用实现线性一致性就像一台单机服务器一次处理一个请求的行为一样简单。 例如，如果一个客户端发起一个更新请求并从服务器获取了响应，随后从其他客户端发起的读操作可以保证能看到改更新的结果。在单台服务器上提供线性一致性是相对比较容易的。

Lab 在 src/kvsrv 中提供了框架代码和单元测试。你需要更改 kvsrv/client.go、kvsrv/server.go 和 kvsrv/common.go 文件。

通过下面的命令进行运行测试

```shell
cd src/kvsrv
go test
```

## Part A: 无网络故障的 Key/value server

### A: 任务要求

此任务需要实现一个在没有丢失消息的情况下有效的解决方案。你需要在 client.go 中，在 Clerk 的 Put/Append/Get 方法中添加 RPC 的发送代码；并且实现 server.go 中 Put、Append、Get 三个 RPC handler。

当你通过了前两个测试 case：one client、many clients 时表示完成该任务。

## Part B: 可能drop message的 Key/value server

### B: 任务要求

现在，你应该修改你的解决方案，以便在遇到丢失的消息（例如 RPC 请求和 RPC 回复）时继续工作。如果消息丢失，则客户端的 ck.server.Call() 将返回 false （更准确地说， Call() 等待响应直至超市，如果在此时间内没有响应就返回false）。你将面临的一个问题是 Clerk 可能需要多次发送 RPC，直到成功为止。但是，每次调用 Clerk.Put() 或 Clerk.Append() 应该只会导致一次执行，因此你必须确保重新发送不会导致服务器执行请求两次。

你的任务是在 Clerk 中添加重试逻辑，并且在 server.go 中来过滤重复请求。

> Hint:
>
> - 你需要唯一标识客户端操作，以确保键/值服务器每个操作只执行一次。
> - 你需要仔细考虑服务器必须维护什么状态来处理重复的 Get()、Put() 和 Append() 请求，如果有的话。
> - 你的重复检测方案应该能够快速释放服务器内存，例如通过每个 RPC 暗示客户端已经看到了其前一个 RPC 的回复。可以假设客户端一次只向 Clerk 发起一个调用。

### 思路

PartA较为简单, PartB的难点在于如何处理重复请求, 以及如何处理丢失的消息

#### Plan1

为每一个Client和每次请求生成一个唯一的ID, 用于标识请求
用两个map来记录

```go
type KVServer struct {
mu sync.Mutex
// Your definitions here.
kvm          map[string]string // key-value map
lastReplyID  map[int]int64     // 保存每个clerk的最后一次请求的ReplyID
lastReplyRet map[int64]string  // 保存最后一次请求的结果, 处理重传
}
```

当client发送请求时, server检查这个请求和上一次请求的ID是否相同, 如果相同则直接返回上一次请求的结果, 否则执行请求, 并更新lastReplyID和lastReplyRet

结果: 无法通过memory测试, 在lastReplyID中消耗了太多内存

- TestMemGetManyClients测试逻辑为: 开多个client, 但是每个client只发送一个请求, 但是由于每个client都有一个lastReplyID, 导致内存消耗过大(test要求平均client每个消耗10B)
- 针对TestMemPut和TestMemAppend, 给的memory消耗为200B, 可以通过测试
- 我认为测试不够合理

最后解决方案: 对Get请求不保存任何状态, 每次请求不缓存, 直接返回结果, 对Put和Append请求, 保存每个client的lastReplyID, 用于处理重传

- 即Get请求不会更新lastReplyID, Put和Append请求会更新lastReplyID

#### Plan2

增加请求类型(Modify / Report), 其中Report表示这个请求是用来通知server删除上次的请求记录
修改client, 当请求成功接收到回复时, 再发送一个Report请求, 用于删除上次请求记录

- 不是很认可这种方案, 毕竟在分布式中, 网络是瓶颈, 不能频繁发送请求

#### Plan3

不使用map存储lastReplyID, 而是使用一个数组, 用于存储每个client的lastReplyID, 通过clientID来索引

## 问题记录

1. log: 这里的测试都是通过go test, 只能输出到标准输出, 无法通过命令行 2> log.ansi 去输出到文件中, 需要实现一个创建log文件函数, 并log.SetOutput(f)去设置输出文件
2. 在框架提供的可视化history中, Put信息似乎无法显示
3. 在这个实验中, 没有考虑通信延迟, 只考虑了消息丢失
    - 如Client先发请求reply_A, 后发reply_B, 其中reply_A1延迟到达, 超时, 重发reply_A2, 此时reply_B已经到达, 但是reply_A2到达, 会导致reply_B的lastReplyID被覆盖, 从而导致reply_B的结果被覆盖
    - 一个解决思路是, client的ReplyID使用递增的ID, 用来表示请求的顺序
4. 实验中似乎不能在MakeClerk函数中Call, 原因未知(似乎是server未启动)
