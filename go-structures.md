这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第十六部分。

## 什么是结构体

结构体是表示字段集合的用户定义类型。它可以用于将数据分组到单个单元中，而不是将每个单元维护为单独的类型。

例如，一名员工，有姓、名和年龄。将这三个属性分组到单个结构 employee 中是有意义的。

## 声明结构体

```go
type Employee struct {  
    firstName string
    lastName  string
    age       int
}
```

上面的代码段声明了一个结构类型 Employee，其中包含字段 firstName、lastName 和 age。通过在类型名称后面的单行中声明属于同一类型的字段，还可以使该结构更加紧凑。在上面的结构中，firstName 和 lastName 属于同一类型的字符串，因此结构可以重写为。

```go
type Employee struct {  
    firstName, lastName string
    age, salary         int
}
```

上面的 Employee 结构体被称为命名结构，因为它创建了一个名为 Employee 的新类型，可以用来创建 Employee 类型的结构。

可以在不声明新类型的情况下声明结构，这些类型的结构称为**匿名结构**。
```go
var employee struct {  
        firstName, lastName string
        age int
}
```
上面的代码段创建了一个匿名结构 `employee`。

## 创建命名结构

让我们使用一个简单的程序定义一个命名结构 Employee。

```go
package main

import (  
    "fmt"
)

type Employee struct {  
    firstName, lastName string
    age, salary         int
}

func main() {

    //creating structure using field names
    emp1 := Employee{
        firstName: "Sam",
        age:       25,
        salary:    500,
        lastName:  "Anderson",
    }

    //creating structure without using field names
    emp2 := Employee{"Thomas", "Paul", 29, 800}

    fmt.Println("Employee 1", emp1)
    fmt.Println("Employee 2", emp2)
}
```
在 [playground](https://play.golang.org/p/uhPAHeUwvK) 运行。

在上面程序第七行中，我们创建了一个命名结构 Employee 的结构体。第十五行，`emp1` 通过为每个字段名称指定值来定义结构。在声明结构类型时，字段名的顺序不必与结构类型相同。这里我们改变了 lastName 的位置，将它移到了最后。工作流程没有任何问题。

在上面程序的第二十三行中，emp2 通过省略字段名称来定义。在这种情况下，必须保持字段顺序与结构声明中指定的顺序相同。

上面程序的输出是：
```
Employee 1 {Sam Anderson 25 500}  
Employee 2 {Thomas Paul 29 800}  
```

## 创建匿名结构

```go
package main

import (  
    "fmt"
)

func main() {  
    emp3 := struct {
        firstName, lastName string
        age, salary         int
    }{
        firstName: "Andreah",
        lastName:  "Nikola",
        age:       31,
        salary:    5000,
    }

    fmt.Println("Employee 3", emp3)
}
```
在 [playground](https://play.golang.org/p/TEMFM3oZiq) 运行。


在上述程序的第八行中，定义了一个匿名结构变量 emp3。正如我们已经提到的，该结构称为匿名结构，因为它仅创建一个新的 struct 变量 emp3，而没有定义任何新的结构类型。

程序的输出是：
```
Employee 3 {Andreah Nikola 31 5000}  
```

## 结构的零值
当定义了一个结构并且未显式初始化任何值时，默认情况下为该结构的字段分配其零值。

```go
package main

import (  
    "fmt"
)

type Employee struct {  
    firstName, lastName string
    age, salary         int
}

func main() {  
    var emp4 Employee //zero valued structure
    fmt.Println("Employee 4", emp4)
}
```
在 [playground](https://play.golang.org/p/p7_OpVdFXJ) 运行。

上面的程序定义了 emp4，但是它没有初始化任何值。因此，firstName 和 lastName 被赋值为字符串 "" 的零值，age 和 salary 被赋值为 int 的零值，int 是 0。这个程序输出
```
Employee 4 {  0 0}  
```

也可以为某些字段指定值，而忽略其他字段。在本例中，忽略的字段名被赋零值。

```go
package main

import (  
    "fmt"
)

type Employee struct {  
    firstName, lastName string
    age, salary         int
}

func main() {  
    emp5 := Employee{
        firstName: "John",
        lastName:  "Paul",
    }
    fmt.Println("Employee 5", emp5)
}
```
在 [playground](https://play.golang.org/p/w2gPoCnlZ1) 运行。

在上述程序十四行和十五行中，firstName 和 lastName 都有名字，而 age 和 salary 没有。因此，age 和 salary 被赋值为零。这个程序输出,

```
Employee 5 {John Paul 0 0}  
```

## 访问结构体的各个字段
`.` 运算符用于访问结构体的各个字段。
```go
package main

import (  
    "fmt"
)

type Employee struct {  
    firstName, lastName string
    age, salary         int
}

func main() {  
    emp6 := Employee{"Sam", "Anderson", 55, 6000}
    fmt.Println("First Name:", emp6.firstName)
    fmt.Println("Last Name:", emp6.lastName)
    fmt.Println("Age:", emp6.age)
    fmt.Printf("Salary: $%d", emp6.salary)
}
```
在 [playground](https://play.golang.org/p/GPd_sT85IS) 运行。

上面程序中的 emp6.firstName 访问 emp6 结构体 的 firstName  字段。该程序输出
```
First Name: Sam  
Last Name: Anderson  
Age: 55  
Salary: $6000  
```

也可以创建一个零结构体，然后在以后为其字段分配值。

```go
package main

import (  
    "fmt"
)

type Employee struct {  
    firstName, lastName string
    age, salary         int
}

func main() {  
    var emp7 Employee
    emp7.firstName = "Jack"
    emp7.lastName = "Adams"
    fmt.Println("Employee 7:", emp7)
}
```
在 [playground](https://play.golang.org/p/ZEOx10g7nN) 运行。

在上面的程序 emp7 被后面定义，然后 firstName 与 lastName 被分配值。该程序输出
```
Employee 7: {Jack Adams 0 0}  
```

## 指向结构体的指针
```go
package main

import (  
    "fmt"
)

type Employee struct {  
    firstName, lastName string
    age, salary         int
}

func main() {  
    emp8 := &Employee{"Sam", "Anderson", 55, 6000}
    fmt.Println("First Name:", (*emp8).firstName)
    fmt.Println("Age:", (*emp8).age)
}
```
在 [playground](https://play.golang.org/p/xj87UCnBtH) 运行。

上面程序中的 emp8 是指向 Employee 结构体的指针。(* emp8).firstName 是访问 emp8 结构体的 firstName 字段的语法。这个程序输出
```go
First Name: Sam  
Age: 55  
```

该语言使我们可以选择使用 emp8.firstName 而不是显式引用 (*emp8).firstName 来访问 firstName 字段。
```go
package main

import (  
    "fmt"
)

type Employee struct {  
    firstName, lastName string
    age, salary         int
}

func main() {  
    emp8 := &Employee{"Sam", "Anderson", 55, 6000}
    fmt.Println("First Name:", emp8.firstName)
    fmt.Println("Age:", emp8.age)
}
```
在 [playground](https://play.golang.org/p/0ZE265qQ1h) 运行。

我们曾经在上述程序中 emp8.firstName 访问过 firstName 字段，并且该程序还会输出
```
First Name: Sam  
Age: 55  
```

## 匿名字段

可以使用仅包含类型而不包含字段名称的字段来创建结构。这些字段称为匿名字段。

下面的代码段创建了一个结构 Person，该结构体具有两个匿名字段 string 和 int

```go
type Person struct {  
    string
    int
}
```

让我们使用匿名字段编写程序。

```go
package main

import (  
    "fmt"
)

type Person struct {  
    string
    int
}

func main() {  
    p := Person{"Naveen", 50}
    fmt.Println(p)
}
```
在 [playground](https://play.golang.org/p/YF-DgdVSrC) 运行。

在上面的程序中，Person 带有两个匿名字段的结构。p := Person{"Naveen", 50} 定义类型的变量 Person。该程序输出 {Naveen 50}。

即使匿名字段没有名称，默认情况下，匿名字段的名称也是其类型的名称。例如，对于上面的 Person 结构，尽管字段是匿名的，但默认情况下，它们使用字段类型的名称。因此 Person 结构体有两个字段，名称分别为 string 和 int。

```go
package main

import (  
    "fmt"
)

type Person struct {  
    string
    int
}

func main() {  
    var p1 Person
    p1.string = "naveen"
    p1.int = 50
    fmt.Println(p1)
}
```
在 [playground](https://play.golang.org/p/K-fGNxVyiA) 运行。

在上面程序第十四行和第十五行中，我们使用 Person 结构体的匿名字段的类型作为字段名，分别是 "string" 和 "int"。上述程序的输出为:
```
{naveen 50}
```

## 结构体嵌套

```go
package main

import (  
    "fmt"
)

type Address struct {  
    city, state string
}
type Person struct {  
    name string
    age int
    address Address
}

func main() {  
    var p Person
    p.name = "Naveen"
    p.age = 50
    p.address = Address {
        city: "Chicago",
        state: "Illinois",
    }
    fmt.Println("Name:", p.name)
    fmt.Println("Age:",p.age)
    fmt.Println("City:",p.address.city)
    fmt.Println("State:",p.address.state)
}
```
在 [playground](https://play.golang.org/p/46jkQFdTPO) 运行。

上面程序中的 Person 结构有一个 address 地址，而 address 地址又是一个结构体。这个程序输出

```
Name: Naveen  
Age: 50  
City: Chicago  
State: Illinois  
```

## 提升字段

结构中属于匿名结构字段的字段称为提升字段，因为可以像访问包含匿名结构体字段的结构一样访问它们。我可以理解这个定义是相当复杂的，所以让我们直接进入一些代码来理解这个 :)
```go
type Address struct {  
    city, state string
}
type Person struct {  
    name string
    age  int
    Address
}
```

在上面的代码片段中，Person 结构体具有一个匿名字段 Address，它是一个结构体。现在的场 Address 结构体，即 city 与 state 被称为提升字段，因为它们可以像直接在 Person 结构本身中声明的那样被访问。

```go
package main

import (  
    "fmt"
)

type Address struct {  
    city, state string
}
type Person struct {  
    name string
    age  int
    Address
}

func main() {  
    var p Person
    p.name = "Naveen"
    p.age = 50
    p.Address = Address{
        city:  "Chicago",
        state: "Illinois",
    }
    fmt.Println("Name:", p.name)
    fmt.Println("Age:", p.age)
    fmt.Println("City:", p.city) //city is promoted field
    fmt.Println("State:", p.state) //state is promoted field
}
```
在 [playground](https://play.golang.org/p/OgeHCJYoEy) 运行。

在上面程序的第二十七行和第二十六行中，提升字段 city 和 state 被访问，就好像它们是在结构体 p 本身中使用语法 p 声明的一样。p.city 和 p.state。这个程序输出：
```
Name: Naveen  
Age: 50  
City: Chicago  
State: Illinois  
```

## 访问结构体和字段
如果结构类型以大写字母开头，则它是导出的类型，可以从其他包中访问它。类似地，如果结构的字段以大写字母开头，则可以从其他包访问它们。

让我们编写一个包含自定义程序包的程序，以更好地理解它。

在 go 工作空间的 src 目录中创建一个名为 structs 的文件夹。在 structs 中创建另一个目录 computer。

在 computer 目录中，用文件名 spec.go 保存下面的程序
```go
package computer

type Spec struct { //exported struct  
    Maker string //exported field
    model string //unexported field
    Price int //exported field
}
```

上面的代码片段创建了一个 computer 包，其中包含一个可被外部包访问的的结构体类型规范，两个访问的字段 Maker 和 Price 以及一个未导出的字段模型。让我们从主包中导入这个包并使用 Spec 结构体。

创建一个名为 main.go 文件。进入 structs 目录并在 main.go 中编写以下程序

```go
package main

import "structs/computer"  
import "fmt"

func main() {  
    var spec computer.Spec
    spec.Maker = "apple"
    spec.Price = 50000
    fmt.Println("Spec:", spec)
}
```

包的结构体应该如下所示

```go
src  
  structs
    computer
      spec.go
    main.go
```
在上述程序的第三行中，我们导入了 computer 包。在第八行和第九行，我们访问这两个公共变量 Spec 结构体的 Maker 和 Price。该程序可以通过执行命令来执行 go install structs 后面跟着 workspacepath/bin/structs

程序的输出是: `Spec: {apple  50000}`

如果我们尝试访问未公开的字段 model，则编译器会恐慌。用 main.go 以下代码替换的内容。

```go
package main

import "structs/computer"  
import "fmt"

func main() {  
    var spec computer.Spec
    spec.Maker = "apple"
    spec.Price = 50000
    spec.model = "Mac Mini"
    fmt.Println("Spec:", spec)
}
```
在上述程序第十行中，我们尝试访问未公开字段 model。运行此程序将导致编译错误 **spec.model 未定义公开，即：`cannot refer to unexported field or method model`**

## 结构体等式

结构体是值类型，它们是可比较的。如果两个结构体变量对应的字段相等，则认为它们是相同的。

```go
package main

import (  
    "fmt"
)

type name struct {  
    firstName string
    lastName string
}


func main() {  
    name1 := name{"Steve", "Jobs"}
    name2 := name{"Steve", "Jobs"}
    if name1 == name2 {
        fmt.Println("name1 and name2 are equal")
    } else {
        fmt.Println("name1 and name2 are not equal")
    }

    name3 := name{firstName:"Steve", lastName:"Jobs"}
    name4 := name{}
    name4.firstName = "Steve"
    if name3 == name4 {
        fmt.Println("name3 and name4 are equal")
    } else {
        fmt.Println("name3 and name4 are not equal")
    }
}
```
在 [playground](https://play.golang.org/p/AU1FkdsPk7) 运行。

在上面的程序中，`name` 结构体类型包含两个字符串字段。由于字符串是可比较的，所以可以比较类型为 name 的两个结构体变量。

在上面的程序中，name1 和 name2 是相等的，而 name3 和 name4 不是。这个程序将输出

```
name1 and name2 are equal  
name3 and name4 are not equal  
```

如果结构体变量包含不具有可比性的字段，那么它们就不具有可比性！

```go
package main

import (  
    "fmt"
)

type image struct {  
    data map[int]int
}

func main() {  
    image1 := image{data: map[int]int{
        0: 155,
    }}
    image2 := image{data: map[int]int{
        0: 155,
    }}
    if image1 == image2 {
        fmt.Println("image1 and image2 are equal")
    }
}
```
在 [playground](https://play.golang.org/p/T4svXOTYSg) 运行。

在上面的程序中，`image` 结构体类型包含一个类型为 map 的字段数据。map 是不可比较的，因此不能比较 image1 和 image2。如果运行此程序，编译将失败，并带有 `main.go:18: invalid operation: image1 == image2 (struct containing map[int]int cannot be compared)`

Go 中的结构体已经结束了。祝你有美好的一天。

## 下一个教程 - [Methods（方法）](https://github.com/LeaningGo/go-learn/blob/master/go-methods.md)
