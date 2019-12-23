这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第二十六部分。

## 是面向对象吗？
Go 并不是纯粹的面向对象变成的语言，Go 常见问题解答的摘录中解答了这个问题
```
Yes and no. Although Go has types and methods and allows an object-oriented style of programming, there is no 
type hierarchy. The concept of “interface” in Go provides a different approach that we believe is easy to use and in 
some ways more general. There are also ways to embed types in other types to provide something analogous—but 
not identical—to subclassing. Moreover, methods in Go are more general than in C++ or Java: they can be defined 
for any sort of data, even built-in types such as plain, “unboxed” integers. They are not restricted to structs (classes).  
```
译
```
是的也可以说不是。尽管 Go 有类型和方法，并且允许面向对象的编程风格，但是没有类型层次结构。Go 中的 `interface` 
概念提供了一种不同的方法，我们认为这种方法易于使用，而且在某些方面更通用。还有一些方法可以将类型嵌入到其他类型中，
提供类似于（但并不相同的）子类一样的东西。此外，Go 中的方法比 c++ 或 Java 中的方法更通用：它们可以为任何类型的数据定义，
甚至像 plain 这样的内置类型、`unboxed` 整型。它们不局限于结构体（类）。
```

在下面的讨论中我们将学习使用 Go 实现面向对象编程的概念，和 Java 等其他面向对象编程的语言相比实现实现会有不同。

## 使用结构体不使用类

Go 不提供类，但提供结构体。可以在结构体上添加[方法](go-methods.md)。这提供了捆绑数据的行为以及对数据进行操作的[方法](go-methods.md)，类似于类。

我们来写一个例子，更好的理解这些

我们创建一个自定义包，方便更好的理解结构体如何有效的替代类

在 Go 工作空间中创建一个文件夹并命名为 oop。在 oop 内创建子文件夹 employee。在 employee 文件夹内，创建一个名为 employee.go 的文件

目录的结构像下面一样

```
workspacepath -> oop -> employee -> employee.go
```

复制下面的内容粘贴在 employee.go 中
```go
package employee

import (  
    "fmt"
)

type Employee struct {  
    FirstName   string
    LastName    string
    TotalLeaves int
    LeavesTaken int
}

func (e Employee) LeavesRemaining() {  
    fmt.Printf("%s %s has %d leaves remaining", e.FirstName, e.LastName, (e.TotalLeaves - e.LeavesTaken))
}
```
在上面的程序中，第一行指定了这个文件属于 `employee` 包，`Employee` 结构体在第七行中声明，第十四行中名为 `LeavesRemaining` 的方法被添加到 `Employee` 结构中。这将计算并显示员工剩余的休假天数。现在，我们有了一个结构体和一个方法，该方法和捆绑在一起的结构体进行操作，类似于`class`。

在 oop 文件夹中创建一个 main.go 文件

现在结构体类似于下面：
```
workspacepath -> oop -> employee -> employee.go  
workspacepath -> oop -> main.go  
```

main.go 文件中定义以下内容
```go
package main

import "oop/employee"

func main() {  
    e := employee.Employee {
        FirstName: "Sam",
        LastName: "Adolf",
        TotalLeaves: 30,
        LeavesTaken: 20,
    }
    e.LeavesRemaining()
}
```
我们在第三行导入了 `employee` 包，在 main 方法中的第二十行调用了 employee 结构体的 LeavesRemaining 方法。

这个程序运行的话将会打印：
```
Sam Adolf has 10 leaves remaining  
```

## 使用 new() 函数而不是用构造函数
我们在上面写的例子看起来不错，但是其中有一个微妙的问题。如果用零值定义 employee 结构时会发生什么。将 main.go 更新为以下代码：
```go
package main

import "oop/employee"

func main() {  
    var e employee.Employee
    e.LeavesRemaining()
}
```
我们做的唯一的修改就是在第六行创建了 Employee 并且是空的。该程序将输出
```
has 0 leaves remaining
```

如你所见，使用零值（空值）创建的变量 Employee 不可用。它没有有效的 FirstName，LastName，也没有有效的详细的 leave  信息。

在其他 OOP 语言（如 java）中，可以使用构造函数解决此问题。可以使用带参构造函数来创建有效对象。

Go 不支持构造函数。如果类型的零值不可用，你的工作的就是取消这个引用，防止从其他包访问，并提供一个名为 `NewT(parameters)` 的函数，函数用所需的值初始化类型 T。在 Go 中命名约定是一个函数，该函数创建一个类型为 T 的值 `NewT(parameters)` 的值。这就像一个构造函数。如果包仅定义了一种类型，那么 Go 中的约定是将这个函数命名为 `New(parameters)` 而非 `NewT(parameters)`。

让我们修改程序，以便我们每次创建员工时，它可以使用。

第一步是取消导入 `Employee` 结构体并创建一个函数 `New()`，该函数将创建一个新的 `Employee`。将 `employee.go` 替换为以下内容
```go
package employee

import (  
    "fmt"
)

type employee struct {  
    firstName   string
    lastName    string
    totalLeaves int
    leavesTaken int
}

func New(firstName string, lastName string, totalLeave int, leavesTaken int) employee {  
    e := employee {firstName, lastName, totalLeave, leavesTaken}
    return e
}

func (e employee) LeavesRemaining() {  
    fmt.Printf("%s %s has %d leaves remaining", e.firstName, e.lastName, (e.totalLeaves - e.leavesTaken))
}
```
我们在这里做了一些重要的更改。我们将 Employee 结构的首字母改为小写，即已更改 `type Employee struct` 为 `type employee struct`。这样，我们已成功取消引用 employee 结构体并阻止了其他程序包的访问。除非有特殊需要，否则最好将私有结构体的所有字段都可以被访问（公开）。由于我们不需要 employee 包被引用任何字段，所以我们将所有字段都私有了。

我们在 `LeavesRemaining()` 方法中相应地更改了字段名称。

由于 employee 被私有，所以不可能从其他包中创建 employee 类型的值。所以我们在第十四行提供了一个公有的函数。它接收所需的参数作为输入并返回一个新创建的 employee 。

这个程序仍然需要进行一些修改才能正常工作，但是我们现在运行这个程序来了解到目前为止更改的效果。如果这个程序运行，它将失败与以下编译错误
```
go/src/constructor/main.go:6: undefined: employee.Employee  
```
这是因为我们有私有的 Employee，因此编译器会抛出错误，说明 main.go 中没有定义这种类型。这正是我们想要的。现在没有其他包能够创建零值 Employee。我们成功地阻止了创建。现在创建 Employee 的惟一方法是使用 New 函数。

在 main.go 替换为下面的代码
```go
package main  

import "oop/employee"

func main() {  
    e := employee.New("Sam", "Adolf", 30, 20)
    e.LeavesRemaining()
}
```

对 main.go 中唯一的修改就是在第六行，通过将所需的参数传递给 New 函数，这时我们创建了新的雇员。

以下是两个文件做了修改后的内容

employee.go
```go
package employee

import (  
    "fmt"
)

type employee struct {  
    firstName   string
    lastName    string
    totalLeaves int
    leavesTaken int
}

func New(firstName string, lastName string, totalLeave int, leavesTaken int) employee {  
    e := employee {firstName, lastName, totalLeave, leavesTaken}
    return e
}

func (e employee) LeavesRemaining() {  
    fmt.Printf("%s %s has %d leaves remaining", e.firstName, e.lastName, (e.totalLeaves - e.leavesTaken))
}
```
main.go
```go
package main  

import "oop/employee"

func main() {  
    e := employee.New("Sam", "Adolf", 30, 20)
    e.LeavesRemaining()
}
```
运行这个程序将输出：
```go
Sam Adolf has 10 leaves remaining  
```

因此，你可以理解为，虽然 Go 不支持类，但可以有效地使用 struct 来代替类，而 signature New(parameters) 的方法可以代替构造函数。

这就是在 Go 中的类和构造函数。祝你有美好的一天。

## 下一个教程 - [组合代替继承](inheritance.md)
