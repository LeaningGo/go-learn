这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第二十五部分。

在本教程中，我们将学习互斥锁。我们还将学习如何使用互斥锁和 [channels](channels.md) 来解决争用条件。

## 关键部分
在跳到互斥对象之前，理解并发编程中的[临界区](https://en.wikipedia.org/wiki/Critical_section)概念是很重要的。当一个程序并发运行时，修改共享资源的代码部分不应该被多个 Goroutines 同时访问。修改共享资源的这段代码称为临界段。例如，假设我们有一段代码，它使变量 x 增加 1。
```
x = x + 1  
```
如果是单个的 Goroutine 可以访问以上代码，不会出现任何问题。

让我们看看为什么同时运行多个 Goroutine 时此代码将失败。为了简单起见，假如我们有两个 Goroutine 同时运行上述代码

在内部，上述代码行将由系统在以下步骤中执行（有更多涉及寄存器的技术细节，加法如何工作等等，但为了本教程的目的，我们假设这是三个步骤）

* 得到当前 x 的值
* 计算 x + 1
* 将第二步计算的值分配给 x

当仅仅使用一个 Goroutines 执行这三个步骤时，一切都很好。

现在我们讨论两个 Goroutines 同时运行这段代码时发生的情况，下图描述了两种 Goroutines 同时访问代码行时可能发生的情况 `x = x + 1`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115162844742.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jhb2Jhb3hpYW5udg==,size_16,color_FFFFFF,t_70)
我们假设 x 的初始值为 0，Goroutine 1 获取 x 的初始值，计算 x+1，然后将计算的值赋给 x 之前，系统上下文切换为 Goroutine 2.现在 Goroutine 2 得到 x 的初始化仍然是 0，计算 x+1。此后，系统上下文再次切换到 Goroutine 1，现在 Goroutine 1 将其计算值 1 赋给 x，因此 x 变为 1。然后 Goroutine 2 再次开始执行，然后将它的计算值赋值给 x，也就是 1 到 x，因此 x 在两个 Goroutine 执行后都是 1。

然后，让我们来看看可能发生的另一种情况。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115163232204.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jhb2Jhb3hpYW5udg==,size_16,color_FFFFFF,t_70)

在上面的场景中，Goroutine 1 开始执行并完成所有三个步骤，因此 x 的值为 1。然后 Goroutine 2 开始执行。现在 x 的值是1，当 Goroutine 2 完成执行时，x 的值是 2。

从这两种情况可以看出 x 的最终值是 1 还是 2 取决于系统上下文切换的方式。这种程序输出依赖于 Goroutines 执行顺序的情况称为[竞态条件（race condition）](https://en.wikipedia.org/wiki/Race_condition)。

在上面的场景中，如果只允许一个 Goroutine 在任何时候访问代码的关键部分，则可以避免竞态条件。这可以通过使用互斥来实现。

## 互斥锁
互斥锁用于提供一种锁定机制，以确保在任何时刻只有一个 Goroutine 在运行代码的关键部分，从而防止竞争条件的发生。

互斥锁在同步包中可用。在互斥锁上定义了 [Lock](https://tip.golang.org/pkg/sync/#Mutex.Lock) 和 [Unlock](https://tip.golang.org/pkg/sync/#Mutex.Unlock) 两种方法。在 Lock 和 Unlock 调用之间出现的任何代码将仅由一个 Goroutine 执行，从而避免了竞争条件。

```go
mutex.Lock()  
x = x + 1  
mutex.Unlock()  
```
在上面的代码，`x = x + 1` 在任何时间点只有一个 Goroutine 执行，防止了出现竞争条件。

如果一个 Goroutine 已经持有锁，并且如果一个新的 Goroutine 试图获取锁，那么新的 Goroutine 将被阻塞，直到互斥锁被解锁。

## 出现竞争条件的程序
在这个小节中，我们将编写一个具有竞争条件的程序。在接下来的部分，我们将解决竞争条件。
```go
package main  
import (  
    "fmt"
    "sync"
    )
var x  = 0  
func increment(wg *sync.WaitGroup) {  
    x = x + 1
    wg.Done()
}
func main() {  
    var w sync.WaitGroup
    for i := 0; i < 1000; i++ {
        w.Add(1)        
        go increment(&w)
    }
    w.Wait()
    fmt.Println("final value of x", x)
}
```
在上面的程序中，increment  函数在第七行将 x 的值增加 1，然后调用 WaitGroup 的 Done() 函数来通知它的完成。

我们从上面程序的第十五行行衍生出 1000 个增量 Goroutines。这些 goroutine 中的每一个都是并发运行的，当多个 goroutine 试图同时访问 x 的值时，在第八行尝试增加 x 时发生竞争条件。

请你在本地运行这个程序，因为 playground 是确定的，竞争条件不会在 playground 上发生。在你的本地机器上多次运行这个程序，你可以看到由于竞争条件的原因，每次的输出都是不同的。我遇到的一些输出是 `final value of x 941`、`final value of x 928`、`final value of x 922` 等等。

## 使用互斥锁来解决竞争条件
在上面的程序中，我们生成了 1ooo 个 Goroutine。如果 x 的值每增加 1,x 的最终期望值应该是 1000。在本节中，我们将使用互斥锁修复上面程序中的竞争条件。

```go
gopackage main  
import (  
    "fmt"
    "sync"
    )
var x  = 0  
func increment(wg *sync.WaitGroup, m *sync.Mutex) {  
    m.Lock()
    x = x + 1
    m.Unlock()
    wg.Done()   
}
func main() {  
    var w sync.WaitGroup
    var m sync.Mutex
    for i := 0; i < 1000; i++ {
        w.Add(1)        
        go increment(&w, &m)
    }
    w.Wait()
    fmt.Println("final value of x", x)
}
```

