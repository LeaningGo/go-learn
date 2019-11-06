这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第二十一部分。

在上一教程中，我们讨论了并发及其与并行性的区别。在本教程中，我们将讨论如何使用 Goroutines 在 Go 中实现并发。

## 什么是 Goroutines？
Goroutines 是与其他函数或方法并发运行的函数或方法。Goroutines 可以看作是轻量级线程。与线程相比，创建 Goroutine 的成本很小。因此，Go 应用程序通常会同时运行数千个 Goroutines。

## Goroutines 相对于线程的优势

* 与线程相比，Goroutines 非常廉价。它们的堆栈大小只有几个 kb，堆栈可以根据应用程序的需要增长和收缩，而在线程的情况下，堆栈大小必须指定并固定。

* goroutine 被多路复用到更少的 OS 线程。一个程序中可能只有一个线程，而程序中有成千上万个 Goroutines。如果该线程块中的任何 Goroutine 表示等待用户输入，则创建另一个 OS 线程，并将其余 Goroutine 移动到新的 OS 线程。所有这些都是由运行时处理的，作为程序员，我们从这些复杂的细节中抽象出来，并得到一个干净的 API 来处理并发性。

* Goroutines使用 channels 进行通信。通过设计，channels 可以防止在使用 Goroutines 访问共享内存时出现竞争条件。channels 可以看作是 Goroutines 通信使用的管道。我们将在下一篇教程中详细讨论 channels。

## 如何开始 Goroutine？
在函数或方法调用前面加上关键字 go，就会得到一个并发运行的新 Goroutine。

让我们创建一个 Goroutine :)

```go
package main

import (  
    "fmt"
)

func hello() {  
    fmt.Println("Hello world goroutine")
}
func main() {  
    go hello()
    fmt.Println("main function")
}
```
在 [playground](https://play.golang.org/p/zC78_fc1Hn) 运行程序。

在第十一行，go hello() 启动了一个新的 Goroutines。现在，hello() 函数已经与 main() 函数同时运行 main 函数在它自己爹 Goroutine 中运行。并称为 "main Goroutines"

运行这个程序你会有一个惊喜

这个程序仅输出文本 `main function` 我们开始的 Goroutines 怎么了？我们需要了解 go 例程的两个主要属性来理解为什么会发生这种情况

* 当一个新的 Goroutine 启动时，Goroutine 调用立即返回。与函数不同，控制不会等待 Goroutine 完成执行。控制在 Goroutine 调用后立即返回下一行代码，并且忽略 Goroutine 的任何返回值
* main Goroutine 应该为其他 Goroutine 运行而运行。如果 main Goroutine 终止，则程序将终止，其他 Goroutine 将不运行

我想你现在可以理解为什么我们的 Goroutine 没有运行了。在第十一行中调用 go hello() 之后，控制立即返回到下一行代码，无需等待 hello goroutine 完成并打印 mian function。然后 mian Goroutine 终止，因为没有其他代码要执行，因此 hello Goroutine 没有机会运行。

现在解决这个问题
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
在 [playground](https://play.golang.org/p/U9ZZuSql8-) 运行程序。

在上面程序的第十三行中，我们调用了 time.Sleep() 方法。该方法将[休眠](https://golang.org/pkg/time/#Sleep)正在执行的示例中的 go 程序。在这种情况下 main goroutine 将被休眠一秒。现在对 go hello() 的调用有足够的时间在 mian goroutine 终止之前执行。该函数将首先打印 `Hello world goroutine` 然后休眠一秒打印 `main function`

这种在 mian Goroutine 中使用 sleep 来等待其他 Goroutine 完成执行的方法我们用来了解 Goroutine 如何工作的一种技巧。Channels 可以用来阻塞 main Goroutine，直到所有其他 Goroutine 完成它们的执行。我们将在下一篇教程中讨论 channels。

## 启动多个 Goroutine
让我们编写一个程序来启动多个 Goroutine，以便更好地理解 Goroutine

```go
package main

import (  
    "fmt"
    "time"
)

func numbers() {  
    for i := 1; i <= 5; i++ {
        time.Sleep(250 * time.Millisecond)
        fmt.Printf("%d ", i)
    }
}
func alphabets() {  
    for i := 'a'; i <= 'e'; i++ {
        time.Sleep(400 * time.Millisecond)
        fmt.Printf("%c ", i)
    }
}
func main() {  
    go numbers()
    go alphabets()
    time.Sleep(3000 * time.Millisecond)
    fmt.Println("main terminated")
}
```
在 [playground](https://play.golang.org/p/oltn5nw0w3) 运行程序。

在上面的程序中启动了两个 Goroutine，分别在第二十一行和第二十二行。这两个 Goroutine 现在同时运行，numbers Goroutine 最初休眠 250 毫秒，然后打印 1，然后再次休眠并打印 2，相同的循环发生，直到打印 5。类似地，alphabets Goroutine 打印从 a到 e 的字母，睡眠时间为 400 毫秒。mian Goroutine 初始化 number 和 alphabets Goroutines，休眠 3000 毫秒，然后终止。

该程序输出：
```
1 a 2 3 b 4 c 5 d e main terminated  
```

下面的图片描述了工作方式，请在标签页中打开。可以提高清晰度

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191106153454115.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jhb2Jhb3hpYW5udg==,size_16,color_FFFFFF,t_70)

图片的第一部分用蓝色表示 numbers Goroutine，第二部分用粉色表示 alphabets Goroutine，第三部分用绿色表示 mian Goroutine，最后一部分用黑色表示以上三部分的集合，并向我们展示程序是如何工作的。每个框顶部的字符串如 0 ms、250 ms 表示时间(以毫秒为单位)，每个框底部的输出表示为 1、2、3 等等。蓝色方框表示 1 是在 250ms 之后打印的，2 是在 500ms 之后打印的，以此类推。最后一个黑色的底部有值 `1 a 2 3 b 4 c 5 d e main terminated`，这也是程序的输出。图片是不言自明的，你将能够理解程序是如何工作的。

Goroutine 就是这样，祝你有美好的一天 :)

## 下一个教程 - [Channels](channels.md)
