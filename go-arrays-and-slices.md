这里是 [Golang 教程系列](https://github.com/LeaningGo/go-learn)的第十一部分。

## 数组
数组是属于同一类型的元素的集合。例如，整数 5、8、9、79、76 的集合形成一个数组。Go 中不允许混合使用不同类型的值，例如包含字符串和整数的数组。

## 公告
一个数组属于类型 `[n]T`。`n` 表示数组中元素的数量，`T` 表示每个元素的类型。元素 `n` 的数量也是类型的一部分（稍后我们将对此进行更详细的讨论）。

声明数组有不同的方法。让我们一一看一下。

```go
package main

import (  
    "fmt"
)


func main() {  
    var a [3]int //int array with length 3
    fmt.Println(a)
}
```
在 [playground](https://play.golang.org/p/Zvgh82u0ej) 运行。

`var a [3]int` 声明一个长度为 3 的整数数组。数组中的所有元素都自动赋给数组类型的 0 值。在这种情况下，`a` 是一个整数数组，因此 `a` 的所有元素都赋值为 `0`，即 `int` 的 `0` 值，运行上面的程序将输出 [0 0 0]。

数组的索引从 `0` 开始，以 `lenth - 1` 结束。让我们为上面的数组分配一些值。

```go
package main

import (  
    "fmt"
)


func main() {  
    var a [3]int //int array with length 3
    a[0] = 12 // array index starts at 0
    a[1] = 78
    a[2] = 50
    fmt.Println(a)
}
```
在 [playground](https://play.golang.org/p/WF0Uj8sv39) 运行。

`a[0]` 将值分配给数组的第一个元素。程序将输出 [12 78 50]。

让我们使用**简写声明**创建相同的数组。

```go
package main 

import (  
    "fmt"
)

func main() {  
    a := [3]int{12, 78, 50} // short hand declaration to create array
    fmt.Println(a)
}
```

在 [playground](https://play.golang.org/p/NKOV04zgI6) 运行。

上面的程序将打印相同的输出 [12 78 50]

简短声明时，不必为数组中的所有元素分配值。
```go
package main

import (  
    "fmt"
)

func main() {  
    a := [3]int{12} 
    fmt.Println(a)
}
```
在 [playground](https://play.golang.org/p/AdPH0kXRly) 运行。

在上面的程序中，第八行。`a := [3]int{12}` 声明了一个长度为 3 的数组，但是只提供了一个值 12 。其余 2 个元素自动赋值为 0。程序将输出 [12 0 0]

你甚至可以忽略声明中数组的长度，并将其替换为 `...`，让编译器为你找到长度。例如：
```go
package main

import (  
    "fmt"
)

func main() {  
    a := [...]int{12, 78, 50} // ... makes the compiler determine the length
    fmt.Println(a)
}
```

在 [playground](https://play.golang.org/p/_fVmr6KGDh) 运行。

**数组的大小是类型的一部分**。因此，`[5]int` 和 `[25]int` 是不同的类型。因此，无法调整数组的大小。不必担心此限制，因为 `slices`（切片）可以克服此限制。

```go
package main

func main() {  
    a := [3]int{5, 78, 8}
    var b [5]int
    b = a //not possible since [3]int and [5]int are distinct types
}
```
在 [playground](https://play.golang.org/p/kBdot3pXSB) 运行。

不一致。在上面的程序中，我们试图将一个类型为 [3]int 的变量赋给一个类型为 [5]int 的变量，这是不允许的，因此编译器将抛出 `main.go:6: cannot use a (type [3]int) as type [5]int in assignment.`。

## 数组是值类型

**Go 中的数组是值类型，而不是引用类型。这意味着当将它们分配给新变量时，会将原始数组的副本分配给新变量。如果对新变量进行了更改，它将不会映射在原始数组中**。

```go
package main

import "fmt"

func main() {  
    a := [...]string{"USA", "China", "India", "Germany", "France"}
    b := a // a copy of a is assigned to b
    b[0] = "Singapore"
    fmt.Println("a is ", a)
    fmt.Println("b is ", b) 
}
```
在 [playground](https://play.golang.org/p/-ncGk1mqPd) 运行。

在上面的程序中，第七行 a 的一个副本被分配给 b。b 的第一个元素改为 Singapore。这将不会映射在原始数组 a 中。程序将输出：

```
a is [USA China India Germany France]  
b is [Singapore China India Germany France]  
```

类似的，当数组作为参数传递给函数时，它们按值传递，原始数组不变。
```go
package main

import "fmt"

func changeLocal(num [5]int) {  
    num[0] = 55
    fmt.Println("inside function ", num)

}
func main() {  
    num := [...]int{5, 6, 7, 8, 8}
    fmt.Println("before passing to function ", num)
    changeLocal(num) //num is passed by value
    fmt.Println("after passing to function ", num)
}
```
在 [playground](https://play.golang.org/p/-ncGk1mqPd) 运行。

在上面的程序中第十三行。数组 `num` 实际上是通过值传递给函数 `changeLocal` 的，因此不会因为函数调用而更改。这个程序将输出：
```
before passing to function  [5 6 7 8 8]  
inside function  [55 6 7 8 8]  
after passing to function  [5 6 7 8 8]  
```

## 数组长度
通过将数组作为参数传递给 `len()` 函数来找到数组的长度 。
```go
package main

import "fmt"

func main() {  
    a := [...]float64{67.7, 89.8, 21, 78}
    fmt.Println("length of a is",len(a))

}
```

上面程序的输出是 `length of a is 4`

## 使用 range 遍历数组
for 循环可用于迭代数组中的元素
```go
package main

import "fmt"

func main() {  
    a := [...]float64{67.7, 89.8, 21, 78}
    for i := 0; i < len(a); i++ { //looping from 0 to the length of the array
        fmt.Printf("%d th element of a is %.2f\n", i, a[i])
    }
}
```

在 [playground](https://play.golang.org/p/-ncGk1mqPd) 运行。
上面的程序使用 for 循环遍历数组的元素，从索引 0 到数组 len - 1。这个程序可以打印：
```
0 th element of a is 67.70  
1 th element of a is 89.80  
2 th element of a is 21.00  
3 th element of a is 78.00  
```

Go 通过使用 for 循环的 range 形式提供了一种更好更简洁的遍历数组的方法。range 同时返回索引和该索引处的值。让我们使用 range 重写上面的代码。我们还将找到数组中所有元素的和。
```go
package main

import "fmt"

func main() {  
    a := [...]float64{67.7, 89.8, 21, 78}
    sum := float64(0)
    for i, v := range a {//range returns both the index and value
        fmt.Printf("%d the element of a is %.2f\n", i, v)
        sum += v
    }
    fmt.Println("\nsum of all elements of a",sum)
}
```
在 [playground](https://play.golang.org/p/-ncGk1mqPd) 运行。
第八行 `i, v := range a`，上面的程序是 for 循环的 range 形式。它将同时返回索引和该索引处的值。我们打印这些值，并计算数组 a 中所有元素的和。程序的输出是：
```
0 the element of a is 67.70  
1 the element of a is 89.80  
2 the element of a is 21.00  
3 the element of a is 78.00

sum of all elements of a 256.5  
```

如果你只想要值而想要忽略索引，您可以通过用 `_ ` 标识符替换索引来实现。

```
for _, v := range a { //ignores index  
}
```

上面的 for 循环会忽略索引。同样，值也可以忽略。

## 多维数组
到目前为止，我们创建的数组都是一维的。可以创建多维数组。
```go
package main

import (  
    "fmt"
)

func printarray(a [3][2]string) {  
    for _, v1 := range a {
        for _, v2 := range v1 {
            fmt.Printf("%s ", v2)
        }
        fmt.Printf("\n")
    }
}

func main() {  
    a := [3][2]string{
        {"lion", "tiger"},
        {"cat", "dog"},
        {"pigeon", "peacock"}, //this comma is necessary. The compiler will complain if you omit this comma
    }
    printarray(a)
    var b [3][2]string
    b[0][0] = "apple"
    b[0][1] = "samsung"
    b[1][0] = "microsoft"
    b[1][1] = "google"
    b[2][0] = "AT&T"
    b[2][1] = "T-Mobile"
    fmt.Printf("\n")
    printarray(b)
}
```
在 [playground](https://play.golang.org/p/-ncGk1mqPd) 运行。

在上面的程序中，第十七行。一个二维字符串数组 a 已经用简短的语法声明。第二十行末尾的逗号是必要的。这是因为 lexer 根据简单的规则自动插入分号。如果您有兴趣了解更多关于为什么需要这个请阅读 https://golang.org/doc/effective_go.html#。

另一个 2d 数组 b 在第二十三行中声明。每个索引一个一个地添加字符串。这是另一种初始化 2d 数组的方法。

第一行中的 printarray 函数。第七行使用两个 `for range` 循环来打印 `2d` 数组的内容。以上程序的输出为：
```
lion tiger  
cat dog  
pigeon peacock 

apple samsung  
microsoft google  
AT&T T-Mobile  
```
数组就是这样。虽然数组似乎足够灵活，但它们有一个限制，即它们的长度是固定的。增加数组的长度是不可能的。在 go 中，切片比传统数组更常见。

## 切片
切片是一个方便、灵活和强大的数组包装器。切片本身不拥有任何数据。它们只是对现有数组的引用。

## 创建切片

类型为 `T` 的元素用 `[]T` 表示

```go
package main

import (  
    "fmt"
)

func main() {  
    a := [5]int{76, 77, 78, 79, 80}
    var b []int = a[1:4] //creates a slice from a[1] to a[3]
    fmt.Println(b)
}
```
在 [playground](https://play.golang.org/p/Za6w5eubBB) 运行。

语法 `a[start:end]` 从数组 a 中创建一个切片，从索引 `start ` 到索引 `end -1`。所以在第一行。上面的程序 `a[1:4]` 从索引 1 到索引 3 创建数组 a 的切片表示。因此切片 b 值是 `[77 78 79]`。

让我们看看另一种创建切片的方法。

```go
package main

import (  
    "fmt"
)

func main() {  
    c := []int{6, 7, 8} //creates and array and returns a slice reference
    fmt.Println(c)
}
```

在 [playground](https://play.golang.org/p/_Z97MgXavA) 运行。

在上面的程序中，第九行 `c:= []int{6,7,8}` 创建一个有 3 个整数的数组，并返回一个存储在 c 中的切片引用。

## 修改切片

切片不拥有自己的任何数据。它只是底层数组的一种映射。对切片所做的任何修改都将映射在底层数组中。

```go
package main

import (  
    "fmt"
)

func main() {  
    darr := [...]int{57, 89, 90, 82, 100, 78, 67, 69, 59}
    dslice := darr[2:5]
    fmt.Println("array before",darr)
    for i := range dslice {
        dslice[i]++
    }
    fmt.Println("array after",darr) 
}
```
在 [playground](https://play.golang.org/p/_Z97MgXavA) 运行。

在上述程序的第九行中，我们 `dslice` 从数组的索引 2、3、4 创建。for 循环将这些索引中的值加 1。在 for 循环之后打印数组时，我们可以看到切片的修改映射在数组中。该程序的输出是
```
array before [57 89 90 82 100 78 67 69 59]  
array after [57 89 91 83 101 78 67 69 59]  
```

当多个切片共享同一基础数组时，每个切片所做的更改将映射在数组中。

```go
package main

import (  
    "fmt"
)

func main() {  
    numa := [3]int{78, 79 ,80}
    nums1 := numa[:] //creates a slice which contains all elements of the array
    nums2 := numa[:]
    fmt.Println("array before change 1",numa)
    nums1[0] = 100
    fmt.Println("array after modification to slice nums1", numa)
    nums2[1] = 101
    fmt.Println("array after modification to slice nums2", numa)
}
```

在 [playground](https://play.golang.org/p/_Z97MgXavA) 运行。

第九行开始，起始值和结束值都丢失了。开始和结束的默认值分别是 0 和 `len(numa)`。两个切片的 nums1 和 nums2 共享同一个数组。程序的输出是：
```
array before change 1 [78 79 80]  
array after modification to slice nums1 [100 79 80]  
array after modification to slice nums2 [100 101 80]  
```
从输出中可以清楚地看到，当切片共享同一数组时，每个数组所做的修改都会映射在数组中。

## 切片的长度和容量

**切片的长度是切片中元素的数量。切片的容量是基础数组中从创建切片的索引开始的元素数。**

让我们编写一些代码来更好地理解这一点。
```go
package main

import (  
    "fmt"
)

func main() {  
    fruitarray := [...]string{"apple", "orange", "grape", "mango", "water melon", "pine apple", "chikoo"}
    fruitslice := fruitarray[1:3]
    fmt.Printf("length of slice %d capacity %d", len(fruitslice), cap(fruitslice)) //length of fruitslice is 2 and capacity is 6
}
```
在 [playground](https://play.golang.org/p/_Z97MgXavA) 运行。

在上面的程序中，fruitslice 切片是由 fruitslice 数组的索引 1 和索引 2 创建的。因此 fruitslice 切片的长度是 2。

结果数组的长度是 7。frueslice 是由 fruarray 的索引 1 创建的。因此，fruitslice 切片的容量是指从索引 1 开始的 fruitslice 数组中元素的个数。这个值是 6。因此，fruitslice 切片的容量是 6。程序输出为：`length of slice 2 capacity 6`。

切片可以被重新切片至最大容量。除此之外的任何事情都将导致程序抛出运行时错误。

```
package main

import (  
    "fmt"
)

func main() {  
    fruitarray := [...]string{"apple", "orange", "grape", "mango", "water melon", "pine apple", "chikoo"}
    fruitslice := fruitarray[1:3]
    fmt.Printf("length of slice %d capacity %d\n", len(fruitslice), cap(fruitslice)) //length of is 2 and capacity is 6
    fruitslice = fruitslice[:cap(fruitslice)] //re-slicing furitslice till its capacity
    fmt.Println("After re-slicing length is",len(fruitslice), "and capacity is",cap(fruitslice))
}
```
在 [playground](https://play.golang.org/p/GcNzOOGicu) 运行。


## 使用 make 创建切片

*func make([]T, len, cap) []T* 可以通过传递类型、长度和容量来创建切片。capacity 参数是可选的，默认为长度。make 函数创建一个数组并返回一个切片引用。

```go
package main

import (  
    "fmt"
)

func main() {  
    i := make([]int, 5, 5)
    fmt.Println(i)
}
```
在 [playground](https://play.golang.org/p/M4OqxzerxN) 运行。

使用 make 创建切片时，默认情况下将这些值清零。以上程序将输出 [0 0 0 0 0]。

## 追加到切片
众所周知，数组仅限于固定长度，不能增加其长度。切片是动态的，可以使用 `append` 函数将新元素追加到切片。附加函数的定义为 `func append(s []T, x ...T) []T`。

函数定义中的 `x...T` 表示函数接受参数 x 的可变数量的参数。这些类型的函数称为可变函数。

一个问题可能会困扰你。如果切片由数组支持，并且数组本身的长度是固定的，那么切片的长度是动态的。好吧，在底层发生的事情是，**当将新元素附加到切片时，将创建一个新数组。现有数组的元素将复制到此新数组，并返回此新数组的新切片引用**。现在，新片的容量是旧片的容量的两倍。非常酷 :)。以下程序将使情况变得清楚。

```go
package main

import (  
    "fmt"
)

func main() {  
    cars := []string{"Ferrari", "Honda", "Ford"}
    fmt.Println("cars:", cars, "has old length", len(cars), "and capacity", cap(cars)) //capacity of cars is 3
    cars = append(cars, "Toyota")
    fmt.Println("cars:", cars, "has new length", len(cars), "and capacity", cap(cars)) //capacity of cars is doubled to 6
}
```
在 [playground](https://play.golang.org/p/M4OqxzerxN) 运行。

在上述程序中，cars 的初始容量为3。我们在第十行的 cars 上增加了一个新元素并再次将 `append(cars, "Toyota")` 映射给 cars。现在 cars 的容量增加了一倍，变成了6 辆。以上程序的输出为：
```
cars: [Ferrari Honda Ford] has old length 3 and capacity 3  
cars: [Ferrari Honda Ford Toyota] has new length 4 and capacity 6  
```

切片类型的零值为 nil。空切片的长度和容量为 0。可以使用 `append` 函数将值追加到空切片。
```go
package main

import (  
    "fmt"
)

func main() {  
    var names []string //zero value of a slice is nil
    if names == nil {
        fmt.Println("slice is nil going to append")
        names = append(names, "John", "Sebastian", "Vinay")
        fmt.Println("names contents:",names)
    }
}
```
在 [playground](https://play.golang.org/p/x_-4XAJHbM) 运行。

在上面的程序中 names = nil，我们将 3 个字符串附加到 names。该程序的输出是
```
slice is nil going to append  
names contents: [John Sebastian Vinay]  
```

也可以使用 `...` 运算符将一个切片附加到另一个切片。你可以在可变参数函数教程中了解有关此运算符的更多信息。

```go
package main

import (  
    "fmt"
)

func main() {  
    veggies := []string{"potatoes","tomatoes","brinjal"}
    fruits := []string{"oranges","apples"}
    food := append(veggies, fruits...)
    fmt.Println("food:",food)
}
```

在 [playground](https://play.golang.org/p/UnHOH_u6HS) 运行。

第十行，上面提到的 10 种食物都是通过在 veggies 中添加 fruits 来完成的。该程序的输出是: food: [potatoes tomatoes brinjal oranges apples]

## 将切片传递给函数
切片可以认为是由结构类型，在内部表示看起来就是这样

```
type slice struct {  
    Length        int
    Capacity      int
    ZerothElement *byte
}
```

切片包含长度、容量和指向数组第 0 元素的指针。**当一个切片被传递给一个函数时，即使它是通过值传递的，指针变量也会指向相同的底层数组。**因此，当一个切片作为参数传递给一个函数时，在函数内部所做的更改在函数外部也是可见的。让我们写一个程序来检查这个。

```go
package main

import (  
    "fmt"
)

func subtactOne(numbers []int) {  
    for i := range numbers {
        numbers[i] -= 2
    }

}
func main() {  
    nos := []int{8, 7, 6}
    fmt.Println("slice before function call", nos)
    subtactOne(nos)                               //function modifies the slice
    fmt.Println("slice after function call", nos) //modifications are visible outside
}
```
在 [playground](https://play.golang.org/p/IzqDihNifq) 运行。

上述程序第十七行中的函数调用将切片中的每个元素减 2。当在函数调用之后打印切片时，这些修改是可见的。如果你还记得，这与在函数内部对数组所做的更改在函数外部不可见的数组是不同的。上述程序的输出为:
```
slice before function call [8 7 6]  
slice after function call [6 5 4]  
```

## 多维切片
与数组相似，切片可以具有多个维度。
```
package main

import (  
    "fmt"
)


func main() {  
     pls := [][]string {
            {"C", "C++"},
            {"JavaScript"},
            {"Go", "Rust"},
            }
    for _, v1 := range pls {
        for _, v2 := range v1 {
            fmt.Printf("%s ", v2)
        }
        fmt.Printf("\n")
    }
}
```

在 [playground](https://play.golang.org/p/IzqDihNifq) 运行。
该程序的输出为
```
C C++  
JavaScript  
Go Rust  
```

## 内存优化

切片包含对底层数组的引用。只要切片在内存中，就不能对数组进行垃圾收集。当涉及到内存管理时，这可能会引起关注。假设我们有一个非常大的数组，并且只对其中的一小部分感兴趣。从此以后，我们从该数组创建一个切片并开始处理该切片。这里需要注意的重要一点是，由于切片引用了数组，所以数组仍然在内存中。

解决这个问题的一种方法是使用复制函数 `func copy(dst, src []T) int` 来复制切片。通过这种方式，我们可以使用新的切片，并且可以对原始数组进行垃圾收集。

```
package main

import (  
    "fmt"
)

func countries() []string {  
    countries := []string{"USA", "Singapore", "Germany", "India", "Australia"}
    neededCountries := countries[:len(countries)-2]
    countriesCpy := make([]string, len(neededCountries))
    copy(countriesCpy, neededCountries) //copies neededCountries to countriesCpy
    return countriesCpy
}
func main() {  
    countriesNeeded := countries()
    fmt.Println(countriesNeeded)
}
```
在 [playground](https://play.golang.org/p/35ayYBhcDE) 运行。

第九行。在上面的程序中，`neededCountries:= countries[:len(countries)-2]` 创建了一个包含后两个元素的 countries 切片。第十一行程序将 neededCountries 复制到 `countriesCpy `，并在下一行的函数中返回。现在 countries 数组可以被垃圾收集，因为需要的 countries  不再被引用。

这就是数组和切片。感谢你的阅读。请留下宝贵的反馈和意见。

## 下一章 - [可变参数函数](https://github.com/LeaningGo/go-learn/blob/master/go-variadic-functions.md)
