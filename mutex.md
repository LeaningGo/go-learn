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
在 [playground](https://play.golang.org/p/VX9dwGhR62) 运行。

互斥（Mutex）是一种结构类型，我们在15行中创建了一个类型为 Mutex 的零值变量 m。在上面的程序中，我们修改了 increment 函数，使 x = x + 1 的代码位于 m.Lock() 和 m.Unlock() 之间。现在这段代码是无效的任何竞态条件，因为在任何时候只有一个 Goroutine 可以执行这段代码。

现在如果运行这个程序，它将输入：
```
final value of x 1000  
```

在第十八行中传递互斥锁的地址很重要，如果互斥对象是通过值传递而不是地址传递，这样的话每个 Goroutine 都将拥有自己的互斥对象副本，竞态条件仍然会发生。


## 使用 channel 解决竞态条件

使用 channel 也可以解决竞态条件，让我们看一下是如何实现的吧

```go
package main  
import (  
    "fmt"
    "sync"
    )
var x  = 0  
func increment(wg *sync.WaitGroup, ch chan bool) {  
    ch <- true
    x = x + 1
    <- ch
    wg.Done()   
}
func main() {  
    var w sync.WaitGroup
    ch := make(chan bool, 1)
    for i := 0; i < 1000; i++ {
        w.Add(1)        
        go increment(&w, ch)
    }
    w.Wait()
    fmt.Println("final value of x", x)
}
```
在 [playground](https://play.golang.org/p/M1fPEK9lYz) 运行。

在上面的程序中，我们创建了一个[缓冲 channel](buffered-channels-worker-pools.md) 并指定容量为 1，并将其传递给了十八行中的 `increment` Goroutine。这个缓冲 channel 用于确保只有一个 Goroutine 访问增加 x 的代码的关键部分。这是通过在 x 增加之前将 true 传递给缓冲 chennel 来实现的，由于缓冲 channel 的容量为 1，所以所有的其他试图写入这个 channel 的 Goroutine 都会被阻塞。第十行直到将 x 增加之后从 channel 读取该值。这实际上只允许一个 Goroutine 访问临界区。

这个程序会打印
```
final value of x 1000  
```

## Mutex vs Channels
我们使用互斥锁和 channel 解决了竞争条件问题。那么我们如何决定什么时候使用什么。答案在于你要解决的问题。如果你试图解决的问题更适合于互斥锁，那么继续使用互斥锁。如果需要，不要犹豫使用互斥锁。如果问题似乎更适合 channel，那么使用它 :)。

大多数 Go 新手尝试使用 channel 解决所有并发问题，因为这是 go 语言一个很厉害的特性。这是错误的。go 语言让我们可以选择使用互斥锁或 channel，选择其中之一是没有错的。

一般来说，当 Goroutine 需要互相通信时使用 channel，当只有一个 Goroutine 应该访问代码的关键部分时使用互斥锁。

对于我们在上面解决的问题，我更喜欢使用互斥锁，因为这个问题不需要 goroutines 之间的任何通信。因此，互斥锁是一个正常的选择。

我的建议是为问题选择工具，不要试图用工具来解决问题 :)

本章教程这就结束了。祝你有美好的一天。

## 下一个教程 - [结构体和类](structs-instead-of-classes.md)
