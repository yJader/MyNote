**Rule 1**: Whenever you have data that more than one goroutine uses, and at least one goroutine might modify the data, the goroutines should use locks to prevent simultaneous use of the data. The Go race detector is pretty good at detecting violations of this rule (though it won't help with any of the rules below).

> **规则1**：当你有多个goroutine使用的数据，并且至少有一个goroutine可能会修改这些数据时，goroutines应该使用锁来防止同时使用这些数据。
>
> - Go的race detector非常擅长检测违反此规则的行为（尽管它不会帮助检测下面的任何规则）。

**Rule 2**: Whenever code makes a sequence of modifications to shared data, and other goroutines might malfunction if they looked at the data midway through the sequence, you should use a lock around the whole sequence.

> **规则2**：当代码对共享数据进行一系列修改，并且如果其他协程在修改序列中途查看数据可能会发生故障，你应该在整个序列周围使用锁。

- 类似于事务的概念, 对于一系列操作, 他们在业务逻辑上具有原子性, 所以应该一起锁起来

**Rule 3**: Whenever code does a sequence of reads of shared data (or reads and writes), and would malfunction if another goroutine modified the data midway through the sequence, you should use a lock around the whole sequence.

> **规则3**：当代码执行一系列共享数据的读取操作（或读取和写入）时，如果另一个协程在序列中间修改了数据，程序可能会出错，此时你应该在整个序列周围使用锁。

- 与规则2类似

Rule 4: It's usually a bad idea to hold a lock while doing anything that might wait: reading a Go channel, sending on a channel, waiting for a timer, calling time.Sleep(), or sending an RPC (and waiting for the reply). One reason is that you probably want other goroutines to make progress during the wait. Another reason is deadlock avoidance. Imagine two peers sending each other RPCs while holding locks; both RPC handlers need the receiving peer's lock; neither RPC handler can ever complete because it needs the lock held by the waiting RPC call.

> 规则4：在执行任何可能需要等待的操作时，通常不建议持有锁：读取Go通道，发送到通道，等待计时器，调用time.Sleep()，或者发送RPC（并等待回复）。
>
> - 一个原因是你可能希望其他goroutine在等待期间做一些其他事情。
> - 另一个原因是避免死锁。想象两个peers在持有锁的同时互相发送RPC；两个RPC处理器都需要接收对等体的锁；由于需要等待RPC调用持有的锁，两个RPC处理器都无法完成。

Rule 5: Be careful about assumptions across a drop and re-acquire of a lock. One place this can arise is when avoiding waiting with locks held.

> 规则5：在锁的释放和重新获取过程中要谨慎对待假设。这种情况可能发生在避免持有锁时等待。

例如，以下发送投票RPC的代码是不正确的：

```go
rf.mu.Lock()
rf.currentTerm += 1
rf.state = Candidate
for <each peer> {
  go func() {
    rf.mu.Lock()
    args.Term = rf.currentTerm
    rf.mu.Unlock()
    Call("Raft.RequestVote", &args, ...)
    // handle the reply...
  } ()
}
rf.mu.Unlock()
```

代码在单独的goroutine中发送每个RPC。这是不正确的，因为args.Term可能不等于rf.currentTerm，在rf.currentTerm时，外围代码决定成为候选人。在周围代码创建goroutine和goroutine读取rf.currentTerm之间可能经过了很多时间；例如，可能会经过多个任期，而同伴可能不再是一个候选人。一种修复方法是创建的goroutine在持有锁的外部代码中使用rf.currentTerm的副本。同样，在Call()之后的回复处理代码必须在重新获得锁后重新检查所有相关的假设；例如，它应该检查自从决定成为候选人以来rf.currentTerm是否发生了变化。