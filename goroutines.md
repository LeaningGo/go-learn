这里是 [Golang 教程系列](https://github.com/LeaningGo/go-learn)的第二十一部分。

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
