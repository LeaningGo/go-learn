
这里是 [golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第十七部分。

## 入门

方法只是在 func 关键字和方法名之间具有特殊接收器类型的[函数](https://github.com/LeaningGo/go-learn/blob/master/go-functions.md)。接收方可以是结构体类型，也可以是非结构体类型。

下面提供了方法声明的语法。

```go
func (t Type) methodName(parameter list) {  
}
```

上面的代码段创建了一个名为 methodName 的方法，该方法具有接收者类型。调用 t 作为接收方，可以在方法中访问它。（题外话，译者在这里认为方法就相当于 Java 中的继承关系，上面的代码用 Java 语言来说就是：methodName 继承了 Type）

## 方法示例
让我们编写一个简单的程序，在结构体类型上创建一个方法并调用它。
```go
package main

import (  
    "fmt"
)

type Employee struct {  
    name     string
    salary   int
    currency string
}

/*
 displaySalary() method has Employee as the receiver type
*/
func (e Employee) displaySalary() {  
    fmt.Printf("Salary of %s is %s%d", e.name, e.currency, e.salary)
}

func main() {  
    emp1 := Employee {
        name:     "Sam Adolf",
        salary:   5000,
        currency: "$",
    }
    emp1.displaySalary() //Calling displaySalary() method of Employee type
}
```
在 [playground](https://play.golang.org/p/rRsI_sWAOZ) 上运行程序。

在上面程序的第十六行中，我们在 Employee 结构体类型上创建了一个方法 displaySalary。displaySalary() 方法可以访问其中的接收者 e。我们使用收款人 e，打印员工的 name、currency 和 salary。

我们在第二十六行使用该方法调用了 emp1.displaySalary()

上面程序的输出是 `Salary of Sam Adolf is $5000`

## 方法 VS 函数
可以只使用函数重写上面的程序，而不使用方法。

```go
package main

import (  
    "fmt"
)

type Employee struct {  
    name     string
    salary   int
    currency string
}

/*
 displaySalary() method converted to function with Employee as parameter
*/
func displaySalary(e Employee) {  
    fmt.Printf("Salary of %s is %s%d", e.name, e.currency, e.salary)
}

func main() {  
    emp1 := Employee{
        name:     "Sam Adolf",
        salary:   5000,
        currency: "$",
    }
    displaySalary(emp1)
}
```
在 [playground](https://play.golang.org/p/dFwObgCUU0) 上运行程序。

在上面的程序中，displaySalary 方法转换为函数，并将 Employeestruct 作为参数传递给它。该程序还产生完全相同的输出 `Salary of Sam Adolf is $5000`。

那么为什么我们可以使用函数编写相同的程序时就拥有方法呢？这有两个原因。让我们一一看一下。

* [Go 不是纯粹的面向对象的编程语言](https://golang.org/doc/faq#Is_Go_an_object-oriented_language)，它不支持类。因此，类型上的方法是实现类似于类的行为的一种方法。方法允许与类似于类的类型相关的行为的逻辑分组。在上面的示例程序中，通过使用 Employee receiver 类型创建方法，可以对与 Employee 类型相关的所有行为进行分组。例如，我们可以添加 calculatePension、calculateLeaves 等方法。
* 具有相同名称的方法可以在不同类型上定义，而具有相同名称的函数是不允许的。假设我们有一个正方形和圆形的结构体。可以在 Square 和 Circle 上定义一个名为 Area 的方法。这是在下面的程序中完成的。
```go
package main

import (  
    "fmt"
    "math"
)

type Rectangle struct {  
    length int
    width  int
}

type Circle struct {  
    radius float64
}

func (r Rectangle) Area() int {  
    return r.length * r.width
}

func (c Circle) Area() float64 {  
    return math.Pi * c.radius * c.radius
}

func main() {  
    r := Rectangle{
        length: 10,
        width:  5,
    }
    fmt.Printf("Area of rectangle %d\n", r.Area())
    c := Circle{
        radius: 12,
    }
    fmt.Printf("Area of circle %f", c.Area())
}
```
在 [playground](https://play.golang.org/p/0hDM3E3LiP) 上运行程序。

上面的程序打印

```go
Area of rectangle 50  
Area of circle 452.389342  
```

方法的上述属性用于实现接口。我们将在下一篇教程中详细讨论接口。

## 指针接收器 vs 值接收器（Pointer Receivers vs Value Receivers）
到目前为止，我们只看到了带有值接收器的方法。可以使用指针接收器创建方法。值和指针接收器之间的区别是，**在方法内部使用指针接收器所做的更改对调用者是可见的，而在值接收器中则不是这样**。让我们通过一个程序来理解这一点。
```go
package main

import (  
    "fmt"
)

type Employee struct {  
    name string
    age  int
}

/*
Method with value receiver  
*/
func (e Employee) changeName(newName string) {  
    e.name = newName
}

/*
Method with pointer receiver  
*/
func (e *Employee) changeAge(newAge int) {  
    e.age = newAge
}

func main() {  
    e := Employee{
        name: "Mark Andrew",
        age:  50,
    }
    fmt.Printf("Employee name before change: %s", e.name)
    e.changeName("Michael Andrew")
    fmt.Printf("\nEmployee name after change: %s", e.name)

    fmt.Printf("\n\nEmployee age before change: %d", e.age)
    (&e).changeAge(51)
    fmt.Printf("\nEmployee age after change: %d", e.age)
}
```
在 [playground](https://play.golang.org/p/tTO100HmUX) 上运行程序。

在上面的程序中，changeName 方法有一个值接收器 (e Employee)，而 changeAge 方法有一个指针接收器 (e *Employee)。在 changeName 中对 Employee 结构体的 name 字段所做的更改对调用者是不可见的，因此程序会在方法 e 之前和之后打印相同的 name。因为 changeAge 方法有一个指针接收器 (e *Employee)，所以在方法调用 (&e) 之后对 age 字段所做的更改 (51) 将对调用者可见。这个程序打印
```
Employee name before change: Mark Andrew  
Employee name after change: Mark Andrew

Employee age before change: 50  
Employee age after change: 51  
```

在上面程序的第三十六行中，我们使用 (&e).changeAge(51) 来调用 changeAge 方法。因为 changeAge 有一个指针接收器，所以我们使用 (&e) 来调用这个方法。这里实际是不需要的，该语言让我们可以选择使用 e.changeAge(51)。e.changeAge(51) 在语言上会被解释为 (&e). changeage(51)。

下面的程序被重写为使用 e.changeAge(51) 而不是 (&e).changeage(51)，并打印相同的输出。
```go
package main

import (  
    "fmt"
)

type Employee struct {  
    name string
    age  int
}

/*
Method with value receiver  
*/
func (e Employee) changeName(newName string) {  
    e.name = newName
}

/*
Method with pointer receiver  
*/
func (e *Employee) changeAge(newAge int) {  
    e.age = newAge
}

func main() {  
    e := Employee{
        name: "Mark Andrew",
        age:  50,
    }
    fmt.Printf("Employee name before change: %s", e.name)
    e.changeName("Michael Andrew")
    fmt.Printf("\nEmployee name after change: %s", e.name)

    fmt.Printf("\n\nEmployee age before change: %d", e.age)
    e.changeAge(51)
    fmt.Printf("\nEmployee age after change: %d", e.age)
}
```
在 [playground](https://play.golang.org/p/nnXBsR3Uc8) 上运行程序。

## 什么时候使用指针接收与值接收

通常，当方法内部对接收器所做的更改应该对调用者可见时，可以使用指针接收。

指针接收也可以用在复制数据结构内存开销比较大的地方。考虑一个有许多字段的结构。将这个结构体用作方法中的值接收器将需要复制整个结构体，这将导致内存开销大。在这种情况下，如果使用指针接收，则不会复制结构体，并且在方法中只会使用指向结构体的指针。

在所有其他情况下，都可以使用值接收器。

## 匿名结构体字段的方法

可以调用属于结体构匿名字段的方法，就像它们属于定义匿名字段的结构体一样。
```go
package main

import (  
    "fmt"
)

type address struct {  
    city  string
    state string
}

func (a address) fullAddress() {  
    fmt.Printf("Full address: %s, %s", a.city, a.state)
}

type person struct {  
    firstName string
    lastName  string
    address
}

func main() {  
    p := person{
        firstName: "Elon",
        lastName:  "Musk",
        address: address {
            city:  "Los Angeles",
            state: "California",
        },
    }

    p.fullAddress() //accessing fullAddress method of address struct

}
```
在 [playground](https://play.golang.org/p/vURnImw4_9) 上运行程序。

在上方程序的第三十二行中，我们使用 p.fullAddress() 调用 address 结构体的 fullAddress() 方法。不需要显式的 address.fulladdress()。这个程序打印

```
Full address: Los Angeles, California  
```

## 方法中的值接收 vs 函数中的值参数

这个话题绊倒了大多数新手。我将尽力使它尽可能清晰 :)

当函数具有值参数时，它将仅接受值参数。

当方法具有值接收时，它将接受指针和值接收器。

让我们通过一个例子来理解这一点。

```go
package main

import (  
    "fmt"
)

type rectangle struct {  
    length int
    width  int
}

func area(r rectangle) {  
    fmt.Printf("Area Function result: %d\n", (r.length * r.width))
}

func (r rectangle) area() {  
    fmt.Printf("Area Method result: %d\n", (r.length * r.width))
}

func main() {  
    r := rectangle{
        length: 10,
        width:  5,
    }
    area(r)
    r.area()

    p := &r
    /*
       compilation error, cannot use p (type *rectangle) as type rectangle 
       in argument to area  
    */
    //area(p)

    p.area()//calling value receiver with a pointer
}
```
在 [playground](https://play.golang.org/p/gLyHMd2iie) 上运行程序。

第十二行中的 `func area(r rectangle)` 接受值参数，第十六行中的方法 `func (r rectangle) area()` 接受值接收器。

在第二十五行，我们调用带有值参数 area(r) 的 area 函数，它会起作用。类似地，我们使用一个值接收器调用 area 方法 r.area()，这也可以。

我们在第二十八行中创建一个指向 r 的指针 p，如果我们试图把这个指针传递给只接受一个值的函数去，编译器会报错。我已经注释了第三十三行。如果你取消注释这行，那么编译器将抛出错误编译错误，**compilation error, cannot use p (type *rectangle) as type rectangle in argument to area**。这和预期的一样。

现在到了棘手的部分，第三十五行代码 p.area( ) 调用方法，该方法接受使用指针接收器 p 的值接收。这是完全有效的。原因是 p.area() 将被 Go 解释为 (*p).area() ，因为 area 有一个值接收器。

上面程序的输出是：
```
Area Function result: 50  
Area Method result: 50  
Area Method result: 50  
```

## 方法中的指针接收 vs 函数中的指针参数

与值参数类似，具有指针参数的函数将仅接受指针，而具有指针接收器的方法将接受指针和值接收器。

```go
package main

import (  
    "fmt"
)

type rectangle struct {  
    length int
    width  int
}

func perimeter(r *rectangle) {  
    fmt.Println("perimeter function output:", 2*(r.length+r.width))

}

func (r *rectangle) perimeter() {  
    fmt.Println("perimeter method output:", 2*(r.length+r.width))
}

func main() {  
    r := rectangle{
        length: 10,
        width:  5,
    }
    p := &r //pointer to r
    perimeter(p)
    p.perimeter()

    /*
        cannot use r (type rectangle) as type *rectangle in argument to perimeter
    */
    //perimeter(r)

    r.perimeter()//calling pointer receiver with a value

}
```
在 [playground](https://play.golang.org/p/Xy5wW9YZMJ) 上运行程序。

上面程序第十二行定义了一个接受指针参数的函数 `perimeter`，而第十七行定义了一个有指针接收器的方法。

第二十七行我们用指针参数调用 perimeter 函数，在二十八行中我们调用指针接收器上的 perimeter 方法。都是可以的。

第三十三行的注释中，我们尝试使用值参数调用 perimeter 函数 r。不允许这样做，因为带有指针参数的函数将不接受值参数。如果未注释此行并运行程序，则编译将失败，**main.go:33: cannot use r (type rectangle) as type *rectangle in argument to perimeter**

在第三十五行中，perimeter 使用值接收器调用指针接收器方法 r。这是允许的，并且为了方便起见，代码行 r.perimeter() 将由 go 语言解释为 (&r).perimeter()。该程序将输出：
```
perimeter function output: 30  
perimeter method output: 30  
perimeter method output: 30  
```

## 非结构体接收器的方法

到目前为止，我们仅在结构体类型上定义了方法。也可以在非结构类型上定义方法，但是有一个陷阱。要在类型上定义方法，接收器类型的定义和方法的定义应存在于同一程序包中。到目前为止，我们定义的所有结构体和结构体上的方法都位于同一 main 包中，因此它们可以工作。

```go
package main

func (a int) add(b int) {  
}

func main() {

}
```
在 [playground](https://play.golang.org/p/ybXLf5o_lA) 上运行程序。

在上面程序的第三行中，我们试图在内置的 int 类型上添加一个名为 add 的方法，这是不允许的，因为 add 方法的定义和 int 类型的定义不在同一个包中。此程序将抛出编译错误，**cannot define new methods on non-local type int**

使其工作的方法是为内置类型int创建一个类型别名，然后用这个类型别名创建一个方法作为接收者。

使其工作的方法是为内置类型 int 创建一个类型别名，然后用这个类型别名创建一个方法作为接收者。

```go
package main

import "fmt"

type myInt int

func (a myInt) add(b myInt) myInt {  
    return a + b
}

func main() {  
    num1 := myInt(5)
    num2 := myInt(10)
    sum := num1.add(num2)
    fmt.Println("Sum is", sum)
}
```
在 [playground](https://play.golang.org/p/sTe7i1qAng) 上运行程序。

在上面程序的第五行中，我们已经创建了一个类型别名 myInt 类型为 int。在第七行代码，我们已经定义的方法 add 并把 myInt 作为接收器。

该程序将打印 `Sum is 15`

Go 中的方法就是这样。祝你有美好的一天。

## 下一教程 - [接口 I]()

