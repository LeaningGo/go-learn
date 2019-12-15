## Part 3: 变量

这是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第三部分，它处理 golang 中的变量。

你可以阅读 [Golang 教程第二部分](https://github.com/LeaningGo/go-learn/blob/master/go-helloworld.md)：`Hello World`，了解配置 golang 和运行 `hello world` 程序。

## 什么是变量
变量是给定给内存位置的名称，用于存储特定类型的值。在 go 中声明变量有多种语法。

## 声明一个变量

**var name type** 是声明单个变量的语法。

```
package main

import "fmt"

func main() {  
    var age int // variable declaration
    fmt.Println("my age is", age)
}
```

在 [`playground`](https://play.golang.org/p/XrveIxw_YI) 上运行。

该语句 `var age int` 声明了一个名为 `age` 的 `int` 类型的变量。我们没有为变量分配任何值。如果没有为变量赋值，则使用变量类型的零值自动初始化它。在这种情况下，为 `age` 分配值 0。如果运行此程序，则可以看到以下输出：
```
my age is 0  
```

可以将变量分配给其类型的任何值。在上面的程序中，`age` 可以分配任何整数值。

```
package main

import "fmt"

func main() {  
    var age int // variable declaration
    fmt.Println("my age is ", age)
    age = 29 //assignment
    fmt.Println("my age is", age)
    age = 54 //assignment
    fmt.Println("my new age is", age)
}
```

在 [`playground`](https://play.golang.org/p/z4nKMjBxLx) 上运行。

上述程序将产生以下输出：
```
my age is  0  
my age is 29  
my new age is 54  
```

## 声明具有初始值的变量

变量在声明时也可以给出初始值。

**var name type = initialvalue** 是声明具有初始值的变量的语法。

```
package main

import "fmt"

func main() {  
    var age int = 29 // variable declaration with initial value

    fmt.Println("my age is", age)
}
```

在 [`playground`](https://play.golang.org/p/TFfpzsrchh) 上运行。

在上面的程序中，`age` 是 `int` 类型的变量，并且具有初始值 `29`.如果运行上述程序，则可以看到以下输出。它显示年龄已初始化为值 `29`：

```
my age is 29  
```

## 类型推断

如果变量具有初始值，Go 将自动使用该初始值推断该变量的类型。因此，如果变量具有初始值，则可以省略变量声明中的类型。

如果使用语法 **var name = initialvalue** 声明变量，Go 将自动从初始值推断出该变量的类型。

在以下示例中，您可以看到变量 `age` 的 `int` 类型已在第六行中删除。由于变量的初始值为 29，因此可以推断它的类型为 `int`。

```
package main

import "fmt"

func main() {  
    var age = 29 // type will be inferred

    fmt.Println("my age is", age)
}
```

在 [`playground`](https://play.golang.org/p/TFfpzsrchh) 上运行。

## 多变量声明

可以在单个语句中声明多个变量。

**var name1，name2 type = initialvalue1，initialvalue2** 是多变量声明的语法。

package main

import "fmt"

func main() {  
    var width, height int = 100, 50 //declaring multiple variables

    fmt.Println("width is", width, "height is", height)
}

在 [`playground`](https://play.golang.org/p/4aOQyt55ah) 上运行。

如果变量具有初始值，则可以省略类型。下面的程序使用类型推断声明多个变量。

```
package main

import "fmt"

func main() {  
    var width, height = 100, 50 //"int" is dropped

    fmt.Println("width is", width, "height is", height)
}
```

在 [`playground`](https://play.golang.org/p/sErofTJ6g-) 上运行。

上述程序将 `width is 100 height is 50` 作为输出打印。

正如您现在可能已经猜到的那样，如果没有为 `width` 和 `height` 指定初始值，它们将被指定为 0 初始值。

```
package main

import "fmt"

func main() {  
    var width, height int
    fmt.Println("width is", width, "height is", height)
    width = 100
    height = 50
    fmt.Println("new width is", width, "new height is ", height)
}
```

在 [`playground`](https://play.golang.org/p/DM00pcBbsu) 上运行。

上面的程序将打印出：

```
width is 0 height is 0  
new width is 100 new height is  50  
```

在某些情况下，我们可能希望在单个语句中声明属于不同类型的变量。这样做的语法是

```
var (  
      name1 = initialvalue1
      name2 = initialvalue2
)
```

以下程序使用上述语法来声明不同类型的变量。

```
package main

import "fmt"

func main() {  
    var (
        name   = "naveen"
        age    = 29
        height int
    )
    fmt.Println("my name is", name, ", age is", age, "and height is", height)
}
```

在 [`playground`](https://play.golang.org/p/7pkp74h_9L) 上运行。

这里我们声明**一个类型为 `string` 的变量名，`name ` 和 `height ` 类型为 `int`** (我们将在下一篇教程中讨论 golang 中可用的各种类型)。运行上述程序将产生输出：`my name is naveen , age is 29 and height is 0`。

## 简写声明

Go 还提供了另一种简洁的方式来声明变量。这称为简写声明，它使用 `:=` 运算符。

**name := initialvalue** 是声明变量的简写语法。

```
package main

import "fmt"

func main() {  
    name, age := "naveen", 29 //short hand declaration

    fmt.Println("my name is", name, "age is", age)
}
```

运行 [`playground`](https://play.golang.org/p/ctqgw4w6kx)

如果运行上述程序，则可以看到输出 `my name is naveen age is 29`。

简短声明要求赋值左侧的所有变量的初始化。以下程序将抛出错误 `cannot assign 1 values to 2 variables`。这是因为没有为 `age` 赋值。

```
package main

import "fmt"

func main() {  
    name, age := "naveen" //error

    fmt.Println("my name is", name, "age is", age)
}
```

在 [`playground`](https://play.golang.org/p/wZd2HmDvqw) 上运行。

只有当 `:=` 左边的变量中至少有一个是新声明的时，才可以使用简写语法。考虑以下程序。

```
package main

import "fmt"

func main() {  
    a, b := 20, 30 // declare variables a and b
    fmt.Println("a is", a, "b is", b)
    b, c := 40, 50 // b is already declared but c is new
    fmt.Println("b is", b, "c is", c)
    b, c = 80, 90 // assign new values to already declared variables b and c
    fmt.Println("changed b is", b, "c is", c)
}
```

在 [`playground`](https://play.golang.org/p/MSUYR8vazB) 上运行。

在上面的程序中，第八行中行中没有， b 已经被声明但 c 是新声明的，因此它输出为：

```
a is 20 b is 30  
b is 40 c is 50  
changed b is 80 c is 90  
```

如果我们运行以下程序：

```
package main

import "fmt"

func main() {  
    a, b := 20, 30 //a and b declared
    fmt.Println("a is", a, "b is", b)
    a, b := 40, 50 //error, no new variables
}
```

在 [`playground`](https://play.golang.org/p/EYTtRnlDu3) 上运行。

它会抛出错误 `no new variables on left side of :=` 这是因为变量 a 和 b 都已经声明，并且 `:=` 左侧没有新的变量。

还可以为变量分配在运行时计算的值。考虑以下程序，

```
package main

import (  
    "fmt"
    "math"
)

func main() {  
    a, b := 145.8, 543.8
    c := math.Min(a, b)
    fmt.Println("minimum value is ", c)
}
```

在 [`playground`](https://play.golang.org/p/7XojAtrpH9) 上运行。

在上面的程序中，c 的值是在运行时计算的，它是 a 和 b 最小的那个值。上面的程序将打印出来：

```
minimum value is  145.8  
```

由于 Go 是强类型的，因此声明属于一种类型的变量不能赋予另一种类型的值。以下程序将抛出一个错误，`cannot use "naveen" (type string) as type int in assignment` 因为 `age` 声明为 `int` 类型，我们正在尝试为其分配一个字符串值。

```
package main

func main() {  
    age := 29      // age is int
    age = "naveen" // error since we are trying to assign a string to a variable of type int
}
```

在 [`playground`](https://play.golang.org/p/K5rz4gxjPj) 上运行。

### 下一个教程 - [类型](https://github.com/LeaningGo/go-learn/blob/master/go-types.md)
