这里是 [Golang 教程系列](https://blog.csdn.net/baobaoxiannv/article/category/9365771)的第十部分。

switch 是一个条件语句，它计算表达式并将其与可能的匹配列表进行比较，并根据匹配执行代码块。可以将其视为编写多个 `if else` 子句的惯用方式。

让我们从一个简单的示例开始，它将以手指作为输入，并输出该手指的名称 :)。例如，1是拇指，2是食指，等等。
```go
package main

import (  
    "fmt"
)

func main() {  
    finger := 4
    switch finger {
    case 1:
        fmt.Println("Thumb")
    case 2:
        fmt.Println("Index")
    case 3:
        fmt.Println("Middle")
    case 4:
        fmt.Println("Ring")
    case 5:
        fmt.Println("Pinky")

    }
}
```
在 [playground](https://play.golang.org/p/q4kjm2kpVe) 运行。

在上面的程序中，switch finger 将 finger 的值与每个 case 语句进行比较。从上到下计算用例，并执行与表达式匹配的第一个用例。在本例中，finger 的值为 4，因此打印了Ring。

不允许重复使用相同的常数值。如果你试图运行下面的程序，编译器会报错 `main.go:18:2: duplicate case 4 in switch previous case at tmp/sandbox887814166/main.go:16:7`

```go
package main

import (  
    "fmt"
)

func main() {  
    finger := 4
    switch finger {
    case 1:
        fmt.Println("Thumb")
    case 2:
        fmt.Println("Index")
    case 3:
        fmt.Println("Middle")
    case 4:
        fmt.Println("Ring")
    case 4://duplicate case
        fmt.Println("Another Ring")
    case 5:
        fmt.Println("Pinky")

    }
}
```
在 [playground](https://play.golang.org/p/SfXdChWdoN) 运行。

## 默认 case

我们只有 5 根手指。如果我们输入了错误的手指号码会发生什么? 这就是缺省情况出现的地方。当其他情况都不匹配时，将执行默认情况。
```
package main

import (  
    "fmt"
)

func main() {  
    switch finger := 8; finger {
    case 1:
        fmt.Println("Thumb")
    case 2:
        fmt.Println("Index")
    case 3:
        fmt.Println("Middle")
    case 4:
        fmt.Println("Ring")
    case 5:
        fmt.Println("Pinky")
    default: //default case
        fmt.Println("incorrect finger number")
    }
}
```
在 [playground](https://play.golang.org/p/Fq7U7SkHe1) 运行。

在上面的程序中 finger  是 8，它不符合任何情况，因此不正确的手指号码被打印出来。不一定要将默认作为 switch 语句中的最后一种情况。它可以出现在 switch 的任何地方。

你可能还注意到 finger 声明中的一个小变化。它在 switch 本身中声明。switch 可以包含一个可选语句，它在表达式求值之前执行。在这行 switch finger := 8; finger 首先被声明，也在表达式中使用。在这种情况下，finger 的范围仅限于 switch 块。

## 多重表达式以防万一

用逗号分隔多个表达式是可以的。
```
package main

import (  
    "fmt"
)

func main() {  
    letter := "i"
    switch letter {
    case "a", "e", "i", "o", "u": //multiple expressions in case
        fmt.Println("vowel")
    default:
        fmt.Println("not a vowel")
    }
}
```
在 [playground](https://play.golang.org/p/Zs9Ek5SInh) 运行。

上面的程序检查 letter 是否元音。该行 "a", "e", "i", "o", "u" 与所有元音匹配。该程序输出 `vowel`。

#### Expressionless switch
switch 中的表达式是可选的，可以省略。如果表达式被省略，则认为 switch 为 true，并对每个 case 表达式求真值，并执行相应的代码块。

```go
package main

import (  
    "fmt"
)

func main() {  
    num := 75
    switch { // expression is omitted
    case num >= 0 && num <= 50:
        fmt.Println("num is greater than 0 and less than 50")
    case num >= 51 && num <= 100:
        fmt.Println("num is greater than 51 and less than 100")
    case num >= 101:
        fmt.Println("num is greater than 100")
    }

}
```
在 [playground](https://play.golang.org/p/mMJ8EryKbN) 运行。

在上面的程序中，表达式在 switch 中是不存在的，因此它被认为是 true，并对每种情况求值。`case num >= 51 && num <= 100` 为 true，程序输出 `num is greater than 51 and less than 100`。这种类型的 switch  可以作为多个 if else 子句的替代。

## Fallthrough

让我们写一个程序来理解 fallthrough。我们的程序将检查输入数是否小于 50、100 或 200。例如，如果输入 75，则程序将打印 75 小于 100 和 200。我们将使用 fallthrough 实现此输出。
```
package main

import (  
    "fmt"
)

func number() int {  
        num := 15 * 5 
        return num
}

func main() {

    switch num := number(); { //num is not a constant
    case num < 50:
        fmt.Printf("%d is lesser than 50\n", num)
        fallthrough
    case num < 100:
        fmt.Printf("%d is lesser than 100\n", num)
        fallthrough
    case num < 200:
        fmt.Printf("%d is lesser than 200", num)
    }

}
```
在 [playground](https://play.golang.org/p/svGJAiswQj) 运行。

Switch 和 case 表达式不需要只是常量。它们也可以在运行时进行计算。在上面的程序中，num 被初始化为函数 number() 的返回值。控制进入 switch 内部，并对情况进行计算。`case num < 100:` 为真，程序打印 `75 is lesser than 100`。下一个语句是 fallthrough。遇到 fallthrough 时，控制移动到下一个 case 的第一个语句并打印 `75 is lesser than 200`。程序的输出是：
```
75 is lesser than 100  
75 is lesser than 200  
```

fallthrough 是 `case` 中的最后一个语句。如果它出现在中间的某个地方，编译器将抛出错误 `fallthrough statement out of place`

这就结束了本教程。还有一种类型的 `switch` 称为 `type switch`。当我们学习接口时，我们将对此进行研究。

请分享您宝贵的意见和反馈 :)。

## 下一个教程 - [数组和切片](https://blog.csdn.net/baobaoxiannv/article/details/102546473)
