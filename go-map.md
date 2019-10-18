这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第十三部分。

## 什么是集合?

集合是 Go 中的内置类型，它将值与键相关联。可以使用相应的键来检索该值。

## 创建集合

可以通过将键和值的类型传递给 make 函数来创建集合。`make(map[type of key]type of value)` 是创建集合的语法。
```go
personSalary := make(map[string]int)  
```

上面的代码行创建了一个名为 `personSalary` 的集合，其中包含 string 键和 int 值。

集合的零值为 nil。如果试图将添加到 nil 集合，将会发生运行时恐慌。因此，必须使用 make 函数初始化集合。
```go
package main

import (  
    "fmt"
)

func main() {  
    var personSalary map[string]int
    if personSalary == nil {
        fmt.Println("map is nil. Going to make one.")
        personSalary = make(map[string]int)
    }
}
```
在 [playground](https://play.golang.org/p/IwJnXMGc1M) 运行。

在上面的程序中，personSalary 为 nil，因此使用 make 函数对其进行初始化。程序将输出 `map is nil. Going to make one`

## 向集合添加数据

向集合添加数据的语法与数组的语法相同。以下程序在 personSalary 集合上添加了一些新数据。
```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := make(map[string]int)
    personSalary["steve"] = 12000
    personSalary["jamie"] = 15000
    personSalary["mike"] = 9000
    fmt.Println("personSalary map contents:", personSalary)
}
```
在 [playground](https://play.golang.org/p/IwJnXMGc1M) 运行。

上面的程序输出， `personSalary map contents: map[steve:12000 jamie:15000 mike:9000]`

在声明本身期间也可以初始化集合。
```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := map[string]int {
        "steve": 12000,
        "jamie": 15000,
    }
    personSalary["mike"] = 9000
    fmt.Println("personSalary map contents:", personSalary)
}
```
在 [playground](https://play.golang.org/p/nlH_ADhO9f) 运行。

上面的程序声明了 personSalary，并在声明本身期间向其中添加了两个元素。mike 之后又添加了一个带有键的元素。程序输出
```
personSalary map contents: map[steve:12000 jamie:15000 mike:9000]  
```

不必只将字符串类型作为键。所有可比较的类型，例如布尔，整数，浮点数，复数，字符串等，也可以是键。如果您想进一步了解类似类型，请访问：http://golang.org/ref/spec#Comparison_operators

## 获取集合数据

现在我们已经向集合添加了一些元素，让我们学习如何检索它们。map[key] 是检索集合元素的语法。
```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := map[string]int{
        "steve": 12000,
        "jamie": 15000,
    }
    personSalary["mike"] = 9000
    employee := "jamie"
    fmt.Println("Salary of", employee, "is", personSalary[employee])
}
```
在 [playground](https://play.golang.org/p/-TSBac7F1v) 运行。

上面的程序非常简单。jamie 检索并打印员工的薪水。程序输出 `Salary of jamie is 15000`。

如果一个元素不存在会发生什么？集合将返回该元素类型的零值。在 personSalary 集合的情况下，如果我们试图访问一个不存在的元素，那么 int 的 0 值将会返回。
```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := map[string]int{
        "steve": 12000,
        "jamie": 15000,
    }
    personSalary["mike"] = 9000
    employee := "jamie"
    fmt.Println("Salary of", employee, "is", personSalary[employee])
    fmt.Println("Salary of joe is", personSalary["joe"])
}
```
在 [playground](https://play.golang.org/p/-TSBac7F1v) 运行。

上面程序的输出是
```
Salary of jamie is 15000  
Salary of joe is 0  
```

上面的程序将 joe 的薪水返回为 0。我们没有得到任何运行时错误，指出该键 joe 不存在于 personSalary 地图中。

如果我们想知道一个键是否存在于集合中，该怎么办?

```
value, ok := map[key]  
```

上面的语法用于检索某个键是否存在于集合中。如果 ok 为 true，否则不存在
```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := map[string]int{
        "steve": 12000,
        "jamie": 15000,
    }
    personSalary["mike"] = 9000
    newEmp := "joe"
    value, ok := personSalary[newEmp]
    if ok == true {
        fmt.Println("Salary of", newEmp, "is", value)
    } else {
        fmt.Println(newEmp,"not found")
    }

}
```
在 [playground](https://play.golang.org/p/q8fL6MeVZs) 运行。

在上述程序中，第十五行 ok 是 false，因为 joe 不存在。因此程序会输出：
```
joe not found  
```
for 循环的 range 形式用于遍历集合的所有元素。
```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := map[string]int{
        "steve": 12000,
        "jamie": 15000,
    }
    personSalary["mike"] = 9000
    fmt.Println("All items of a map")
    for key, value := range personSalary {
        fmt.Printf("personSalary[%s] = %d\n", key, value)
    }

}
```
在 [playground](https://play.golang.org/p/gq9ZOKsI9b) 运行。

上面程序的输出是
```
All items of a map  
personSalary[mike] = 9000  
personSalary[steve] = 12000  
personSalary[jamie] = 15000  
```
**一个重要的事实是，在使用 for range 时，从集合中检索值的顺序不能保证在每次执行程序时都相同。**

## 删除数据
*delete(map, key)* 是从 map 中删除键的语法。delete 函数不返回任何值。
```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := map[string]int{
        "steve": 12000,
        "jamie": 15000,
    }
    personSalary["mike"] = 9000
    fmt.Println("map before deletion", personSalary)
    delete(personSalary, "steve")
    fmt.Println("map after deletion", personSalary)

}
```
在 [playground](https://play.golang.org/p/nroJzeF-a7) 运行。

上面的程序删除键 "steve" 并输出
```
map before deletion map[steve:12000 jamie:15000 mike:9000]  
map after deletion map[mike:9000 jamie:15000]  
```

## 集合长度
可以使用 len() 函数来确定集合的长度
```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := map[string]int{
        "steve": 12000,
        "jamie": 15000,
    }
    personSalary["mike"] = 9000
    fmt.Println("length is", len(personSalary))

}
```
在 [playground](https://play.golang.org/p/nroJzeF-a7) 运行。

上面程序中的 *len(personSalary)* 确定集合的长度。上面的程序输出：`length is 3`

## 集合是引用类型
与切片类似，集合也是引用类型。当将集合分配给新变量时，它们都指向相同的内部数据结构。因此，在一个方面做出的改变将映射在另一个方面。

```go
package main

import (  
    "fmt"
)

func main() {  
    personSalary := map[string]int{
        "steve": 12000,
        "jamie": 15000,
    }
    personSalary["mike"] = 9000
    fmt.Println("Original person salary", personSalary)
    newPersonSalary := personSalary
    newPersonSalary["mike"] = 18000
    fmt.Println("Person salary changed", personSalary)

}
```
在 [playground](https://play.golang.org/p/OGFl3addq1) 运行。

在上述程序第十四行中，personSalary 被分配给 newPersonSalary。在下一行,  mike 的工资改为 18000 在 newPersonSalary 集合。mike 现在的工资也将是 18000 英镑。程序输出：
```go
Original person salary map[steve:12000 jamie:15000 mike:9000]  
Person salary changed map[steve:12000 jamie:15000 mike:18000]  
```

类似的情况还有将集合作为参数传递给函数。当对函数内部的集合进行任何更改时，调用者都可以看到它。

## 集合 equality
不能使用 `==` 操作符操作集合，`==` 只能用来检查映射是否为 nil
```go
package main

func main() {  
    map1 := map[string]int{
        "one": 1,
        "two": 2,
    }

    map2 := map1

    if map1 == map2 {
    }
}
```
在 [playground](https://play.golang.org/p/MALqDyWkcT) 运行。

面的程序将抛出编译错误：**invalid operation: map1 == map2 (map can only be compared to nil).**

检查两个集合是否相等的一种方法是一个一个的比较每个的单个元素。希望你为此编写一个程序并使它起作用 :)

集合就是这样。谢谢阅读。祝你有美好的一天。

## 下一个教程 - [字符串](https://github.com/LeaningGo/go-learn/blob/master/go-strings.md)
