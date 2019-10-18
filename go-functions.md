## Part 6: 函数

这里是 [Golang 教程系列](https://blog.csdn.net/baobaoxiannv/article/category/9365771)的第六部分。

## 什么是函数？

函数是执行特定任务的代码块。函数接受输入，对输入执行一些计算并生成输出。

## 函数声明

```
func functionname(parametername type) returntype {  
 //function body
}
```

函数声明以 `func` 关键字开头，后面跟着函数名。参数在 `( ..... )` 之间指定，后面跟着函数的 `returntype`。指定参数的语法是参数名称后面跟着类型。可以像这样指定任意数量的参数`(parameter1 类型、parameter2 类型)`。然后在 `{ ....... }` 之间有一个代码块，它是函数的主体。

参数和返回类型在函数中是可选的。因此，下面的语法也是一个有效的函数声明。

```
func functionname() {  
}
```

## 函数示例

让我们编写一个函数，该函数将单个产品的价格和产品数量作为输入参数，并通过将这两个值相乘来计算总价格并返回输出。

```
func calculateBill(price int, no int) int {  
    var totalPrice = price * no
    return totalPrice
}
```

上面的函数有两个输入参数 `price` 和 `no`，类型为 `int`，返回 `price` 和 `no` 的乘积 `totalPrice`。返回值也是 `int` 类型。

如果连续参数具有相同的类型，则可以避免每次都编写该类型，并且在结束时只需编写一次即可。例如 `price int, no int` 可以写成 `price, no int` 因此上面的函数可以重写为：

```
func calculateBill(price, no int) int {  
    var totalPrice = price * no
    return totalPrice
}
```

现在我们已经准备好了一个函数，让我们从代码中的某个地方调用它。调用函数的语法是 `functionname(参数)`。可以使用代码调用上面的函数。

```
calculateBill(10, 5) 
```

下面是完整的程序，使用上述功能，并打印总价格。

```
package main

import (  
    "fmt"
)

func calculateBill(price, no int) int {  
    var totalPrice = price * no
    return totalPrice
}
func main() {  
    price, no := 90, 6
    totalPrice := calculateBill(price, no)
    fmt.Println("Total price is", totalPrice)
}
```

在 [playground](https://play.golang.org/p/YJlW3g-VZH) 运行

以上程序将打印出来

```
Total price is 540
```

## 多个返回值

可以从一个函数返回多个值。让我们编写一个函数 `rectProps`，它获取矩形的长度和宽度，并返回矩形的面积和周长。长方形的面积是长和宽的乘积，周长是长和宽之和的两倍。

```
package main

import (  
    "fmt"
)

func rectProps(length, width float64)(float64, float64) {  
    var area = length * width
    var perimeter = (length + width) * 2
    return area, perimeter
}

func main() {  
     area, perimeter := rectProps(10.8, 5.6)
    fmt.Printf("Area %f Perimeter %f", area, perimeter) 
}
```

在 [playground](https://play.golang.org/p/qAftE_yke_) 运行

如果一个函数返回多个返回值，那么应该在 `( ....... )` 之间指定它们。`func rectProps(length, width float64)(float64, float64)` 有两个 `float64` 参数 `length` 和 `width`，还返回两个 `float64` 值。以上程序输出：

```
Area 60.480000 Perimeter 32.800000  
```

## 指定返回值

可以从函数返回命名值。如果命名了返回值，则可以将其视为在函数的第一行中声明为变量。

可以使用指定的返回值重写上面的 `rectProps`

```
func rectProps(length, width float64)(area, perimeter float64) {  
    area = length * width
    perimeter = (length + width) * 2
    return //no explicit return value
}
```

**area** 和 **perimeter** 是上述函数中的返回值命名。请注意，函数中的 **return** 语句不会显式返回任何值。由于 **area** 和 **perimeter** 在函数声明中指定为返回值，因此遇到 **return 语句时会自动从函数返回它们**。

## 空白标识符

**`_`** 在 Go 被称为空白标识符。它可以用来代替任何类型的任何值。让我们看看这个空白标识符的用途是什么。

该 `rectProps` 函数返回矩形的面积和周长。如果我们只需要 `area` 想要放弃 `perimeter`。`_` 在这个地方非常有用。

下面的程序仅使用函数 `area` 返回的 `rectProps` 函数。

```
package main

import (  
    "fmt"
)

func rectProps(length, width float64) (float64, float64) {  
    var area = length * width
    var perimeter = (length + width) * 2
    return area, perimeter
}
func main() {  
    area, _ := rectProps(10.8, 5.6) // perimeter is discarded
    fmt.Printf("Area %f ", area)
}
```

在 [playground](https://play.golang.org/p/IkugSH1jIt) 运行

在第十三行代码中，`_`标识符用于丢弃参数。

我们结束了本章。请留下您宝贵的意见和反馈。谢谢阅读。

### 下一篇教程 - [包管理](https://blog.csdn.net/baobaoxiannv/article/details/101105220)
