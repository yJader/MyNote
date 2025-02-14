# 6.824-lec5-Go,threads and raft

> https://zhuanlan.zhihu.com/p/616175406
> 添加了一些个人感想

## 1.大纲

本章主要教一些Go相关的使用的并发编程技巧，在后续的lab中会用到

- 用到多线程只是因为这段代码的意义贴合并发, 这样写更好理解, 而不是为了追求性能
- 不用考虑细粒度锁或者其他技术处理事务, 一把大锁保平安
- 不用过分担心CPU性能层面的表现



## 2. Go 并发原语

### 2.1 Closure(闭包)

使用闭包可以让我们在后续的lab中轻松地并发执行某些调用(如同时发送RPC给多个followers)。

```go
package main

import "sync"

func main() {
    var wg sync.WaitGroup
    for i := 0; i < 5; i++ {
         wg.Add(1)
         // 方式一：参数复制副本，再引用，避免i后续变化给函数带来影响
         // 应该使用方式一
         go func(x int) {
             sendRPC(x)
             wg.Done()
         }(i)
        // 这种方法是有问题的
        // 方式二：直接引用，i的值变化会直接影响到函数的结果
        // go func() {
        //     sendRPC(i)
        //     wg.Done()
        // }()
         wg.Wait()
    } 
}

func sendRPC(i int) {
     println(i)
}
```

- 方式一：代码中的`go func(x int) { }(i)`，就是一个闭包(closure)，它允许我们在一个函数体内部直接定义函数并调用它，`func()`可以直接引用其外部`main()`域中定义的变量，这里的i就是传入`func(x int)`的参数。
  - *在某一刻i被传入后，x复制了一份i那一刻的副本，之后i的变化就不会对当时的程序造成影响。*
- 方式二：这里没有用形参，而是直接将i传入到函数中使用，**那么i的变化会直接影响到函数运行**，如刚传入第二次调用的`goroutine(g2)`时是2，但是由于循环的很快，当g2调用到sendRPC时，i已经变成了4，那么就会打印4，因此i的变化直接影响到了程序结果。

### 2.2 periodically task(周期性任务)

有时我们需要写一些周期性发生的任务(如raft中的定期发送心跳)。

```go
package main

import "time"
import "sync"

var done bool
var mu sync.Mutex

func main() {
	time.Sleep(1 * time.Second)
	println("started")
	go periodic()
	time.Sleep(5 * time.Second) // wait for a while so we can observe what ticker does
	mu.Lock()
	done = true
	mu.Unlock()
	println("cancelled")
	time.Sleep(3 * time.Second) // observe no output
}

func periodic() {
	for {
		println("tick")
		time.Sleep(1 * time.Second)
		mu.Lock()
		if done {
      mu.Unlock()
			return
		}
		mu.Unlock()
	}
       // in lab，我们编写的更像是这样的代码
      // for !raft.killed() {
        //  // do sth
        //  time.sleep(n*time.Second)
      // }
}
```

此外，我们注意到，在这段代码中，仅仅针对这段代码，理论上来说其实是可以不用Mutex的。

> 从代码上看，只有main中将done设置为true后，goroutine才会退出，这种顺序已经是板上钉钉的了，无需用锁来保证。

但是，事实证明，Go 编译器不会按照我们所想的去执行，*编译器在接收到这段代码后，我就会得到⼀段低级的机器码，这段机器码所做的事情细节会和我们凭直觉所想的有点不同*。有很多细节可以自行编译观察一下。但是要记住，从高级层面来看，我们要遵守的一条规则是：

> **如果你有访问共享变量的权利，你想去能够跨不同线程来观察这些共享变量，在你对这些共享变量进⾏读写前，你必须持有⼀把锁。这样可以在编程中避免很多问题。**

结合以前的文章，[Diver：The Go Memory Model(个人总结)](https://zhuanlan.zhihu.com/p/615656540)，就不难理解为什么在多线程并发的大背景下我们必须要用到锁了，因为不能一定保证能看到之前的变化。

### 2.3 Mutex

`defer mu.Unlock()`的操作很常用，这样不用我们自己去释放锁。

如后续在raft中的RPC handler，我们经常需要通过RPC handler去在raft上读取或写入数据，通常RPC handler所做的事情就是抢到锁，调⽤defer unlock操作，然后在这个函数⾥⾯做某些⼯作。

```go
 package main

import "sync"
import "time"

func main() {
	counter := 0
	var mu sync.Mutex
	for i := 0; i < 1000; i++ {
		go func() {
			mu.Lock()
                        // defer A：将A语句放到函数结束的时候执行
                        // 与将mu.Unlock()放到下面的效果是一样的
			defer mu.Unlock() 
			counter = counter + 1
                        // mu.Unlock()
		}()
	}

	time.Sleep(1 * time.Second)
	mu.Lock()
	println(counter)
	mu.Unlock()
}
```

若这里不用锁的话，会丢失一些对共享变量的更新，无法得到预期结果。

> 注意：Main本身也是一个[goroutine](https://zhida.zhihu.com/search?content_id=225059358&content_type=Article&match_order=3&q=goroutine&zhida_source=entity)，若要确保能打印出我们期待的值，那么就要确保main等待的时间是足够的(足以让上面的counter自增的goroutines全部执行完并返回)。
> 或用WaitGroup也可以做到确保打印出1000这个值。即上面提到的问题用WaitGroup可以解决。

### 2.4 Mutex深入思考

下面是一个银行的例子，他做的事情是定义了两个[closure](https://zhida.zhihu.com/search?content_id=225059358&content_type=Article&match_order=2&q=closure&zhida_source=entity)，并且并发执行，实现两个客户互相转账的功能。

alice和bob是共享变量，应该互斥访问。我们发现在这个例子里，在某些时刻总额不是20000。即某些不变量在中间的某一刻发生了变化，但是最终而言他还是不变的。

```go
package main

import "sync"
import "time"
import "fmt"

func main() {
	alice := 10000
	bob := 10000
	var mu sync.Mutex

	total := alice + bob
        
	go func() {
		for i := 0; i < 1000; i++ {
			mu.Lock()
			alice -= 1
			bob += 1
			mu.Unlock()
		}
	}()
	go func() {
		for i := 0; i < 1000; i++ {
			mu.Lock()
			bob -= 1
			alice += 1
			mu.Unlock()
		}
	}()
        // 审核总额线程
	start := time.Now()
	for time.Since(start) < 1*time.Second {
		mu.Lock()
		if alice+bob != total {
			fmt.Printf("observed violation, alice = %v, bob = %v, sum = %v\n", alice, bob, alice+bob)
		}
		mu.Unlock()
	}
}
```

解决方法也很简单，将+1与-1操作放在一个critical区域中，即让锁保护的范围更大，就可以避免不变量在执行过程中的某一刻出现的变化情况。

### 2.5 condition variable（条件变量）

举例来说，如在[raft算法](https://zhida.zhihu.com/search?content_id=225059358&content_type=Article&match_order=1&q=raft算法&zhida_source=entity)中的requestVote()，某个follower在某一刻变成了candidate，那么接下来它会向其余服务器请求投票，这里我们想要并行的请求投票，因此使用goroutines。该[candidate](https://zhida.zhihu.com/search?content_id=225059358&content_type=Article&match_order=2&q=candidate&zhida_source=entity)的程序会*不停地判断自己是否收到了所有server的响应或者自己是否拿到了半数以上的选票(由共享变量count、finished决定)*。

```go
package main

import "sync"
import "time"
import "math/rand"

func main() {
	rand.Seed(time.Now().UnixNano())

	count := 0
	finished := 0
	var mu sync.Mutex

	for i := 0; i < 10; i++ {
		go func() {
			vote := requestVote()
			mu.Lock()
			defer mu.Unlock()
			if vote {
				count++
			}
			finished++
		}()
	}

	for {
		mu.Lock()
		if count >= 5 || finished == 10 {
			break
		}
		mu.Unlock()
		// 加sleep，防止其不断地加锁、解锁，让cpu的占用率达到100%
                // 但是此种方式在效率上仍然欠妥
		time.Sleep(50 * time.Millisecond)
	}
	if count >= 5 {
		println("received 5+ votes!")
	} else {
		println("lost")
	}
	mu.Unlock()
}

func requestVote() bool {
	time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
	return rand.Int() % 2 == 0
}
```

在上述的循环判断部分，若我们不加sleep，那么for会一直进行lock(),unlock(),这最终会导致cpu资源被100%占用，从而使别的程序无法取得进展，可以采用如下解决方式：

- 让for每执行一次就休眠一定的时间，可以有效防止资源占用。但是由于我们无法确定修改共享变量的程序运行频率如何，所以我们很难找到一个恰当的时间去sleep。(判断部分可能依然会执行过于频繁、但是我们又不想让其等待太久，我们想让candidate及时的得到结果从而决定自己的最终身份)
- 有一种更优雅的方式：**使用条件变量(Condition Var)**。

条件变量本身的机制是，在创建了一个sync.Mutex的基础上，调用一次sync.NewCond(&mutex)，用条件变量包裹它，Mutex本身的加锁释放锁操作不变，但是多了一些机制，

> 重新捋一下思路。在此程序中存在共享变量，我们有多个goroutines对其进行修改，在main()中我们要不停地判断共享变量是否满足了某个条件，但是我们不想让其一直判断，因为这样会很浪费CPU资源，让别的程序无法执行；加sleep的话又无法找到一个合适的值。

- 因此，我们希望的是，当共享变量的状态发生变化的时候，才会触发一次判断操作，这是最合理的。而条件变量为我们提供了这种机制。

```go
package main

import "sync"
import "time"
import "math/rand"

func main() {
	rand.Seed(time.Now().UnixNano())

	count := 0
	finished := 0
	var mu sync.Mutex
	// 在Mutex基础上，使用一个条件变量引用了它
	cond := sync.NewCond(&mu)

	for i := 0; i < 10; i++ {
		go func() {
			vote := requestVote()
			mu.Lock()
			defer mu.Unlock()
			if vote {
				count++
			}
			finished++
			// Broadcast()，在修改关键变量后，调用此函数，它会唤醒下面的Wait()函数，使其执行完毕
			cond.Broadcast()
		}()
	}

	mu.Lock()
	for count < 5 && finished != 10 {
		// 在BroadCast()函数调用前，此函数会在这里一直阻塞
                // 确认阻塞时会自动释放锁
		cond.Wait()
	}
	if count >= 5 {
		println("received 5+ votes!")
	} else {
		println("lost")
	}
	mu.Unlock()
}

func requestVote() bool {
	time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
	return rand.Int() % 2 == 0
}
```

- 上述代码中，在某个goroutine对共享变量进行完一次修改后，我们会调用一次cond.Broadcast()，此函数会唤醒下面的cond.Wait()函数，让其不再阻塞，从而可以执行下一次判断。
- 若下面的判断在执行完后未能跳出循环，那么会继续执行cond.Wait()继续阻塞，直到下一次cond.Broadcast()将其唤醒，从而可以进行下一次判断。
- 注意上述条件变量函数调用的前提是：*我们此时必须持有[mutex](https://zhida.zhihu.com/search?content_id=225059358&content_type=Article&match_order=2&q=mutex&zhida_source=entity)。*Wait()的结果若是继续阻塞的话，会自动释放锁，不影响下一次操作。

一定严格按照上面的流程使用条件变量，这些流程一般是不会出现问题的，否则可能会出错(如lost broadcast())。 下面总结一下Cond的用法。

------

Cond 有三个方法：Signal、Broadcast 和 Wait。

这三个方法名是计算机科学中条件变量的通用方法名。比如，C 语言中对应的方法名是 pthread_cond_wait、pthread_cond_signal 和 [pthread_cond_broadcast](https://zhida.zhihu.com/search?content_id=225059358&content_type=Article&match_order=1&q=pthread_cond_broadcast&zhida_source=entity)。

**Signal**: 调用者通过该方法唤醒一个正在等待此 Cond 的 goroutine。如果此时没有等待的 goroutine，则无须通知 waiter，如果等待队列中有一个或者多个等待者，则移出第一个 goroutine 并唤醒它。

**Broadcast**: 和上面的 Signal 方法类似，只是 Broadcast 会清空队列，唤醒全部的等待中的 goroutine。

**调用 Signal 和 Broadcast 的时候，不要求调用者持有锁 Mutex 。**

**Wait**: 调用该方法的 goroutine 会被放到 Cond 的等待队列中并阻塞，直到被 Signal 或者 Broadcast 方法唤醒。

**调用 Wait 方法的时候一定要持有锁 Mutex 。**

------

### 2.7 channel

关于channel的总结，已经在这片博客里面写的十分详细，可以参考一下，这里就不再记录了。

[Diver：The Go Memory Model(个人总结)0 赞同 · 0 评论文章](https://zhuanlan.zhihu.com/p/615656540?)

channel适用于生产者/消费者相关问题，并且也可以实现类似于sync.WaitGroup的功能——等待特定数量的goroutines完成再退出main。

大部分情况下，只用前面的几种就足够了，channel应该避免使用，至少在lab中是这样的(TA的建议)。

## 3. Raft实现的一些注意事项

这里不涉及到具体的代码，仅仅说明一些注意事项。

### 3.1 RPC执行不该持有锁

1. 在执行RPC调用中，若到了等待其他peer响应的环节，不应该持有锁（在涉及到共享变量的处理时要持有锁），进一步来说，整个RPC除了涉及到共享变量的R/W外，都可以不加锁的。
   举例来说：如果加锁的话，对于同一个server而言，`CallRequestVote()`和`HandleRequestVote()`应该持有同一把锁；当server0,1同时向对方`RequestVote()`时，都会持有这把锁，并且只有对方调用Handle后才能释放该锁；由于处理Request的函数也需要这把锁，因此会等待当前的Call释放。

> 即server0的Call等待server1的Handle返回信息，server1的handle等待server1的call释放锁，server1的call等待server0的handle返回信息，server0的handle等待server0的call释放锁，如此形成一个等待环，出现死锁。
> 另外一个原因是，若遇到了不可靠的网络，RPC响应会晚一些到达，如果持有锁，那么在这期间我们无法做涉及到获取该锁的任何事情。

建议在RPC实际执行前，提前加锁统一获取所有需要的变量（一般都是共享变量）。

### 3.2 split-brain问题

若在一个任期内出现了两名leader，则会发生split-brain问题。在3.1的基础上，RPC调用不持有锁虽然会提高效率，但是若不做任何处理，也会发生一些不好的情况，就是split-brain问题。

在课堂中的例子中，因为server0在term1参与选举，半数以上server投票给了它，但是由于网络延迟等原因，它很晚才收到并处理投票，虽然s0理论上获得了足够的选票，但是由于延迟，他可能超时了。此时server1开启选举，`server1->currentTerm++`，此时s0投票给s1，根据规则(Raft论文中有写)，s0的`CurrentTerm++ `，恰好此时s0处理了上一个任期的选票，因此s0认为自己可以成为leader，但是它的currentTerm已经更新了，它本该在term1成为leader，但是现在却是在term2成为了leader。

> 本质上是没有对共享变量进行保护，state、term这些变量。

因此解决方法就是在得到某任期的选票后，检查server0的state、currentTerm是否与开始请求选票时一致，若不一致就说明此轮选举超时，选举无效。

> 至于补救行为则取决于实现，我们可以重新开始新一轮选举，或者不管他，直接作废。

## 4. 如何debug以找出问题

### 4.1 DPrintf()

使用DPrintf()函数打印相关log信息，可以判断是哪里出了问题。是一种惯用方法，逐层深入打印信息即可。DPrintf()定义在util.go文件中，通过修改debug参数(大于0就会打印内容)，来控制是否打印输出。

### 4.2 “`Ctrl`+`\`”命令

如果程序死锁或卡住不动了，此时运行“`Ctrl`+`\`”组合键命令，这会向当前Go程序发出一个退出信号，默认情况下，Go会处理这个程序并离开所有的goroutines。并打印出stacktrace信息，通过阅读调用栈信息，我们可以找到故障点所在。

注意测试的时候，用`go test -race -run xx` 命令，加上`-race`参数会自动检测是否有data race存在（确保你想要检测的代码区域是一定会被运行的）。

### 4.3 测试你的代码

有一个`go-test-many.sh`测试脚本，使用方法是：

```
sh go-test-many.sh -test_sum -parallel_test_num -file_name
```

具体内容可见github的说明。