这里是 [Golang 教程系列](https://github.com/LeaningGo/go-learn)的第十二部分。

## 什么是可变参数函数？
函数通常只接受固定数量的参数。可变参数函数是接受可变数量参数的函数。如果函数定义的最后一个参数用省略号作为前缀 `…`，则该函数可以接受该参数的任意数量的参数。

**只有函数的最后一个参数可以是可变参数**。在本教程的下一节中，我们将了解为什么会出现这种情况。

## 语法

```go
func hello(a int, b ...int) {  
}
```
在上面的函数中，参数 b 是可变参数，因为它以省略号作为前缀，并且可以接受任意数量的参数。可以使用以下语法调用此函数

```go
hello(1, 2) //passing one argument "2" to b  
hello(5, 6, 7, 8, 9) //passing arguments "6, 7, 8 and 9" to b  
```

也可以将零参数传递给可变参数函数。

```go
hello(1)  
```

在上面的代码中，我们 `hello` 使用的零参数调用 b。这完全没问题。
到现在为止，我想你已经了解了为什么可变参数仅应位于最后。
让我们尝试使 hello 函数的第一个参数可变。

语法看起来像
```go
func hello(b ...int, a int) {  
}
```

在上面的函数中，不可能将参数传递给参数 a，因为我们传递的任何参数都将被分配给第一个参数 b，因为它是可变参数。因此可变参数只能出现在函数定义的最后。上面的函数编译失败，`syntax error: cannot use ... with non-final parameter b`

## 示例和理解可变参数功能如何工作
让我们创建自己的可变函数。我们将编写一个简单的程序来查找整数输入列表中是否存在整数。

```
package main

import (  
    "fmt"
)

func find(num int, nums ...int) {  
    fmt.Printf("type of nums is %T\n", nums)
    found := false
    for i, v := range nums {
        if v == num {
            fmt.Println(num, "found at index", i, "in", nums)
            found = true
        }
    }
    if !found {
        fmt.Println(num, "not found in ", nums)
    }
    fmt.Printf("\n")
}
func main() {  
    find(89, 89, 90, 95)
    find(45, 56, 67, 45, 90, 109)
    find(78, 38, 56, 98)
    find(87)
}
```
在 [playground](https://play.golang.org/p/7occymiS6s) 上运行。

在上面的程序中 func find(num int, nums…int) nums 参数接受可变数量的参数。换句话说 在函数 find 中 nums 的类型是 []int i 是 一个整数切片。

**可变参数函数的工作方式是将可变数量的参数转换为可变参数类型的切片。例如，在第二十二行。在上面程序中，该 find 函数的可变参数数目是 89、90、95。find 函数需要可变 int 参数。因此，这三个参数将由编译器转换为 int 类型的切片，[]int{89, 90, 95} 然后将其传递给 find 函数。**

在第是十行中，for 循环遍历 nums 切片，并打印切片中 num 是否存在的位置。如果没有，它将打印找不到该号码。
以上程序的输出是：
```
type of nums is []int  
89 found at index 0 in [89 90 95]

type of nums is []int  
45 found at index 2 in [56 67 45 90 109]

type of nums is []int  
78 not found in  [38 56 98]

type of nums is []int  
87 not found in  []  
```

第二十五行，在上面的程序中，find 函数调用只有一个参数。我们没有向可变参数 **nums…int** 传递任何参数。如前所述，这是完全合法的，在这种情况下，nums 将是长度和容量为 0 的空切片。

## 切片参数 vs 可变参数

我们现在肯定有一个问题在你脑海里挥之不去。在上面的小节中，我们了解了一个函数的可变参数实际上是转换成一个切片。那么，当我们可以使用切片实现相同的功能时，为什么还要使用可变参数函数呢?我已经使用下面的切片重写了上面的程序。

```go
package main

import (  
    "fmt"
)

func find(num int, nums []int) {  
    fmt.Printf("type of nums is %T\n", nums)
    found := false
    for i, v := range nums {
        if v == num {
            fmt.Println(num, "found at index", i, "in", nums)
            found = true
        }
    }
    if !found {
        fmt.Println(num, "not found in ", nums)
    }
    fmt.Printf("\n")
}
func main() {  
    find(89, []int{89, 90, 95})
    find(45, []int{56, 67, 45, 90, 109})
    find(78, []int{38, 56, 98})
    find(87, []int{})
}
```
在 [playground](https://play.golang.org/p/rG-XRL3yycJ) 上运行。


以下场景是使用可变参数而不是切片的优点。

* 在每个函数调用期间不需要创建片。如果您查看上面的程序，我们在第 22、23、24 和 25 行中的每个函数调用期间创建了新的切片。使用可变参数函数时可以避免这种额外的切片创建
* 第二十五行在上面的程序中，我们创建了一个空的切片来满足 find 函数的签名。这在可变函数中是完全不需要的。当使用可变参数函数时，可以这样 find(87)。
* 我个人认为，使用可变参数函数的程序比使用切片函数的程序更具可读性 :)

## Append 是一个可变参数函数
你是否曾经想过标准库中用于将值追加到切片的 append 函数如何接受任意数量的参数。这是因为它是可变参数。

```go
func append(slice []Type, elems ...Type) []Type  
```

以上是 append 函数的定义。在这个定义中 elems 是可变参数。因此，append 可以接受可变数量的参数。

## 将切片传递给可变参数函数

让我们将切片传递给可变参数函数，然后从下面的示例中找到结果！

```go
package main

import (  
    "fmt"
)

func find(num int, nums ...int) {  
    fmt.Printf("type of nums is %T\n", nums)
    found := false
    for i, v := range nums {
        if v == num {
            fmt.Println(num, "found at index", i, "in", nums)
            found = true
        }
    }
    if !found {
        fmt.Println(num, "not found in ", nums)
    }
    fmt.Printf("\n")
}
func main() {  
    nums := []int{89, 90, 95}
    find(89, nums)
}
```
在 [playground](https://play.golang.org/p/A-DNilpH2L) 上运行。
第二十三行中，我们将切片传递给期望可变数量参数的函数。

这是行不通的。上面的程序将因编译错误而失败：`./prog.go:23:10: cannot use nums (type []int) as type int in argument to find`

为什么这样，这很简单。find 函数的签名如下所示：
```go
func find(num int, nums ...int)  
```

根据可变参数函数的定义，nums…int 表示它将接受数量可变的 int 类型的参数。
以上程序的第二十三行中，nums 是 []int 切片，它被传递给 find 函数，find 函数需要一个可变参数 int。正如我们已经讨论过的，这些可变参数将被转换为 int 类型的切片，因为 find 需要可变参数。在这种情况下，nums 已经是一个 []int 切片，编译器试图创建一个新的 []int i。编译器尝试去做

```go
find(89, []int{nums})  
```
这会失败，因为 nums 是 []int 而不是 int。

那么有没有办法将切片传递给可变参数函数呢？答案是肯定的。

**有一种语法糖，可用于将切片传递给可变参数函数。你必须在切片后加上省略号后缀 `...` 如果这样做，该切片将直接传递给函数，而无需创建新的切片**

在上面的程序中，如果你替换了第一行中的 find(89, nums)。使用 find(89, nums…)，程序将编译并打印以下输出
```
type of nums is []int  
89 found at index 0 in [89 90 95]  
```

这是完整程序供你参考。
```
package main

import (  
    "fmt"
)

func find(num int, nums ...int) {  
    fmt.Printf("type of nums is %T\n", nums)
    found := false
    for i, v := range nums {
        if v == num {
            fmt.Println(num, "found at index", i, "in", nums)
            found = true
        }
    }
    if !found {
        fmt.Println(num, "not found in ", nums)
    }
    fmt.Printf("\n")
}
func main() {  
    nums := []int{89, 90, 95}
    find(89, nums...)
}
```
在 [playground](https://play.golang.org/p/A-DNilpH2L) 上运行。

## 讲解
只要确保你知道在修改可变参数函数中的切片时在做什么。
让我们看一个简单的例子。

```go
package main

import (  
    "fmt"
)

func change(s ...string) {  
    s[0] = "Go"
}

func main() {  
    welcome := []string{"hello", "world"}
    change(welcome...)
    fmt.Println(welcome)
}
```
在 [playground](https://play.golang.org/p/R0GsuW7rdd) 上运行。

你认为上述程序的输出是什么？如果您认为这将是 [Go world] 恭喜！您已经了解了可变参数函数和切片。如果你弄错了，没什么大不了的，让我解释一下如何获得此输出。

在上面程序第十三行中，我们正在使用语法糖 `...` 并将切片作为可变参数传递给 change 函数。

正如我们已经讨论的，如果使用 `...`，则 welcome 切片本身将作为参数传递，而不会创建新的切片。因此 welcome 将 change 作为参数传递给函数。

在 change 函数内部，切片的第一个元素更改为 Go。因此，该程序输出
```
[Go world]
```

这是另一个了解可变参数函数的程序。

```go
package main

import (  
    "fmt"
)

func change(s ...string) {  
    s[0] = "Go"
    s = append(s, "playground")
    fmt.Println(s)
}

func main() {  
    welcome := []string{"hello", "world"}
    change(welcome...)
    fmt.Println(welcome)
}
```
我会把它留给你练习，以弄清楚上述程序是如何工作的 :)

可变参数函数就是这样。谢谢阅读。请留下您宝贵的反馈和意见。祝你有美好的一天。

## 下一个教程 - [Map（集合）](https://github.com/LeaningGo/go-learn/blob/master/go-map.md)
