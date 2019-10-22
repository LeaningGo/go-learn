这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第十八部分。

## 什么是接口
在面向对象的世界中，**接口的一般定义是“接口定义对象的行为”**。它只指定对象应该做什么。实现这种行为的方法(实现细节)取决于对象。

在 Go 中，接口是一组方法签名。当类型为接口中的所有方法提供定义时，就说它实现了接口。它与 OOP 世界非常相似。接口指定类型应该具有什么方法，类型决定如何实现这些方法。

例如，*WashingMachine 可以是方法签名 Cleaning() 和 Drying() 的接口。任何提供 Cleaning() 和 Drying() 定义的类型都被认为实现了 WashingMachine 接口*。

## 声明和实现接口
让我们直接进入一个程序，该程序创建并实现一个接口。
```go
package main

import (  
    "fmt"
)

//interface definition
type VowelsFinder interface {  
    FindVowels() []rune
}

type MyString string

//MyString implements VowelsFinder
func (ms MyString) FindVowels() []rune {  
    var vowels []rune
    for _, rune := range ms {
        if rune == 'a' || rune == 'e' || rune == 'i' || rune == 'o' || rune == 'u' {
            vowels = append(vowels, rune)
        }
    }
    return vowels
}

func main() {  
    name := MyString("Sam Anderson")
    var v VowelsFinder
    v = name // possible since MyString implements VowelsFinder
    fmt.Printf("Vowels are %c", v.FindVowels())

}
```
在 [playground](https://play.golang.org/p/F-T3S_wNNB) 运行程序。

在上面程序第八行中，创建了一个名为 VowelsFinder 的接口类型，它有一个方法 FindVowels() []rune。

在下一行中创建一个类型 MyString 它只是 string 的包装类。

在第十五行中，我们将方法 FindVowels()[]rune 添加到接收方类型 MyString 中。现在 MyString 被认为实现了 VowelsFinder 接口。这与 Java 等其他语言非常不同，在 Java 中，类必须使用 implements 关键字显式地声明它实现了接口。如果类型包含接口中声明的所有方法，则 go 和 go 接口将隐式实现。

在第二十八行，我们将 MyString 类型的 name 赋给 v 类型的 VowelsFinder。这是可能的，因为 MyString 实现了 VowelsFinder。下一行调用 MyString 类型上的 FindVowels 方法，并打印字符串中所有的元音 Sam Anderson。这个程序输出的 `Vowels are [a e o]`

恭喜！你已经创建并实现了第一个接口。

## 接口的实际使用
上面的例子教会了我们如何创建和实现接口，但是它并没有真正展示接口的实际使用。如果我们在上面的程序中使用 name.FindVowels()，而不是 v.FindVowels()，那么它也可以工作，并且不需要使用创建的接口。

现在让我们来看一个实际的接口用例。

我们将编写一个简单的程序，根据员工的个人工资计算公司的总费用。为简洁起见，我们假设所有费用都以美元计算。

```go
package main

import (  
    "fmt"
)

type SalaryCalculator interface {  
    CalculateSalary() int
}

type Permanent struct {  
    empId    int
    basicpay int
    pf       int
}

type Contract struct {  
    empId  int
    basicpay int
}

//salary of permanent employee is sum of basic pay and pf
func (p Permanent) CalculateSalary() int {  
    return p.basicpay + p.pf
}

//salary of contract employee is the basic pay alone
func (c Contract) CalculateSalary() int {  
    return c.basicpay
}

/*
total expense is calculated by iterating though the SalaryCalculator slice and summing  
the salaries of the individual employees  
*/
func totalExpense(s []SalaryCalculator) {  
    expense := 0
    for _, v := range s {
        expense = expense + v.CalculateSalary()
    }
    fmt.Printf("Total Expense Per Month $%d", expense)
}

func main() {  
    pemp1 := Permanent{1, 5000, 20}
    pemp2 := Permanent{2, 6000, 30}
    cemp1 := Contract{3, 3000}
    employees := []SalaryCalculator{pemp1, pemp2, cemp1}
    totalExpense(employees)

}
```
在 [playground](https://play.golang.org/p/5t6GgQ2TSU) 运行程序。

上述程序的第七行使用单个方法 CalculateSalary() int 声明 SalaryCalculator 接口类型。

我们公司有两种员工，一种是 `Permanent` 员工，另一种是第十一行和第十七行结构体中定义的 `Contract` 员工。`Permanent` 员工的工资是基本工资和基本工资的总和，而 `Contract` 员工的工资是基本工资。这分别用第二十三行和第二十八行中相应的计算方法表示。通过声明这个方法，Permanent 和 Contract 现在都实现了 SalaryCalculator 接口。

第三十六行中声明的 totalExpense 函数表达了使用接口的美妙之处。该方法以 SalaryCalculator 接口作为参数。在第四十九行中，我们将包含 Permanent 类型和 Contract 类型的切片传递给 totalExpense 函数。totalExpense 函数通过调用对应类型的 CalculateSalary 方法来计算费用。这是在三十九行完成的。

这样做的最大好处是 totalExpense 可以扩展到任何新员工类型，而不需要修改任何代码。假设公司增加了一种新的自由职业者，工资结构不同。这个自由职业者可以将 slice 参数传递给 totalExpense，而不需要对 totalExpense 函数进行任何代码更改。这个方法将做它应该做的，因为自由职业者也将实现工资计算器接口 :)

程序的输出是 `Total Expense Per Month $14050`

## 接口内部表示
可以认为接口在内部由一个 `(type, value)` 表示。`type` 是接口的底层具体类型，`value` 保存具体类型的值。

让我们写一个程序来更好地理解。

```go
package main

import (  
    "fmt"
)

type Tester interface {  
    Test()
}

type MyFloat float64

func (m MyFloat) Test() {  
    fmt.Println(m)
}

func describe(t Tester) {  
    fmt.Printf("Interface type %T value %v\n", t, t)
}

func main() {  
    var t Tester
    f := MyFloat(89.7)
    t = f
    describe(t)
    t.Test()
}
```
在 [playground](https://play.golang.org/p/ZpQhhhs2920) 运行程序。

Tester 接口有一个方法 Test()，而 MyFloat 类型实现了那个接口。第二十四行我们将 MyFloat 类型的变量 f 赋给 t, t 是 Tester 类型。现在 t 的具体类型是 MyFloat, t 的值是 89.7。在第十七行，打印接口的值和具体类型。这个程序输出

```
Interface type main.MyFloat value 89.7  
89.7  
```

## 空接口

一个没有方法的接口称为空接口。它表示为 interface{}。由于空接口没有任何方法，所以所有类型都实现空接口。

```go
package main

import (  
    "fmt"
)

func describe(i interface{}) {  
    fmt.Printf("Type = %T, value = %v\n", i, i)
}

func main() {  
    s := "Hello World"
    describe(s)
    i := 55
    describe(i)
    strt := struct {
        name string
    }{
        name: "Naveen R",
    }
    describe(strt)
}
```
在 [playground](https://play.golang.org/p/Fm5KescoJb) 运行程序。

在上面的程序第七行中，describe(i interface{}) 函数接受一个空接口作为参数，因此它可以传递任何类型。

我们将字符串、int 和 结构体分别传递给第 13、15 和 21 行中的 describe 函数。这个程序打印
```
Type = string, value = Hello World  
Type = int, value = 55  
Type = struct { name string }, value = {Naveen R}  
```

## 类型断言
使用类型断言提取接口的基础值。

**i.(T)** 是用来获取具体类型为T的接口i的底层值的语法。

一个程序是值得😀一千字。让我们为类型断言编写一个。

```go
package main

import (  
    "fmt"
)

func assert(i interface{}) {  
    s := i.(int) //get the underlying int value from i
    fmt.Println(s)
}
func main() {  
    var s interface{} = 56
    assert(s)
}
```
在 [playground](https://play.golang.org/p/YstKXEeSBL) 运行程序。


第十二行中的 s 的具体类型是 int。我们使用第八行中的 i.(int) 语法来获取 i 的底层 int 值。这个程序打印 56。

如果上面程序中的具体类型不是 int 类型，会发生什么?让我们来看看。

```go
package main

import (  
    "fmt"
)

func assert(i interface{}) {  
    s := i.(int) 
    fmt.Println(s)
}
func main() {  
    var s interface{} = "Steven Paul"
    assert(s)
}
```
在 [playground](https://play.golang.org/p/88KflSceHK) 运行程序。

在上面的程序中，我们将具体类型 string 的 s 传递给 assert 函数，该函数试图从中提取一个 int 值。程序将对消息感到恐慌: `panic: interface conversion: interface {} is string, not int`

为了解决上述问题，我们可以使用以下语法

```
v, ok := i.(T)  
```
如果 i 的具体类型是 T，那么 v 的值就是 i, ok 为 true。

如果 i 的具体类型不是 T，那么 ok 将是 false，v 将是类型 T 的零值，程序不会恐慌。

```go
package main

import (  
    "fmt"
)

func assert(i interface{}) {  
    v, ok := i.(int)
    fmt.Println(v, ok)
}
func main() {  
    var s interface{} = 56
    assert(s)
    var i interface{} = "Steven Paul"
    assert(i)
}
```
在 [playground](https://play.golang.org/p/0sB-KlVw8A) 运行程序。

当 Steven Paul 被传递给 assert 函数时，ok 将为 false，因为 i 的具体类型不是 int，而 v 的值为 0，也就是 int 的 0 值。

```go
56 true  
0 false  
```

## 类型 Switch
类型 switch 用于将接口的具体类型与各种 case 语句中指定的多种类型进行比较。它类似于 switch case。唯一的区别是，用例指定的是类型，而不是普通 switch 中的值。

类型 switch 的语法类似于类型断言。在类型断言的语法 i.(T) 中，类型 T 应该由类型 switch 的关键字类型替换。让我们在下面的程序中看看这是如何工作的。
```go
package main

import (  
    "fmt"
)

func findType(i interface{}) {  
    switch i.(type) {
    case string:
        fmt.Printf("I am a string and my value is %s\n", i.(string))
    case int:
        fmt.Printf("I am an int and my value is %d\n", i.(int))
    default:
        fmt.Printf("Unknown type\n")
    }
}
func main() {  
    findType("Naveen")
    findType(77)
    findType(89.98)
}
```
在 [playground](https://play.golang.org/p/XYPDwOvoCh) 运行程序。

在上面程序的第八行中 switch i.(type) 指定一个类型 switch。每个 case 语句都将 i 的具体类型与特定类型进行比较。如果任何大小写匹配，则打印相应的语句。这个程序输出

```
I am a string and my value is Naveen  
I am an int and my value is 77  
Unknown type  
```

第二十行中的 89.98 是类型为 float64 的，与任何情况都不匹配，因此会在最后一行打印未知类型。

**也可以将类型与接口进行比较。如果我们有一个类型，并且该类型实现了一个接口，那么可以将该类型与它实现的接口进行比较**。

让我们写一个程序更清楚。

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

func (p Person) Describe() {  
    fmt.Printf("%s is %d years old", p.name, p.age)
}

func findType(i interface{}) {  
    switch v := i.(type) {
    case Describer:
        v.Describe()
    default:
        fmt.Printf("unknown type\n")
    }
}

func main() {  
    findType("Naveen")
    p := Person{
        name: "Naveen R",
        age:  25,
    }
    findType(p)
}
```
在 [playground](https://play.golang.org/p/o6aHzIz4wC) 运行程序。

在上面的程序中，Person 结构实现了 Describer 接口。在十九行中的 case 语句中，v 与 Describer 接口类型相比较。p 实现了 descripber，因此满足了这种情况，当控件在第三十二行中碰到 findType(p) 时，调用 Describe() 方法。

上面的输出是：

```
unknown type  
Naveen R is 25 years old  
```

这就到了接口第一部分的结尾，我们将在第二部分继续讨论接口。祝你有美好的一天。

## 下一教程 - [接口 II](https://github.com/LeaningGo/go-learn/blob/master/interface%20II.md)
