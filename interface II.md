这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第十九部分。
这里是接口 II，如果你没有阅读接口 I 可以访问[接口 I](https://github.com/LeaningGo/go-learn/blob/master/interface%20I.md) 阅读。

## 使用指针接收和值接收实现接口

我们在[第 I 部分](https://github.com/LeaningGo/go-learn/blob/master/interface%20I.md)中讨论的所有示例接口都是使用值接收实现的。也可以使用指针接收来实现接口。使用指针接收实现接口时要注意一个细微之处。查看一下示例。
```go
package main

import "fmt"

type Describer interface {  
    Describe()
}
type Person struct {  
    name string
    age  int
}

func (p Person) Describe() { //implemented using value receiver  
    fmt.Printf("%s is %d years old\n", p.name, p.age)
}

type Address struct {  
    state   string
    country string
}

func (a *Address) Describe() { //implemented using pointer receiver  
    fmt.Printf("State %s Country %s", a.state, a.country)
}

func main() {  
    var d1 Describer
    p1 := Person{"Sam", 25}
    d1 = p1
    d1.Describe()
    p2 := Person{"James", 32}
    d1 = &p2
    d1.Describe()

    var d2 Describer
    a := Address{"Washington", "USA"}

    /* compilation error if the following line is
       uncommented
       cannot use a (type Address) as type Describer
       in assignment: Address does not implement
       Describer (Describe method has pointer
       receiver)
    */
    //d2 = a

    d2 = &a //This works since Describer interface
    //is implemented by Address pointer in line 22
    d2.Describe()

}
```
在 [playground](https://play.golang.org/p/IzspYiAQ82) 运行程序。

在上面的程序中，Person 结构体使用值接收在第十三行中实现了 Describer 接口。

正如我们在讨论[方法](https://github.com/LeaningGo/go-learn/blob/master/go-methods.md)时已经了解到的一样，带有值接收的方法同时接受指针和值。*对任何值或其值可以引用的值调用值方法都是合法的。*

p1 是 Person 类型的值，在第二十九行中分配给 d1。Person 实现 d1 接口，因此第三十行将打印 `Sam is 25 years old`。

类似地，第三十二行 d1 分配给 &p2。第三十三行将打印 `James is 32 years old`

`Address` 结构体在第二十二行中使用指针接收实现 `Describer` 接口。

如果上面程序的第四十五行没有注释，我们将得到编译错误：**main.go:42: cannot use a (type Address) as type Describer in assignment: Address does not implement Describer (Describe method has pointer receiver)**，这是因为：Describer 接口是在第二十二行使用地址指针接收实现的，我们试图分配一个值类型，它还没有实现 Describer 接口。这肯定会让您感到惊讶，因为我们之前已经了解到，带有指针接收的方法将同时接受指针和值。那为什么四十五行的代码不能工作？

原因是，**对已经是指针或可以获取其地址的任何对象调用指针值方法都是合法的。接口中存储的具体值是不可寻址的，因此编译器不可能在四十五行中自动获取 a 的地址，因此这段代码失败**。

第四十七行是有效的，因为我们把 &a 的地址分配给了 d2。

程序的其余部分不言而喻。这个程序会打印
```
Sam is 25 years old  
James is 32 years old  
State Washington Country USA  
```

## 实现多个接口

一个类型可以实现多个接口。让我们看看这是如何在下面的程序中完成的。

```go
package main

import (  
    "fmt"
)

type SalaryCalculator interface {  
    DisplaySalary()
}

type LeaveCalculator interface {  
    CalculateLeavesLeft() int
}

type Employee struct {  
    firstName string
    lastName string
    basicPay int
    pf int
    totalLeaves int
    leavesTaken int
}

func (e Employee) DisplaySalary() {  
    fmt.Printf("%s %s has salary $%d", e.firstName, e.lastName, (e.basicPay + e.pf))
}

func (e Employee) CalculateLeavesLeft() int {  
    return e.totalLeaves - e.leavesTaken
}

func main() {  
    e := Employee {
        firstName: "Naveen",
        lastName: "Ramanathan",
        basicPay: 5000,
        pf: 200,
        totalLeaves: 30,
        leavesTaken: 5,
    }
    var s SalaryCalculator = e
    s.DisplaySalary()
    var l LeaveCalculator = e
    fmt.Println("\nLeaves left =", l.CalculateLeavesLeft())
}
```
在 [playground](https://play.golang.org/p/DJxS5zxBcV) 运行程序。

上面的程序有两个接口 SalaryCalculator，LeaveCalculator 分别在第七行和第十一行声明。

第十五行中定义的 Employee 结构体在二十四行中提供了 SalaryCalculator 接口的 DisplaySalary 方法的实现，在二十八行中提供了 LeaveCalculator 接口的 CalculateLeavesLeft 方法的实现。现在 Employee 实现了 SalaryCalculator 和 leave ecalculator 接口。

在四十一行中，我们将 e 赋值给类型为 SalaryCalculator 接口的变量，在四十三行中，我们将相同的变量 e 赋值给类型为 LeaveCalculator 的变量。这是可能的，因为 e 类型的员工实现 SalaryCalculator 和 LeaveCalculator 接口。

上面程序的输出是：
```
Naveen Ramanathan has salary $5200  
Leaves left = 25  
```

## 嵌入接口
尽管 go 不提供继承，但是可以通过嵌入其他接口来创建新接口。

让我们看看这是如何完成的。

```go
package main

import (  
    "fmt"
)

type SalaryCalculator interface {  
    DisplaySalary()
}

type LeaveCalculator interface {  
    CalculateLeavesLeft() int
}

type EmployeeOperations interface {  
    SalaryCalculator
    LeaveCalculator
}

type Employee struct {  
    firstName string
    lastName string
    basicPay int
    pf int
    totalLeaves int
    leavesTaken int
}

func (e Employee) DisplaySalary() {  
    fmt.Printf("%s %s has salary $%d", e.firstName, e.lastName, (e.basicPay + e.pf))
}

func (e Employee) CalculateLeavesLeft() int {  
    return e.totalLeaves - e.leavesTaken
}

func main() {  
    e := Employee {
        firstName: "Naveen",
        lastName: "Ramanathan",
        basicPay: 5000,
        pf: 200,
        totalLeaves: 30,
        leavesTaken: 5,
    }
    var empOp EmployeeOperations = e
    empOp.DisplaySalary()
    fmt.Println("\nLeaves left =", empOp.CalculateLeavesLeft())
}
```
在 [playground](https://play.golang.org/p/Hia7D-WbZp) 运行程序。

上面程序第十五行中的 EmployeeOperations 接口是通过嵌入 SalaryCalculator 和 LeaveCalculator 接口创建的。

如果任何类型为 SalaryCalculator 和 LeaveCalculator 接口中出现的方法提供了方法定义，则称为实现 EmployeeOperations 接口。

Employee 结构体实现了 EmployeeOperations 接口，因为它在第 29 行和第 33 行分别为 DisplaySalary 和 CalculateLeavesLeft 方法提供了定义。

在四十六行中，类型 Employee 的 e 被分配给类型 EmployeeOperations 的 empOp。在接下来的两行中，在 empOp 上调用 DisplaySalary() 和 CalculateLeavesLeft() 方法。

上面程序的输入是：
```
Naveen Ramanathan has salary $5200  
Leaves left = 25  
```

## 接口的零值
接口的零值为 nil。nil 接口既有它的基础值，也有具体类型。

```go
package main

import "fmt"

type Describer interface {  
    Describe()
}

func main() {  
    var d1 Describer
    if d1 == nil {
        fmt.Printf("d1 is nil and has type %T value %v\n", d1, d1)
    }
}
```
在 [playground](https://play.golang.org/p/vwYHC6Y78H) 运行程序。

上面程序中的 d1 是 nil，该程序将输出
```
d1 is nil and has type <nil> value <nil>  
```

如果我们尝试在 nil 接口上调用方法，则程序将出现恐慌，因为 nil 接口既没有基础值也没有具体类型。

```
package main

type Describer interface {  
    Describe()
}

func main() {  
    var d1 Describer
    d1.Describe()
}
```
在 [playground](https://play.golang.org/p/rM-rY0uGTI) 运行程序。

由于上述程序中的 d1 为 nil，因此该程序将因运行时错误而出现故障
**panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=0xffffffff addr=0x0 pc=0xc8527]"**

这就是接口。祝你有美好的一天 :)

## 下一个教程 - [并发介绍](https://github.com/LeaningGo/go-learn/blob/master/Introduction%20to%20Concurrency.md)
