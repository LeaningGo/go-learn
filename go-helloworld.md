part 2: Hello World

这是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn/blob/master/go-helloworld.md)的第二部分，请阅读 [Golang 教程第一部分](https://github.com/LeaningGo/go-learn/blob/master/go-install.md)简介和安装。了解 golang 是什么？以及如何安装 golang。

没有比学习编程语言更好的方法了。让我们继续写下我们的第一个去项目。

我个人建议使用带有 go 扩展的 [Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Go) 作为IDE。它具有自动不全、代码样式和许多其他功能。

## 设置 go 工作空间

在开始编写代码之前，我们必须设置 go 工作空间。

对于 `Mac` 或 `Linux`，go 工作空间位于 `$HOME/go` 中。所以让我们继续进行，建立一个目录在 `$HOME` 里面。

对于 `Windows`，工作空间应位于 `C\Users\YourName\go` 中。因此，让我们在 `C\Users\YourName` 中创建 go 目录。

通过设置 `GOPATH` 环境变量，可以使用不同的目录作为工作空间。但是现在让我们使用上面的位置来简化。

go 的所有源文件应位于工作空间内名为 `src` 的目录中。因此，让我们在上面创建的 go 目录中创建目录 `src`。

每个 go 项目都应该在 `src` 中拥有自己的子目录。让我们在 `src` 中创建一个目录 `hello` 来保存 `hello world` 项目。

创建上述目录后，目录结构应如下所示。
```
go
  src
    hello
```

在我们刚刚创建的 `hello` 目录中将以下程序保存为 `helloworld.go`。
```
package main

import "fmt"

func main() {  
    fmt.Println("Hello World")
}
```

下面是创建上述程序后目录结构的样子
```
go
  src
    hello
      helloworld.go
```

## 运行 go 程序

运行 go 程序有几种不同的方法。让我们逐一看看它们。

1）使用 `go run` 命令，运行 `go run workspacepath/src/hello/helloworld.go` 命令。

上面命令中的 `workspacepath` 应该替换为工作空间的路径（在 windows 中：C:/Users/YourName/go，在 linux 或 Mac 中：`$HOME/go`）

您应该在控制台中看到输出：
```
Hello World
```

2）使用 `go install` 命令，运行 `go install hello` 命令，然后 `workspacepath/bin/hello` 运行程序。

上面命令中的 `workspacepath` 应该替换为工作空间的路径（在 windows 中：C:/Users/YourName/go，在 linux 或 Mac 中：`$HOME/go`）。您应该在命令行中看到相同的输出结果：
```
Hello World
```

当你输入 `go install hello` 时，go 工具会在工作区内搜索 `hello` 包（hello 被称为包，我们将在后面详细介绍包）。然后它在工作空间的 `bin` 目录中创建一个名为 `hello`（在 `windows` 的情况下是 `hello.exe`）的二进制文件。运行 `go install hello` 后，目录结构如下所示：
```
go
  bin  
    hello
  src
    hello
      helloworld.go
```

3）运行程序的第三个很酷的方法是使用 `go playground`。虽然这有限制，但是当我们想要运行简单的程序时，这种方法会派上用场。我为 `hello world program` 创建了一个 `playground`。[单击此处](https://play.golang.org/p/VtXafkQHYe)在线运行该程序。

您可以使用 [go playground](https://play.golang.org/) 与其他人共享源代码。

## hello world 的简短解释

这是我们刚写的 `hello world` 程序
```
package main //1

import "fmt" //2

func main() { //3  
    fmt.Println("Hello World") //4
}
```
我们在这里简要地看到程序的每一行。在接下来的教程中，我们将深入讨论每个部分。

**package main** - **每个 go 文件必须以 `package name` 语句开始**。包用于提供代码划分和可重用性。这里使用的包名是 `main`。

**import fmt** - 导入 `fmt` 包，它将在 `main` 函数内部用于将文本打印到标准输出。

**func main()** - `main` 是一个特殊的功能。程序执行从主函数开始。**主函数应该始终位于在主包中**。{ ....... } 表示主函数的开始和结束。

**fmt.Println("Hello World")** -  `fmt` 包的 `Println` 函数用于将文本写入标准输出。

您现在可以继续阅读 [Golang 教程第 3 部分](://github.com/LeaningGo/go-learn/blob/master/go-variables.md)：**变量**，以了解 golang 中的变量。

### 下一个教程 - [变量](https://github.com/LeaningGo/go-learn/blob/master/go-variables.md)
