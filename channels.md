这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第二十二部分。

在[上一个教程中](goroutines.md)我们讨论了如何使用 Goroutines 在 Go 实现并发。在本教程中我们将讨论 channels 以及使用 channels 和 Goroutines 进行通信。

## 什么是 Channels？
可以将 channels 视为使用 Goroutines 进行通信的管道。与水在管道中从一端流到另一端的方式类似。可以使用 channels 从一端发送数据并从另一端接收数据。

## Channels 公告
每个 channels 都有与之关联的类型。此类型是允许 channels 传输的数据类型。不允许使用该 channels 传输其他类型

`chan T` T 是 channels 的类型

channels  的零值为 nil，nil channels 没有任何用处。因此必须使用 male 类似于[集合和切片](go-arrays-and-slices.md)的方法来定义 channels。

让我们编写声明 channels 的代码
```go
package main

import "fmt"

func main() {  
    var a chan int
    if a == nil {
        fmt.Println("channel a is nil, going to define it")
        a = make(chan int)
        fmt.Printf("Type of a is %T", a)
    }
}
```
在 [playground](https://play.golang.org/p/QDtf6mvymD) 上运行。

channels a 在第六行中声明并且是 nil，因为 channels 的零值是 nil。因此，执行 if 条件中的语句并定义 channels。上述程序中的 a 是一个整型 channels。这个程序将输出
```
channel a is nil, going to define it  
Type of a is chan int  
```
通常简写声明也是定义 channels 的有效而且简洁的方法
```go
a := make(chan int)  
```
上面的代码定义了一个 int channels a

## 从 Channels 发送和接收
下面给出了从 channels 发送和接收的语法。
```go
data := <- a // read from channel a  
a <- data // write to channel a  
```
箭头相对于 channels 的方向指定数据是发送还是接收。

在第一行，箭头从 a 指向外，因此我们从 channels a 读取并将值存储到变量数据。

在第二行，箭头指向a，因此我们将数据写 channels a。

## 默认情况下发送和接收数据将被阻塞
默认情况下，发送和接收到 channels 将处于阻塞状态。这是什么意思？当数据发送到 channels 时，控制将在 send 语句中被阻塞。直到其他 Goroutine 从该 channels 读取数据为止。同样，当从 channels 读取数据时，将阻塞读取。知道某些 Goroutine 将数据写入该 channels 为止。

channels 的这个属性将帮助 Goroutines 有效通信。而无需使用其他语言很常见的显示锁或者常见变量。

## Channels 示例程序
理论足够 :) 让我们来编写一个 Goroutine 来了解如何使用 channels 进行通信。
我们引用上一个例子
```go
package main

import (  
    "fmt"
    "time"
)

func hello() {  
    fmt.Println("Hello world goroutine")
}
func main() {  
    go hello()
    time.Sleep(1 * time.Second)
    fmt.Println("main function")
}
```
在 [playground](https://play.golang.org/p/U9ZZuSql8-) 上运行。

这是上一个教程中的例子，我们将在这里休眠。主要使用 Goroutine 等待 hello Goroutine 完成。

我们使用 channels 重写上面的例子
```go
package main

import (  
    "fmt"
)

func hello(done chan bool) {  
    fmt.Println("Hello world goroutine")
    done <- true
}
func main() {  
    done := make(chan bool)
    go hello(done)
    <-done
    fmt.Println("main function")
}
```
在 [playground](https://play.golang.org/p/I8goKv6ZMF) 上运行。

在上面的程序中，我们在第十二行中创建了一个 done bool channel。并将其作为参数传递给 hello Goroutine。在第十四行我们正在接收来自 done channel 的数据。这行代码被阻塞，这意味着除非 Goroutine 将数据写入 done 通道，否则控制不会移动到下一行代码。因此，这消除了时间上的需要。休眠，在最初的程序中存在，防止 main Goroutine 退出。

这行代码 `<- done ` 从 channels 接收数据但是不做任何处理是完全合理的。

现在我们阻塞了 mian Goroutine，等待 done channels 上的数据。hello Goroutine 接收 channels 作为参数，打印 hello world Goroutine，然后写入 done channel。当写操作完成时，mian Goroutine 从 done channel 接收数据，它被解除阻塞，然后文本 mian 函数被打印出来。

上面程序的输出是：
```go
Hello world goroutine
main function
```

让我们通过在 hello goroutine 中引入休眠来修改此程序。以便更好的理解阻塞概念。
```go
package main

import (  
    "fmt"
    "time"
)

func hello(done chan bool) {  
    fmt.Println("hello go routine is going to sleep")
    time.Sleep(4 * time.Second)
    fmt.Println("hello go routine awake and going to write to done")
    done <- true
}
func main() {  
    done := make(chan bool)
    fmt.Println("Main going to call hello go goroutine")
    go hello(done)
    <-done
    fmt.Println("Main received data")
}
```
在 [playground](https://play.golang.org/p/EejiO-yjUQ) 上运行。

这个程序会先打印 `Main going to call hello go goroutine`。然后 hello Goroutine 会被启动，它会打印 `hello go routine is going to sleep` 打印完之后，hello Goroutine 将休眠 4 秒，在此期间，mian Goroutine 将被阻塞，因为 <-done 等待来自 done channel 的数据。4 秒后 `hello go routine awake and going to write to done`，将打印随后的 `Main received data`。

## Channels 的另一个例子
让我们再编一个例子更好的了解 channles，该程序将打印数字的单个数字的平方和立方的总和。

例如如果输入 123，则此程序输出：

```go
squares = (1 * 1) + (2 * 2) + (3 * 3)
cubes = (1 * 1 * 1) + (2 * 2 * 2) + (3 * 3 * 3)
output = squares + cubes = 50
```

我们将在程序的结构中使 squares  在一个单独的 Goroutine 中计算，cubes 在另一个 Goroutine 中计算，最后的总和在 mian Goroutine 中进行。

```go
package main

import (  
    "fmt"
)

func calcSquares(number int, squareop chan int) {  
    sum := 0
    for number != 0 {
        digit := number % 10
        sum += digit * digit
        number /= 10
    }
    squareop <- sum
}

func calcCubes(number int, cubeop chan int) {  
    sum := 0 
    for number != 0 {
        digit := number % 10
        sum += digit * digit * digit
        number /= 10
    }
    cubeop <- sum
} 

func main() {  
    number := 589
    sqrch := make(chan int)
    cubech := make(chan int)
    go calcSquares(number, sqrch)
    go calcCubes(number, cubech)
    squares, cubes := <-sqrch, <-cubech
    fmt.Println("Final output", squares + cubes)
}
```
在 [playground](https://play.golang.org/p/4RKr7_YO_B) 上运行。

`calcSquares` 函数在第七行计算每个数字的平方和并将其发送到 squareop channels。类似地，calcCubes 函数在第十七行计算数字的每个数字的立方体的和，并将其发送到 cubeop channels。

这两个函数作为单独的 Goroutines 在第三十一行和第三十二行执行。每个都被作为参数传递一个要写入的 channels。main Goroutine 在三十三行中等待来自这两个 channels 的数据。从两个 channels 接收到数据后，将它们存储在 squares 和 cubes 变量中，并计算和打印最终的输出。这个程序会打印
```
Final output 1536  
```

## 死锁
使用 channels 时要考虑的一个重要的因素就是死锁。如果 Goroutine 在一个 channels 上发送数据，那么其他 Goroutine 应该接收数据。如果没有发生这种情况，程序将在运行时陷入死锁。

类似地，如果 Goroutine 正在等待接收来自某个 channels 的数据，那么其他 Goroutine 将在该通道上写入数据，否则程序将陷入恐慌。

```go
package main


func main() {  
    ch := make(chan int)
    ch <- 5
}
```
在 [playground](https://play.golang.org/p/q1O5sNx4aW) 上运行。

在上面的程序中，创建了一个 channels 叫做 ch，我们在第 5 行中发送 channels ch <- 5。但是没有任何 Goroutine 从 channels 中接收 ch。因此这个程序运行将会陷入恐慌，抛出以下错误
```
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan send]:  
main.main()  
    /tmp/sandbox249677995/main.go:6 +0x80
```

## 单向 Channels 
到目前为止我们学习的 channels 都是双向的，即：可以在它们上发送数据和接收数据。当然也可以创建单向 channels，即：仅发送或者仅接收数据

```go
package main

import "fmt"

func sendData(sendch chan<- int) {  
    sendch <- 10
}

func main() {  
    sendch := make(chan<- int)
    go sendData(sendch)
    fmt.Println(<-sendch)
}
```
在 [playground](https://play.golang.org/p/PRKHxM-iRK) 上运行。

在上面的程序中，我们在第十行之创建了发送 channels sendch，当箭头指向 channels 时，chan<- int 表示是发送 channels，我们在第十二行尝试着从一个只发送数据的 channels 中接收数据这是不允许的，当程序运行时，编译器会抛出错误。

```
main.go:11: invalid operation: <-sendch (receive from send-only type chan<- int)
```

**一切都是很好的，但是如果只是发送数据但是无法接收数据的 channels 又有什么用处呢？**

**这就是使用 channels 转换（channel conversion）的地方。可以将双向 channels  转换为仅发送或仅接收 channels，反之亦然**

```go
package main

import "fmt"

func sendData(sendch chan<- int) {  
    sendch <- 10
}

func main() {  
    chnl := make(chan int)
    go sendData(chnl)
    fmt.Println(<-chnl)
}
```
在 [playground](https://play.golang.org/p/aqi_rJ1U8j) 上运行。

在上面程序的第十行中，我们创建一个双向 channels chnl。它在第十一行中中作为参数传递给 sendData Goroutine。sendData 函数在参数 `sendch chan<- int` 中将这个 channels 转换为第五行中的一个只发送的 channels。然后在第十二行接收 channels 这个程序将打印 10 作为输出。

## 关闭 Channels 和用于 Channels 上的 range 循环

发送方可以关闭 channels 来通知接收方 channels 上不再发送数据。

在接收来自 channels 的数据时，接收器可以使用一个额外的变量来检查信道是否已经关闭。

```go
v, ok := <- ch  
```

在上面的语句中，如果发送操作成功接收到 channels 的值，则 ok 为 true。如果 ok 是 false，这意味着我们是从一个关闭的 channels 读取数据。从关闭的 channels 读取的值将是 channels 类型的零值。例如，如果 channels 是 int chan，那么从关闭 channels 接收的值将为 0。
```go
package main

import (  
    "fmt"
)

func producer(chnl chan int) {  
    for i := 0; i < 10; i++ {
        chnl <- i
    }
    close(chnl)
}
func main() {  
    ch := make(chan int)
    go producer(ch)
    for {
        v, ok := <-ch
        if ok == false {
            break
        }
        fmt.Println("Received ", v, ok)
    }
}
```
在 [playground](https://play.golang.org/p/XWmUKDA2Ri) 上运行。

在上面的程序中，`producer Goroutine` 将 0 到 9 写入 chnl channels，然后关闭 channels。main 函数在十六行中有一个无限的 for 循环，它检查 channels 是否在第十八行中使用变量 ok 关闭。如果 ok 为 false，则表示 channels 关闭，因此循环中断。否则，将打印接收到的值和 ok 的值。这个程序打印

```
Received  0 true  
Received  1 true  
Received  2 true  
Received  3 true  
Received  4 true  
Received  5 true  
Received  6 true  
Received  7 true  
Received  8 true  
Received  9 true  
```

for 循环的 for range 形式可用于从 channels 接收值，直到 channels 关闭。

让我们使用 for range 循环重写上面的程序。

```go
package main

import (  
    "fmt"
)

func producer(chnl chan int) {  
    for i := 0; i < 10; i++ {
        chnl <- i
    }
    close(chnl)
}
func main() {  
    ch := make(chan int)
    go producer(ch)
    for v := range ch {
        fmt.Println("Received ",v)
    }
} 
```
在 [playground](https://play.golang.org/p/JJ3Ida1r_6) 上运行。

十六行中的 for range 循环从 ch channels 接收数据，直到它关闭。一旦 ch 关闭，循环将自动退出。这个程序输出

```
Received  0  
Received  1  
Received  2  
Received  3  
Received  4  
Received  5  
Received  6  
Received  7  
Received  8  
Received  9  
```

可以使用 for range 循环重写另一个 channel 示例部分中的程序，使其具有更多的代码可重用性。

如果你仔细看一下这个程序，你会注意到在 calcSquares 函数和 calcCubes 函数中都重复了找到数字的单个数字的代码。我们将把代码移动到它自己的函数中，并发调用它。

```go
package main

import (  
    "fmt"
)

func digits(number int, dchnl chan int) {  
    for number != 0 {
        digit := number % 10
        dchnl <- digit
        number /= 10
    }
    close(dchnl)
}
func calcSquares(number int, squareop chan int) {  
    sum := 0
    dch := make(chan int)
    go digits(number, dch)
    for digit := range dch {
        sum += digit * digit
    }
    squareop <- sum
}

func calcCubes(number int, cubeop chan int) {  
    sum := 0
    dch := make(chan int)
    go digits(number, dch)
    for digit := range dch {
        sum += digit * digit * digit
    }
    cubeop <- sum
}

func main() {  
    number := 589
    sqrch := make(chan int)
    cubech := make(chan int)
    go calcSquares(number, sqrch)
    go calcCubes(number, cubech)
    squares, cubes := <-sqrch, <-cubech
    fmt.Println("Final output", squares+cubes)
}
```
在 [playground](https://play.golang.org/p/oL86W9Ui03) 上运行。

上面程序中的 numbers 函数现在包含了从数字中获取单个数字的逻辑，它由 calcSquares 函数和 calcCubes 函数同时调用。一旦数字中没有更多的数字，channels 在第十三行中关闭。calcSquares 和 calcCubes Goroutines 使用 for range 循环监听它们各自的 channels，直到它关闭。程序的其余部分是相同的。这个程序也会打印

```go
Final output 1536  
```

这里是本教程的结尾。channels 中很少有其他概念，如缓冲 channels、工作池和 select。我们将在单独的教程中讨论它们。感谢你的阅读。祝你有美好的一天。:)

## 下一个教程 - [缓冲 Channels 和 工作池（Worker Pools）](buffered-channels-worker-pools.md)


