## Part 4: 类型

这是 [Golang 教程系列](https://github.com/LeaningGo/go-learn)的第四部分。

请阅读 [Golang 教程第三部分 - 变量](https://github.com/LeaningGo/go-learn/blob/master/go-variables.md)以了解变量。

以下是 go 中可用的基本类型：

* bool
* Numeric 类型
	* int8, int16, int32, int64, int
	* uint8, uint16, uint32, uint64, uint
	* float32, float64
	* complex64, complex128
	* byte
	* rune
* string

## bool

`bool` 类型表示布尔值，为 `true` 或 `false`。

```
package main

import "fmt"

func main() {  
    a := true
    b := false
    fmt.Println("a:", a, "b:", b)
    c := a && b
    fmt.Println("c:", c)
    d := a || b
    fmt.Println("d:", d)
}
```

在 [playground](https://play.golang.org/p/v_W3HQ0MdY) 运行。

在上面的程序中，a 被赋值为 `true`，b被赋值为 `false` 值。

c 被赋值为 `a && b` 的值。仅当 a 和 b 都为 `true` 时，`&&` 运算符才返回 `true`。所以在这种情况下 c 是 `false`。

当 a 或 b 为 `true` 时，`||` 操作符返回 `true`。在本例中，d 被赋值为 `true`，因为 a 为 `true`。我们将得到这个程序的如下输出：

```
a: true b: false  
c: false  
d: true  
```

## Signed integers（有符号整数）

**int8:** 表示 8 bit signed integers
**大小:** 8 bits
**范围:** -128 ~ 127

**int16:** 表示 16 bit signed integers
**大小:** 16 bits
**范围:** -32768 ~ 32767

**int32:** 表示 32 bit signed integers
**大小:** 32 bits
**范围:** -2147483648 ~ 2147483647

**int64:** 表示 64 bit signed integers
**大小:** 64 bits
**范围:** -9223372036854775808 ~ 9223372036854775807

**int:** 表示 32 位或 64 位整数，具体取决于底层平台。通常应该使用 `int` 来表示整数，除非需要使用特定大小的整数。
**大小:** 32 位系统中的 32 bits 和 64 位系统中的 64 bits 
**范围:** 32 位系统中的 -2147483648 ~ 2147483647，64 位系统中的 -9223372036854775808到 9223372036854775807

```
package main

import "fmt"

func main() {  
    var a int = 89
    b := 95
    fmt.Println("value of a is", a, "and b is", b)
}
```

在 [playground](https://play.golang.org/p/NyDPsjkma3) 运行。

以上程序的输出是：`value of a is 89 and b is 95`

在上面的程序中，a 的类型是 int, b 的类型是从赋给它的值推断出来的（95）。如上所述，int 的大小在 32 位系统中是 32 bit ，在 64 位系统中是 64 bit 。让我们来验证一下这个说法。

变量的类型可以使用 `Printf` 方法中的 `%T` 格式说明符来打印。Go 有一个不安全的包，它有一个 `Sizeof` 函数，函数返回的字节数是传递给它的变量的大小。使用不安全的包时应该小心，因为使用它的代码可能有可移植性问题，但是出于本教程的目的，我们可以使用它。

以下程序输出变量 a 和 b 的类型和大小。`%T` 是打印类型的格式说明符，`%d` 用于打印大小。

```
package main

import (  
    "fmt"
    "unsafe"
)

func main() {  
    var a int = 89
    b := 95
    fmt.Println("value of a is", a, "and b is", b)
    fmt.Printf("type of a is %T, size of a is %d", a, unsafe.Sizeof(a)) //type and size of a
    fmt.Printf("\ntype of b is %T, size of b is %d", b, unsafe.Sizeof(b)) //type and size of b
}
```


在 [playground](https://play.golang.org/p/mFsmjVk5oc) 运行。

上面的程序将输出

```
value of a is 89 and b is 95  
type of a is int, size of a is 4  
type of b is int, size of b is 4  
```

从上面的输出中我们可以推断出 a 和 b 是 `int` 类型的，它们的大小为 `32 bit` （4 字节）。如果在 64 位系统上运行上述程序，输出将会有所不同。在 64 位系统中，a 和 b 占用 64 bit （8 字节）。

## Unsigned integers（无符号整数）

**uint8:** 表示 8 bit unsigned integers
**大小:** 8 bits
**范围:** 0 ~ 255

**uint16:** 表示16bit unsigned integers
**大小:** 16 bits
**范围:** 0 ~ 65535

**uint32:** 表示 32bit unsigned integers
**大小:** 32 bits
**范围:** 0 ~ 4294967295

**uint64:** 表示 64 bit unsigned integers
**大小:** 64 bits
**范围:** 0 ~ 18446744073709551615

**uint:** 表示 32 位或 64 位无符号整数，具体取决于底层平台
**大小:** 32 位系统中的 32 bit 和 64 位系统中的 64 bit
**范围:** 32 位系统从 0 到 4294967295，64 位系统从 0 到 18446744073709551615

## Floating point types（浮点类型）

**float32:** 32 bit 浮点数
**float64:** 64 bit 浮点数

以下是一个简单的程序来说明整数和浮点类型

```
package main

import (  
    "fmt"
)

func main() {  
    a, b := 5.67, 8.97
    fmt.Printf("type of a %T b %T\n", a, b)
    sum := a + b
    diff := a - b
    fmt.Println("sum", sum, "diff", diff)

    no1, no2 := 56, 89
    fmt.Println("sum", no1+no2, "diff", no1-no2)
}
```

在 [playground](https://play.golang.org/p/upwUCprT-j) 运行。

a 和 b 的类型是从分配给它们的值推断出来的。在本例中，a 和 b的类型为 `float64`。(`float64` 是浮点值的默认类型)。我们把 a 和 b 相加，并把它赋给一个变量 `sum`。我们从 a 中减去 b 并将其赋值给 `diff`，然后输出 `sum` 和 `diff`。用 `no1` 和 `no2` 也做了类似的计算。以上程序将打印出来

```
type of a float64 b float64  
sum 14.64 diff -3.3000000000000007  
sum 145 diff -33  
```

## Complex types（复合类型）	

`complex64:` 实数和虚数为 `float32` 的复数
`complex128:` 实数和虚数为 `float64` 的复数

内置函数 [complex](https://golang.org/pkg/builtin/#complex) 用于构造实数和虚数的复数。complex 函数的定义如下：

```
func complex(r, i FloatType) ComplexType  
```

它接受实数和虚数作为参数，并返回一个复合类型。实数和虚数应该是同一类型的。即 `float32` 或 `float64`。如果实数和虚数都是 `float32`，则该函数返回一个 `complex64` 类型的复数值。如果实数和虚数的类型都是 `float64`，那么这个函数返回的复数值类型为 `complex128`。

也可以使用简写语法创建复数

```
c := 6 + 7i 
```

让我们写一个小程序来理解复数。

```
package main

import (  
    "fmt"
)

func main() {  
    c1 := complex(5, 7)
    c2 := 8 + 27i
    cadd := c1 + c2
    fmt.Println("sum:", cadd)
    cmul := c1 * c2
    fmt.Println("product:", cmul)
}
```

在 [playground](https://play.golang.org/p/kEz1uKCdKs) 运行。

在上面的程序中，c1 和 c2 是两个复数。c1 具有 5 作为实数，7 作为虚数。c2 具有实数 8 和虚数 27. `cadd` 被分配 c1 和 c2 的和，`cmul` 并被赋予 c1 和 c2 的乘积。该程序将输出

```
sum: (13+34i)  
product: (-149+191i)  
```

## 其他数字类型

**byte:** 是 `uint8` 的别名
**rune:** 是 `int32` 的别名

当我们了解字符串时，我们将更详细地讨论 `byte` 和  `rune`。

## string 类型

字符串是 golang 中的字节集合。如果这个定义没有任何意义也没关系。现在，我们可以假设字符串是字符的集合。我们将在单独的教程中详细学习字符串。

让我们用字符串来编写一个程序。

```
package main

import (  
    "fmt"
)

func main() {  
    first := "Naveen"
    last := "Ramanathan"
    name := first +" "+ last
    fmt.Println("My name is",name)
}
```

在 [playground](https://play.golang.org/p/CI6phwSVel) 运行。

在上面的程序中，首先分配字符串 **"Naveen"**，最后分配字符串 **"Ramanathan"**。字符串可以使用  `+` 运算符连接。`name` 的值被赋给第一个连接到后面跟着一个空格的值。上面的程序将输出 `My name is Naveen Ramanathan`。

还可以对字符串执行更多的操作。我们将在单独的教程中讨论这些。

## Type Conversion（类型转换）

Go 对显式输入非常严格。没有自动类型升级或转换。让我们用一个例子来看看这是什么意思。

```
package main

import (  
    "fmt"
)

func main() {  
    i := 55      //int
    j := 67.8    //float64
    sum := i + j //int + float64 not allowed
    fmt.Println(sum)
}
```

在 [playground](https://play.golang.org/p/m-TMRpmmnm) 运行。

上面的代码在 `C` 语言中是完全合法的。但在 `go` 中，这是行不通的。i 的类型是 int， j 的类型是 float64。我们试图添加两个不同类型的数字，这是不允许的。当你运行程序时，你会得到 `main.go:10: invalid operation: i + j (mismatched types int and float64)`

为了修正这个错误，i 和 j 应该是同一类型的。让我们把 j 转换成 `int. T(v) ` 是把值 v 转换成类型 T 的语法。

```
package main

import (  
    "fmt"
)

func main() {  
    i := 55      //int
    j := 67.8    //float64
    sum := i + int(j) //j is converted to int
    fmt.Println(sum)
}
```

在 [playground](https://play.golang.org/p/mweu3n3jMy) 运行。

现在，当您运行上面的程序时，您可以看到 `122` 作为输出。

赋值也是如此。需要显式类型转换将一种类型的变量分配给另一种类型。在下面的程序中解释。

```
package main

import (  
    "fmt"
)

func main() {  
    i := 10
    var j float64 = float64(i) //this statement will not work without explicit conversion
    fmt.Println("j", j)
}
```

在 [playground](https://play.golang.org/p/Y2uSYYr46c) 运行。

在第 9 行，i 转换为 `float64`，然后分配给 j。当您尝试将 i 分配给 j 而没有任何类型转换时，编译器将抛出错误。

本章结束。请在评论部分发布您的反馈和查询。

### 下一个教程 - [常量](https://github.com/LeaningGo/go-learn/blob/master/go-constants.md)
