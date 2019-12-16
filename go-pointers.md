这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第十五部分。

在本教程中，我们将学习指针在 Go 中的工作方式，还将理解 Go 指针与其他语言（例如 C 和 C ++）中的指针有何不同。

本教程包括以下部分：

* 什么是指针？
* 声明指针
* 指针的零值
* 使用 new 函数创建指针
* 取消引用指针
* 将指针传递给函数
* 从函数返回指针
* 不要将指向数组的指针作为函数的参数传递。请改用切片。
* Go 不支持指针算术

## 什么是指针？
指针是一个变量，它存储另一个变量的内存地址。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191017104708550.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jhb2Jhb3hpYW5udg==,size_16,color_FFFFFF,t_70)

在上图中，变量 b 具有值 156，并存储在内存 address 0x1040a124。该变量 a 保存的地址 b。现在 a 指向 b。

## 声明指针

***T 是指向类型为 T 的值的指针变量的类型。**
让我们编写一个声明指针的程序。

```go
package main

import (  
    "fmt"
)

func main() {  
    b := 255
    var a *int = &b
    fmt.Printf("Type of a is %T\n", a)
    fmt.Println("address of b is", a)
}
```
在 [playground](https://play.golang.org/p/A4vmlgxAy8) 运行。

`&` 运算符用于获取变量的地址。在上述程序 `var a *int = &b` 中，我们将 b 的地址赋值给类型为 *int 的 a。现在说 a 指向 b，当我们打印 a 中的值时，会打印 b 的地址。这个程序输出 
& 运算符用于获取变量的地址。不一致。在上述程序中，我们将 b 的地址赋值给类型为 *int 的 a。现在说 a 指向 b，当我们打印 a 中的值时，会打印 b 的地址。这个程序输出

```
Type of a is *int  
address of b is 0x1040a124  
```

由于 b 的位置可以在内存中的任何位置，因此会为 b 获得不同的地址

## 指针的零值
指针的零值为  nil

```go
package main

import (  
    "fmt"
)

func main() {  
    a := 25
    var b *int
    if b == nil {
        fmt.Println("b is", b)
        b = &a
        fmt.Println("b after initialization is", b)
    }
}
```
在 [playground](https://play.golang.org/p/A4vmlgxAy8) 运行。
b 在上述程序中最初为 nil，然后将其分配给 a 的地址。该程序输出

```
b is <nil>  
b after initialisation is 0x1040a124  
```

## 使用 new 函数创建指针

Go 还提供了一个方便的函数 new 来创建指针。new 函数将类型作为参数，并返回指向 new 分配的作为参数传递的类型的零值的指针。

示例：
```go
package main

import (  
    "fmt"
)

func main() {  
    size := new(int)
    fmt.Printf("Size value is %d, type is %T, address is %v\n", *size, size, size)
    *size = 85
    fmt.Println("New size value is", *size)
}
```
在 [playground](https://play.golang.org/p/A4vmlgxAy8) 运行。
在上面程序 `size := new(int)` 中，我们使用 new 函数来创建一个类型为 int 的指针。这个函数将返回一个指针到一个新分配的类型为 int 的零值。类型为 int 的零值是 0。因此 size 类型为 *int，并指向 0。

上面程序的输出是：
```
Size value is 0, type is *int, address is 0x414020  
New size value is 85 
```

## 取消引用指针

取消引用指针意味着访问指针指向的变量的值。

让我们看看它在程序中如何工作。
```go
package main  
import (  
    "fmt"
)

func main() {  
    b := 255
    a := &b
    fmt.Println("address of b is", a)
    fmt.Println("value of b is", *a)
}
```
在 [playground](https://play.golang.org/p/A4vmlgxAy8) 运行。
在上述程序的第 `fmt.Println("address of b is", a)` 行，我们遵循 a 并打印它的值。如预期的那样，它打印 b 的值。程序的输出是
```
address of b is 0x1040a124  
value of b is 255  
```

让我们再编写一个程序，在其中使用指针更改 b 中的值。
```go
package main

import (  
    "fmt"
)

func main() {  
    b := 255
    a := &b
    fmt.Println("address of b is", a)
    fmt.Println("value of b is", *a)
    *a++
    fmt.Println("new value of b is", b)
}
```
在 [playground](https://play.golang.org/p/A4vmlgxAy8) 运行。

在上述程序 `*a++` 中，我们将 a 指向的值增加 1，这将改变 b 的值，因为 a 指向 b，因此 b 的值为 256。程序的输出是

```
address of b is 0x1040a124  
value of b is 255  
new value of b is 256  
```

## 将指针传递给函数
```go
package main

import (  
    "fmt"
)

func change(val *int) {  
    *val = 55
}
func main() {  
    a := 58
    fmt.Println("value of a before function call is",a)
    b := &a
    change(b)
    fmt.Println("value of a after function call is", a)
}
```
在 [playground](https://play.golang.org/p/3n2nHRJJqn) 运行。

在上面程序 `change(b)` 中：我们将指针变量 b 传递给函数 change，该变量保存了 a 的地址。在 change 函数中，使用 `*val = 55` 中的引用来更改 a 的值。这个程序输出

```
value of a before function call is 58  
value of a after function call is 55  
```

## 函数返回指针

函数返回局部变量的指针是完全合法的。Go 编译器足够智能，它会在堆上分配这个变量。
```go
package main

import (  
    "fmt"
)

func hello() *int {  
    i := 5
    return &i
}
func main() {  
    d := hello()
    fmt.Println("Value of d", *d)
}
```
在 [playground](https://play.golang.org/p/I6r-fRx2qML) 运行。

在上面程序 `return &i` 中：我们从函数 hello 中返回局部变量 i 的地址。这段代码的行为在 C 和 c++ 等编程语言中是未定义的，因为一旦函数 hello 返回，变量 i 就会超出作用域。但在 Go 的情况下，编译器会进行转义分析，并在地址转义本地范围时在堆上分配 i。因此，这个程序会工作，它会打印

```
Value of d 5
```

## 不要将指向数组的指针作为参数传递给函数，而是使用切片

假设我们想要对函数内部的数组做一些修改，并且对函数内部数组所做的修改应该对调用者可见。一种方法是将指向数组的指针作为函数的参数传递。

```go
package main

import (  
    "fmt"
)

func modify(arr *[3]int) {  
    (*arr)[0] = 90
}

func main() {  
    a := [3]int{89, 90, 91}
    modify(&a)
    fmt.Println(a)
}
```
在 [playground](https://play.golang.org/p/lOIznCbcvs) 运行。

在上面程序 `modify(&a)` 中，我们将数组 a 的地址传递给 modify 函数。在 `(*arr)[0] = 90` 中，在 modify 函数中，我们对 arr 引用，并为数组的第一个元素赋值 90。本程序输出 [90 90 91]

**a[x] 是 (*a)[x] 的缩写。因此 (*arr)[0] 在上述程序中可以被替换为 arr[0]**。让我们用这种简写语法重写上面的程序。

```go
package main

import (  
    "fmt"
)

func modify(arr *[3]int) {  
    arr[0] = 90
}

func main() {  
    a := [3]int{89, 90, 91}
    modify(&a)
    fmt.Println(a)
}
```
在 [playground](https://play.golang.org/p/k7YR0EUE1G) 运行。

这个程序的输出是：`[90 90 91]`

虽然这种将指针作为参数传递给函数并对其进行修改的方法是有效的，但这并不是在 Go 中实现这一点的惯用方法，惯用方法是切片。

让我们使用[切片](https://github.com/LeaningGo/go-learn/blob/master/go-arrays-and-slices.md)重写相同的程序。

```go
package main

import (  
    "fmt"
)

func modify(sls []int) {  
    sls[0] = 90
}

func main() {  
    a := [3]int{89, 90, 91}
    modify(a[:])
    fmt.Println(a)
}
```
在 [playground](https://play.golang.org/p/rRvbvuI67W) 运行。

在上面程序 `modify(a[:])` 中，我们将一个切片传递给 modify 函数。在 modify 函数中，切片的第一个元素被更改为 90。该程序还输出 [90 90 91]。所以不要把指针传递给数组，而是使用切片 :)。这段代码更简洁，并且是惯用的 Go :) 语法。

## Go 不支持指针运算
Go 不支持其他语言（如 C 和 C ++）中提供的指针算法。
```go
package main

func main() {  
    b := [...]int{109, 110, 111}
    p := &b
    p++
}
```
在 [playground](https://play.golang.org/p/WRaj4pkqRD) 运行。

上面的程序将抛出编译错误：`main.go:6: invalid operation: p++ (non-numeric type *[3]int)`

这就是 go 的指针。祝您有美好的一天 :) 

## 下一个教程 - [结构体](https://github.com/LeaningGo/go-learn/blob/master/go-structures.md)
