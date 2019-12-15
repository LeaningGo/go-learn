## Part 7: 包管理

这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第七部分。

## 什么是软件包？我们为什么要使用它们？

到目前为止，我们已经看到 go 程序只有一个文件，它有一个主函数和几个其他函数。**在现实世界中，这种将所有源代码编写在一个文件中的方法是行不通的。以这种方式编写的代码不可能重用和维护**。这就是包节省时间的地方。

**软件包用于组织 go 源代码，提高可重用性和可读性**。包提供了对代码的分隔，因此易于维护 go 应用程序。

例如，假设我们正在创建一个 go 图片处理应用程序，它提供了诸如图像裁剪、锐化、模糊和颜色增强等功能。组织此应用程序的一种方法是在其自己的包中对与某个特性相关的所有代码进行分组。例如，裁剪可以是一个单独的包，锐化可以是另一个包。这样做的好处是，颜色增强功能可能需要一些锐化功能。颜色增强代码可以简单地导入（我们稍后将讨论导入）锐化包并开始使用它的功能。这样代码就很容易重用。

我们将逐步创建一个计算矩形面积和对角线的应用程序。

我们将通过这个应用程序更好地了解软件包。

## main 函数 和 main 包

每个可执行的 go 应用程序必须包含一个 main 函数。这个函数是程序执行的入口点。主要功能应保留在 main 包中。

指定特定源文件属于某个包的代码是 `package packagename`。这应该是每个 go 源文件的第一行。

首先，为我们的应用程序创建 `main` 函数和 `main` 包。在 go 工作空间的 src 文件夹内创建一个文件夹，并将其命名 `geometry`。在文件夹 `geometry` 内创建一个文件 `geometry.go`。

在 `geometry.go` 中编写以下代码

```
//geometry.go
package main 

import "fmt"

func main() {  
    fmt.Println("Geometrical shape properties")
}
```

第一行 `package main` 指定此文件属于 `main` 软件包，`import "packagename"` 语句用于导入现有软件包。在这种情况下，我们导入 `fmt` 包含 `Println` 方法的包。然后有一个 main 函数 `Geometrical shape properties`。

通过输入 `go install geometry` 编译上述程序。此命令搜索 `geometry` 文件夹中包含 main 函数的文件。在这种情况下，它会找到 `geometry.go`。然后编译它，并在工作空间 `bin` 文件夹中生成一个名为 `geometry` 的二进制文件（在 windows 中是 `geometry.exe`）。现在工作区结构将是如下：

```
src  
  geometry
    gemometry.go
bin  
  geometry
```

让我们通过输入 `workspacepath/bin/geometry` 来运行程序。用 go 工作空间路径替换 `workspacepath`。此命令执行 `bin` 文件夹中的 `geometry` 二进制文件。您应该得到几何形状属性作为输出。

## 自定义包

我们将以这样一种方式来构造代码，使得与 `rectangle` 相关的所有函数都在 `rectangle` 软件包中。

让我们创建一个自定义包 `rectangle`，该包具有确定矩形的面积和对角线的函数。

**属于包的源文件应该放在单独的文件夹中。使用包的相同名称命名此文件夹是 Go 中的惯例。**

让我们在 `geometry ` 文件夹中创建一个名为 `rectangle` 的文件夹。`rectangle ` 文件夹中的所有文件都应该以 `package rectangle` 开始，因为它们都属于 `rectangle ` 包。

创建一个文件 `rectprops.go`。进入我们刚刚创建的 `rectangle ` 文件并添加以下代码。

```
//rectprops.go
package rectangle

import "math"

func Area(len, wid float64) float64 {  
    area := len * wid
    return area
}

func Diagonal(len, wid float64) float64 {  
    diagonal := math.Sqrt((len * len) + (wid * wid))
    return diagonal
}
```

在上面的代码中，我们创建了两个计算面积和对角线的函数。长方形的面积是长和宽的乘积。长方形的对角线是长和宽的平方和的平方根。`math` 包中的 `Sqrt` 函数用于计算平方根。

注意，函数名 `Area` 和 `Diagonal` 以大写字母开头。这是必要的，我们将很快解释为什么需要这样做。

## 导入自定义包

要使用自定义包，我们必须首先将其导入。`import path` 是导入自定义程序包的语法。我们必须相对于 `src` 工作空间内的文件夹指定自定义包的路径。我们当前的文件夹结构是：

```
src  
 geometry
   geometry.go
   rectangle
     rectprops.go
```

这行代码 `import "geometry/rectangle"` 导入 `rectangle` 包.

将以下代码添加到 `geometry.go`

```
//geometry.go
package main 

import (  
    "fmt"
    "geometry/rectangle" //importing custom package
)

func main() {  
    var rectLen, rectWidth float64 = 6, 7
    fmt.Println("Geometrical shape properties")
        /*Area function of rectangle package used
        */
    fmt.Printf("area of rectangle %.2f\n", rectangle.Area(rectLen, rectWidth))
        /*Diagonal function of rectangle package used
        */
    fmt.Printf("diagonal of the rectangle %.2f ",rectangle.Diagonal(rectLen, rectWidth))
}
```
上面的代码导入 `rectangle` 包，并使用它的 `Area` 和 `Diagonal` 函数查找矩形的面积和对角线。 `%.2f Printf` 中的格式说明符是将浮点截断为两位小数。该应用程序的输出是：

```
Geometrical shape properties  
area of rectangle 42.00  
diagonal of the rectangle 9.2
```

## Exported Names（导出名称）

我们将函数大写 `Area` 和 `Diagonal` 在 `rectangle` 包中。这在 Go 中具有特殊含义。**任何以大写字母开头的变量或函数都会在 go 中被导出供其他函数使用**。只能从其他程序包访问导出的函数和变量。在当前例子这种情况下，我们需要从 `main` 包访问 `Area` 和 `Diagonal` 功能。因此它们大写。

如果在函数名 `rectprops.go` 中从 `Area(len, wid float64)` 更改为 `area(len, wid float64)`，在 `geometry.go` 中 `rectangle.Area(rectLen, rectWidth)` 更改为 `rectangle.area(rectLen, rectWidth)`，这时程序运行时，编译器将抛出错误：`geometry.go:11: cannot refer to unexported name rectangle.area`。因此，如果您想访问包外的功能，应该将其大写。

## init 函数

每个包都可以包含一个 `init` 函数。`init` 函数不应该有任何返回类型，也不应该有任何参数。在我们的源代码中不能显式地调用 `init` 函数。`init` 函数如下所示：

```
func init() {  
}
```
`init` 函数用于执行初始化任务，也可用于在执行开始之前验证程序的正确性。

程序包的初始化顺序如下：

* 包级别变量首先被初始化
* 接下来调用 `init` 函数。包可以具有多个 `init` 函数（在单个文件中或分布在多个文件中），并且它们按向编译器显示的顺序被调用。

如果某个软件包导入了其他软件包，则首先初始化导入的软件包。

一个包即使从多个包中导入，也只会被初始化一次。

让我们对应用程序进行一些修改以了解 `init` 函数。

首先，让我们向 `rectprops.go` 文件添加一个 `init` 函数。

```
//rectprops.go
package rectangle

import "math"  
import "fmt"

/*
 * init function added
 */
func init() {  
    fmt.Println("rectangle package initialized")
}
func Area(len, wid float64) float64 {  
    area := len * wid
    return area
}

func Diagonal(len, wid float64) float64 {  
    diagonal := math.Sqrt((len * len) + (wid * wid))
    return diagonal
}
```

我们添加了一个简单的 `init` 函数，仅打印 `rectangle package initialised`

现在让我们修改 `mian` 包。我们知道矩形的长度和宽度应该大于零。我们将在 `geometry.go` 中使用 `init` 函数和包级别变量来定义这个检查文件。

`geometry.go` 如下所示修改文件：

```
//geometry.go
package main 

import (  
    "fmt"
    "geometry/rectangle" //importing custom package
    "log"
)
/*
 * 1. package variables
*/
var rectLen, rectWidth float64 = 6, 7 

/*
*2. init function to check if length and width are greater than zero
*/
func init() {  
    println("main package initialized")
    if rectLen < 0 {
        log.Fatal("length is less than zero")
    }
    if rectWidth < 0 {
        log.Fatal("width is less than zero")
    }
}

func main() {  
    fmt.Println("Geometrical shape properties")
    fmt.Printf("area of rectangle %.2f\n", rectangle.Area(rectLen, rectWidth))
    fmt.Printf("diagonal of the rectangle %.2f ",rectangle.Diagonal(rectLen, rectWidth))
}
```

下面是对 `geometry.go` 所做的更改

* `rectLen` 和 `rectWidth` 变量从 `main` 函数级别移至程序包级别。
* `init` 函数已经添加。如果 `rectLen` 或 `rectWidth` 小于零，则使用 `log.Fatal` 函数，`init` 函数将打印日志并终止程序执行。

`main` 包的初始化顺序为：

* 导入的程序包首先被初始化。因此，`rectangle ` 包首先被初始化。
* 接下来初始化包级别变量 **rectLen** 和 **rectWidth**
* **init** 函数被调用
* **main** 函数最后被调用

如果运行该程序，将得到以下输出：

```
rectangle package initialized  
main package initialized  
Geometrical shape properties  
area of rectangle 42.00  
diagonal of the rectangle 9.22  
```

正如预期的那样，首先调用 `rectangle` 包的 `init` 函数，然后初始化包级别变量 `rectLen` 和 `rectWidth`。接下来调用主包的 `init` 函数。它检查 `rectLen` 和 `rectWidth` 是否小于零，如果条件为 `true` 则终止。我们将在单独的教程中详细了解 `if` 语句。现在，您可以假设，如果 `rectLen < 0` 将检查 `rectLen` 是否小于 0，如果是，程序将被终止。我们已经为 `rectWidth` 写了一个类似的条件。在这种情况下，两个条件都为 `false` ，程序执行将继续。最后调用 `main` 函数。

让我们稍微修改一下该程序，了解 `init` 函数的用法。

在 `geometry.go` 中修改行 `var rectLen, rectWidth float64 = 6, 7` 为 `rectLen, rectWidth float64 = -6, 7` 我们初始化为负值。

现在，如果您运行应用程序，您将看到

```
rectangle package initialized  
main package initialized  
2017/04/04 00:28:20 length is less than zero 
```

与往常一样，在主包中初始化 `rectangle ` 包，然后初始化包级别变量 `rectLen` 和 `rectWidth`。`rectLen` 是负的。因此，当 `init` 函数接下来运行时，程序在打印 `length is less than zero`。

代码可以从 [github](https://github.com/golangbot/geometry)下载。

## 空白标识符的使用

在 Go 中导入一个未使用的包是非法的。如果您这样做，编译器将会抛出错误。这样做的原因是为了避免未使用包的泛滥，这将显著增加编译时间。替换 `geometry.go` 中的代码。遵循以下原则。

```
//geometry.go
package main 

import (   

     "geometry/rectangle" //importing custom package

)
func main() {

}
```

上面的程序会抛出错误：`geometry.go:6: imported and not used: "geometry/rectangle"`

但是，在应用程序处于活动开发阶段时导入包，并在稍后（如果不是现在）的代码中使用包，这是非常常见的。在这些情况下，`_` 标识符可以保存接收。

上述程序中的错误可以通过以下代码清楚，

```
package main

import (  
    "geometry/rectangle" 
)

var _ = rectangle.Area //error silencer

func main() {

}
```

行 `var _ = rectangle.Area //error silencer`。我们应该跟踪这些类型 `error silencer`，并在应用程序开发结束时删除它们，包括导入的包（如果包没有使用）。因此，建议在 `import` 语句之后在包级别编写 `error silencers`。

有时候我们需要导入一个包来确保初始化发生，即使我们不需要使用包中的任何函数或变量。例如，我们可能需要确保调用 `rectangle` 包的 `init` 函数，即使我们在代码中的任何地方都没有使用该包。在这种情况下也可以使用 `_ ` 标识符，如下所示。

```
package main 

import (   

     _ "geometry/rectangle" 

)
func main() {

}
```

运行上述程序将输出 `rectangle package initialized`。我们已经成功地初始化了包，尽管它在代码中的任何地方都没有使用。

包管理是这样的。希望你喜欢阅读。请留下您宝贵的意见和反馈 :)。

## 下一个教程 - [if else 语句](https://github.com/LeaningGo/go-learn/blob/master/go-if-else.md)
