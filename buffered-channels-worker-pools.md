这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第二十三部分。

我们在[前一篇教程](channels.md)中讨论的所有 channels 基本上都都是没有缓冲的。就像我们在 channels 教程中详细讨论的，发送和接收到非缓冲 channels 是阻塞的。

可以使用缓冲区创建 channels。发送到缓冲 channels 的数据仅在缓冲区已满时被阻塞。类似地，只有当缓冲区为空时，从缓冲 channels 接收的数据才会被阻塞。

通过向指定缓冲区大小的 make 函数传递额外的容量参数，可以创建缓冲 channels。
```
ch := make(chan type, capacity)  
```

以上语法中的 `capacity` 应该大于 0，这样 channels 就具有缓冲区。未缓冲 channels 的容量默认为 0，因此在前面的教程中创建 channels 时我们省略了 `capacity` 参数。

让我们编写一些代码，并创建一个带有缓冲 chennels 的示例。

## 示例
```
package main

import (  
    "fmt"
)


func main() {  
    ch := make(chan string, 2)
    ch <- "naveen"
    ch <- "paul"
    fmt.Println(<- ch)
    fmt.Println(<- ch)
}
```
在 [playground](https://play.golang.org/p/It-em11etK) 上运行程序。

在上面程序的第九行中，我们创建了一个容量为 2 的缓冲 channels。由于该 chennels 的容量为 2，因此我们可以将 2 个字符写入该 channels 而且不会被阻塞。我们在第十行和第十一行写入两个字符串并且 channels 不会被阻塞。在十二行和第十三行我们分别读取这两个字符串，下面是程序打印：
```go
naveen
paul
```

## 另外一个例子
让我们来看另一个缓冲 channels 的示例，其中 channels 的值在并发 Goroutine 中写入并在 main 函数中读取。这个例子将帮助我们更好的理解何时写入缓冲的 channels
```go
package main

import (  
    "fmt"
    "time"
)

func write(ch chan int) {  
    for i := 0; i < 5; i++ {
        ch <- i
        fmt.Println("successfully wrote", i, "to ch")
    }
    close(ch)
}
func main() {  
    ch := make(chan int, 2)
    go write(ch)
    time.Sleep(2 * time.Second)
    for v := range ch {
        fmt.Println("read value", v,"from ch")
        time.Sleep(2 * time.Second)

    }
}
```
在 [playground](https://play.golang.org/p/bKe5GdgMK9) 上运行程序。

在上面的程序中，容量为 2 的缓冲 channels ch 在 main Goroutine 的第十六行中创建，并在十七行中传递给 `write Goroutine`。然后 `main Goroutine` 休眠 2 秒。在此期间，write Goroutine 将并发运行。write Goroutine 有一个 for 循环，它将 0 到 4 之间的数字写入 ch channels。该缓冲 channels 的容量为 2，因此 write Goroutine 将能够立即将值 0 和 1 写入 ch channels，然后阻塞，直到从 ch channels 读取至少一个值为止。因此，这个程序将立即打印以下两行。

```
successfully wrote 0 to ch  
successfully wrote 1 to ch  
```
ps：为什么只打印这两行，因为 channels 的容量为 2，容量满了之后立即进入阻塞状态。

在打印上述两行之后，write Goroutine 中对 ch channels 的写入操作将被阻塞，直到有人从 ch channels 读取数据。由于 main Goroutine 在开始从 channels 读取之前会休眠 2 秒钟，所以在接下来的 2 秒钟内程序不会打印任何内容。main Goroutine 在 2 秒后唤醒，并开始使用第十九行中的 for range 循环从 ch channels 读取数据。打印读值，然后再次休眠 2 秒，这个循环一直持续到 ch 关闭。程序会在 2 秒后打印以下几行
```
read value 0 from ch  
successfully wrote 2 to ch
```

这将一直进行下去，直到将所有值写入 channels 并在 write Goroutine 中关闭为止。最终的输出是
```
successfully wrote 0 to ch  
successfully wrote 1 to ch  
read value 0 from ch  
successfully wrote 2 to ch  
read value 1 from ch  
successfully wrote 3 to ch  
read value 2 from ch  
successfully wrote 4 to ch  
read value 3 from ch  
read value 4 from ch  
```

## 死锁
```go
 package main

import (  
    "fmt"
)

func main() {  
    ch := make(chan string, 2)
    ch <- "naveen"
    ch <- "paul"
    ch <- "steve"
    fmt.Println(<-ch)
    fmt.Println(<-ch)
}
```
在 [playground](https://play.golang.org/p/FW-LHeH7oD) 上运行程序。

在上面的程序中，我们将 3 个字符串写入容量为 2 的缓冲 channels。当控制到达第十一行中的第三个写入操作时，由于 chennels 已超出其容量，所以写入操作被阻塞。现在，一些 Goroutine 必须从 channels 中读取数据，才可以进行写入操作，但是在这种情况下，并没有并发的线程从这个 chennels 读取数据。因此会出现死锁，程序会在运行时发出以下消息
```go
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan send]:  
main.main()  
    /tmp/sandbox274756028/main.go:11 +0x100
```

## 长度与容量

缓冲 channels 的容量是该 chennels 可以容纳的值的数量。这是我们在使用 make 函数创建缓冲 channels 时指定的值。

缓冲 channels 的长度是当前在其中排队的元素的数量。

用一个程序来表述上面的描述 :)
```go
package main

import (  
    "fmt"
)

func main() {  
    ch := make(chan string, 3)
    ch <- "naveen"
    ch <- "paul"
    fmt.Println("capacity is", cap(ch))
    fmt.Println("length is", len(ch))
    fmt.Println("read value", <-ch)
    fmt.Println("new length is", len(ch))
}
```
在 [playground](https://play.golang.org/p/2ggC64yyvr) 上运行程序。


## WaitGroup
本教程的下一节将介绍工作池。为了理解工作池，我们需要首先了解 WaitGroup，因为它将在工作池的实现中使用。

WaitGroup 用于等待 goroutine 的集合完成执行。该控制被阻塞，直到所有 Goroutines 完成执行。假设我们有 3 个并发执行的 Goroutine，它们是从 mian Goroutine 派生出来的。main goroutine 需要等待其他 3 个 goroutine 完成后才能结束。这可以使用 WaitGroup 来完成。

停止理论，编写一些代码 :)
```go
package main

import (  
    "fmt"
    "sync"
    "time"
)

func process(i int, wg *sync.WaitGroup) {  
    fmt.Println("started Goroutine ", i)
    time.Sleep(2 * time.Second)
    fmt.Printf("Goroutine %d ended\n", i)
    wg.Done()
}

func main() {
    no := 3
    var wg sync.WaitGroup
    for i := 0; i < no; i++ {
        wg.Add(1)
        go process(i, &wg)
    }
    wg.Wait()
    fmt.Println("All go routines finished executing")
}
```
在 [playground](https://play.golang.org/p/CZNtu8ktQh) 上运行程序。

[WaitGroup](https://golang.org/pkg/sync/#WaitGroup) 是一个结构体类型，我们在第十八行中创建了一个类型为 WaitGroup 的零值变量。WaitGroup 的工作方式是使用计数器（counter）。当我们在 WaitGroup 上调用 Add 并给它传递一个 int 值时，WaitGroup 的计数器会随着传递给 Add 的值的增加而增加。Wait() 方法阻塞调用它的 Goroutine，直到计数器变为零。

在上面的程序中，我们在 for 循环中调用 wg.Add(1)，该循环迭代 3 次。所以现在计数器变成了 3。for 循环还生成 3 个进程 Goroutines，然后在二十三行中调用 wg.Wait() 使 main Goroutine 等待，直到计数器变为 0。在第十三行的进程 Goroutine 中，对 wg.Done() 的调用降低了计数器。一旦 3 个衍生的 Goroutine 执行完毕，即 wg.Done() 被调用三次，计数器将变为零，mian Goroutine 将被解除阻塞。

在二十一行传递 wg 的地址是很重要的。如果没有传递地址，那么每个 Goroutine 将有自己的 WaitGroup 副本，当它们完成执行时，main 将不会收到通知。

上面程序的输出是
```
started Goroutine  2  
started Goroutine  0  
started Goroutine  1  
Goroutine 0 ended  
Goroutine 2 ended  
Goroutine 1 ended  
All go routines finished executing  
```

您的输出可能会与我不同，因为 Goroutine 的执行顺序会是不一致的。

## 工作池实现（Worker Pool Implementation）
缓冲 channels 的重要用途之一就是[工作池](https://en.wikipedia.org/wiki/Thread_pool)的实现。

通常，工作池是等待分配任务的线程的集合。一旦它们完成了分配的任务，它们就可以再次执行下一个任务。

我们将使用缓冲 channels 实现工作池。我们的工作池将执行查找输入数字的数字和的任务。例如，如果通过 234，输出将是 9 (2 + 3 + 4)。工作池的输入将是伪随机整数列表。

以下是我们的工作池的核心功能

* 创建 Goroutine 池，该缓冲池在输入缓冲的 channels 上监听等待分配的 job（任务/作业）
* 向输入缓冲 channels 添加 job
* job 完成后将结果写入输出缓冲 channels
* 从输出缓冲 channels 读取和打印结果


```go
type Job struct {  
    id       int
    randomno int
}
type Result struct {  
    job         Job
    sumofdigits int
}
```
每一个 `Job` 结构体都有一个 id 和 randomon 必须为其计算各个数字的总和。

Result 结构有一个 `Job` 字段，它在 sumofdigits 字段中保存结果(单个数字的和)。

下一步是创建用于接收 job 和写入、输出的缓冲 channels。

```
var jobs = make(chan Job, 10)  
var results = make(chan Result, 10)  
```

Worker Goroutines 监听 job 缓冲 channels 上的新任务。job 完成后，将结果写入结果缓冲 channels。

下面的 `digit` 函数实际执行查找整数的个位数和并返回它的工作。我们将向这个函数添加一个 2 秒的休眠时间，只是为了模拟这个函数需要一些时间来计算结果。

```go
func digits(number int) int {  
    sum := 0
    no := number
    for no != 0 {
        digit := no % 10
        sum += digit
        no /= 10
    }
    time.Sleep(2 * time.Second)
    return sum
}
```

接下来，我们将编写创建一个程序 worker Goroutine 函数。

```go
func worker(wg *sync.WaitGroup) {  
    for job := range jobs {
        output := Result{job, digits(job.randomno)}
        results <- output
    }
    wg.Done()
}
```

上面的函数创建一个 worker，它从 job channels 读取数据，使用当前的 job 和 digits 函数的返回值创建一个 results 结构，然后将 results 写入结果缓冲 channels。这个函数接受一个 WaitGroup `wg` 作为参数，当所有的 job 都完成时，它将调用 Done() 方法。

`createWorkerPool` 函数将创建一个 worker Goroutines 池。
```go
func createWorkerPool(noOfWorkers int) {  
    var wg sync.WaitGroup
    for i := 0; i < noOfWorkers; i++ {
        wg.Add(1)
        go worker(&wg)
    }
    wg.Wait()
    close(results)
}
```

上面的函数以要创建的 worker 的数量作为参数。它在创建 Goroutine 之前调用 wg.Add(1) 来增加 WaitGroup 计数器。然后通过将 WaitGroup wg 的地址传递给 worker 函数来创建 worker Goroutines。在创建了所需的 worker Goroutines 之后，它通过调用 wg.Wait() 来等待所有 Goroutines 完成它们的执行。在所有的 goroutine 完成执行之后，它会关闭 results channels，因为所有的 goroutine 都已经完成了它们的执行，没有其他人会进一步写入  results channels。

现在我们已经准备好了 worker 池，让我们继续编写将 job 分配给 worker 的函数。

```go
func allocate(noOfJobs int) {  
    for i := 0; i < noOfJobs; i++ {
        randomno := rand.Intn(999)
        job := Job{i, randomno}
        jobs <- job
    }
    close(jobs)
}
```

上面的 `allocate` 函数将要创建的 jobs 数量作为输入参数，生成最大值为 998 的伪随机数，使用随机数和 for 循环计数器 i 作为 id 创建 job 结构体，然后将它们写入 job channels。它在写入所有 job 之后关闭 job channels。

下一步是创建读取 results channels 并打印输出的函数。

```go
func result(done chan bool) {  
    for result := range results {
        fmt.Printf("Job id %d, input random no %d , sum of digits %d\n", result.job.id, result.job.randomno, result.sumofdigits)
    }
    done <- true
}
```

result 函数读取 results channels 并打印 job id、输入随机编号和随机编号的数字和。result 函数还将 done channels 作为参数，在打印完所有结果后将写入该参数。

一切都准备好了。让我们继续完成从 main() 函数调用所有这些函数的最后一步。

```go
func main() {  
    startTime := time.Now()
    noOfJobs := 100
    go allocate(noOfJobs)
    done := make(chan bool)
    go result(done)
    noOfWorkers := 10
    createWorkerPool(noOfWorkers)
    <-done
    endTime := time.Now()
    diff := endTime.Sub(startTime)
    fmt.Println("total time taken ", diff.Seconds(), "seconds")
}
```


我们首先将程序的执行开始时间存储在 mian 函数的第二行中，并在最后一行(第十二行)中计算结束时间和开始时间之间的时间差，并显示程序运行所需的总时间。这是必需的，因为我们将通过更改 Goroutines 的数量来执行一些基本测试。

`noOfJobs` 被设置为 100，然后调用 allocate 来将 job 添加到 job channels。

然后创建 done channels 并将其传递给 results Goroutine，以便它可以开始打印输出，并在打印完所有内容后通知。

最后，通过调用 createWorkerPool 函数创建一个包含 10 个 worker goroutine 的池，然后 main 在 done channels 上等待打印所有结果。

这是完整的程序供您参考。我也导入了必要的包。

```go
package main

import (  
    "fmt"
    "math/rand"
    "sync"
    "time"
)

type Job struct {  
    id       int
    randomno int
}
type Result struct {  
    job         Job
    sumofdigits int
}

var jobs = make(chan Job, 10)  
var results = make(chan Result, 10)

func digits(number int) int {  
    sum := 0
    no := number
    for no != 0 {
        digit := no % 10
        sum += digit
        no /= 10
    }
    time.Sleep(2 * time.Second)
    return sum
}
func worker(wg *sync.WaitGroup) {  
    for job := range jobs {
        output := Result{job, digits(job.randomno)}
        results <- output
    }
    wg.Done()
}
func createWorkerPool(noOfWorkers int) {  
    var wg sync.WaitGroup
    for i := 0; i < noOfWorkers; i++ {
        wg.Add(1)
        go worker(&wg)
    }
    wg.Wait()
    close(results)
}
func allocate(noOfJobs int) {  
    for i := 0; i < noOfJobs; i++ {
        randomno := rand.Intn(999)
        job := Job{i, randomno}
        jobs <- job
    }
    close(jobs)
}
func result(done chan bool) {  
    for result := range results {
        fmt.Printf("Job id %d, input random no %d , sum of digits %d\n", result.job.id, result.job.randomno, result.sumofdigits)
    }
    done <- true
}
func main() {  
    startTime := time.Now()
    noOfJobs := 100
    go allocate(noOfJobs)
    done := make(chan bool)
    go result(done)
    noOfWorkers := 10
    createWorkerPool(noOfWorkers)
    <-done
    endTime := time.Now()
    diff := endTime.Sub(startTime)
    fmt.Println("total time taken ", diff.Seconds(), "seconds")
}
```
在 [playground](https://play.golang.org/p/au5islUIbx) 上运行程序。

请在您的本地机器上运行这个程序，获得更准确的总时间计算。

这个程序会打印

```
Job id 1, input random no 636, sum of digits 15  
Job id 0, input random no 878, sum of digits 23  
Job id 9, input random no 150, sum of digits 6  
...
total time taken  20.01081009 seconds  
```

与这 100 个 job 对应的总共 100 行将被打印出来，最后程序运行所需的总时间将打印在最后一行。您的输出将不同于我的，因为 Goroutines 可以以任何顺序运行，总的时间也会根据硬件的不同而不同。在我的例子中，程序完成大约需要 20 秒。

现在让我们增加 noOfWorkers 的 main 函数到 20。我们使 workers  数增加了一倍。由于 workers Goroutines 增加了（精确地说是增加了一倍），程序完成所需的总时间应该减少了（精确地说是减少了一半）。我的情况是 10.004364685 秒，程序打印出来

```
...
total time taken  10.004364685 seconds  
```

现在我们可以理解，随着工作总数的增加，完成工作的总时间减少了。我将其作为练习留给您使用 `noOfJobs` 和 `noOfWorkers` 在 `main` 函数中使用不同的值并分析结果。

这就结束了本教程。祝您有美好的一天。:)

## 下一个教程 - [select](select.md)

