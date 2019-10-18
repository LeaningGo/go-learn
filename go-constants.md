## Part 5: 常量

这里是 [Golang 学习教程系列](https://github.com/LeaningGo/go-learn)的第五部分。

请阅读 [Golang 教程第四部分 - 类型](https://github.com/LeaningGo/go-learn/blob/master/go-types.md)以了解 golang 基本类型。

## 定义

**常量**，一词在 go 中用来表示固定值，如 5、-89、“I love go”、67.89 等等。

参考以下代码

```
var a int = 50  
var b string = "I love Go"  
```
**在上面的代码中，a 和 b 分别赋值给常数 50 和 I love Go**。关键字 const 用于表示诸如 50 和 I love Go 之类的常量。尽管我们在上面的代码中没有显式地使用关键字 const，但在内部，它们是 Go 中的常量。

一个常量表明不能再重新赋值给任何其他值，因此下面的程序将无法工作，它将失败，编译错误不能赋值给 a。

```
package main

func main() {  
    const a = 55 //allowed
    a = 89 //reassignment not allowed
}
```

在 [playground](https://play.golang.org/p/b2J8_UQobb) 运行

常量的值应该在编译时就知道。因此，它不能被分配给函数调用返回的值，因为函数调用发生在运行时。

```
package main

import (  
    "fmt"
    "math"
)

func main() {  
    fmt.Println("Hello, playground")
    var a = math.Sqrt(4)//allowed
    const b = math.Sqrt(4)//not allowed
}
```

在 [playground](https://play.golang.org/p/dCON1LzCTw) 运行

在上面的程序中，a 是一个变量，因此它可以分配给函数的结果 math.Sqrt(4)（我们将在单独的教程中更详细地讨论函数）。
b 是常量，在编译时需要知道 b 的值。该函数 `math.Sqrt(4)` 仅在运行时进行计算，因此 `const b = math.Sqrt(4)` 抛出错误 **error main.go:11: const initializer math.Sqrt(4) is not a constant**。

## 字符串常量

双引号之间的任何值都是 Go 中的字符串常量。例如，像 “Hello World” 或 “Sam” 这样的字符串都是 Go 中的常量。

字符串常量属于什么类型？答案是它们是**无类型的**。

**像 “Hello World” 这样的字符串常量没有任何类型**。

```
const hello = "Hello World"  
```

在上面的例子中，我们将 “Hello World” 分配给一个命名的常量 `Hello`。常量 hello 有类型吗? 答案是否定的。常数仍然没有类型。

Go 是一种强类型语言。所有变量都需要显式类型。那么，下面的程序是如何将变量名分配给无类型常量 Sam 的呢?

```
package main

import (  
    "fmt"
)

func main() {  
    var name = "Sam"
    fmt.Printf("type %T value %v", name, name)

}
```

在 [playground](https://play.golang.org/p/xhYV4we_Jz) 运行

**答案是无类型常量有一个与之关联的默认类型，当且仅当一行代码需要它时，它们才提供它。在 var name = "Sam" 语句中，name 需要一个类型，它从字符串常量 "Sam" 的默认类型获得该类型，该字符串是一个字符串**。

有没有办法创建一个类型常量？答案是肯定的。以下代码创建一个类型化常量。

```
const typedhello string = "Hello World"  
```

**上面代码中的 typedhello 是 string 类型的常量**。

Go 是一种强类型语言。不允许在分配期间混合类型。让我们通过一个程序来看看这意味着什么。

```
package main

func main() {  
        var defaultName = "Sam" //allowed
        type myString string
        var customName myString = "Sam" //allowed
        customName = defaultName //not allowed

}
```

在 [playground](https://play.golang.org/p/1Q-vudNn_9) 运行


在上面的代码中，我们首先创建一个变量 `defaultName` 并将其分配给常量 Sam。常量 Sam 的默认类型是 string，因此在赋值之后 `defaultName` 的类型是 string。

在下一行中，我们创建一个新的类型 `myString`，它是 `string` 的别名。

然后我们创建一个 `myString` 类型的 `customName` 变量，并将其分配给常量 Sam。由于常量 Sam 是无类型的，所以可以将它分配给任何字符串变量。因此，这个赋值是允许的，`customName` 获取类型 `myString`。

现在我们有一个类型为 string 的变量 `defaultName` 和另一个类型为 `myString` 的变量 `customName`。尽管我们知道 `myString` 是 `string` 的别名，但 Go 的强类型策略不允许将一种类型的变量分配给另一种类型。因此，不允许赋值 `customName = defaultName`，编译器将抛出一个错误：**main.go:7:20: cannot use defaultName (type string) as type myString in assignment**

## Boolean 常量

布尔常量与字符串常量没有区别。它们是两个类型化常量 `true` 和 `false`。字符串常量的相同规则适用于 booleans，因此我们在此不再重复。以下是解释 booleans 常量的简单程序。

```
package main

func main() {  
    const trueConst = true
    type myBool bool
    var defaultBool = trueConst //allowed
    var customBool myBool = trueConst //allowed
    defaultBool = customBool //not allowed
}
```

在 [playground](https://play.golang.org/p/h9yzC6RxOR) 运行

上面的程序是不言而明的。

## Numeric  常量

Numeric 常量包括整数、浮点数和复数常量。数值常量有一些微妙之处。

让我们看一些例子来把事情弄清楚。

```
package main

import (  
    "fmt"
)

func main() {  
    const a = 5
    var intVar int = a
    var int32Var int32 = a
    var float64Var float64 = a
    var complex64Var complex64 = a
    fmt.Println("intVar",intVar, "\nint32Var", int32Var, "\nfloat64Var", float64Var, "\ncomplex64Var",complex64Var)
}
```

在 [playground](https://play.golang.org/p/a8sxVNdU8M) 运行

在上面的程序中，`const a` 是无类型的，值为 5。您可能想知道 a 的默认类型是什么，如果它确实有，那么我们如何将它分配给不同类型的变量。答案在于 a 的语法。下面的程序将使事情变得更清楚。

```
package main

import (  
    "fmt"
)

func main() {  
    var i = 5
    var f = 5.6
    var c = 5 + 6i
    fmt.Printf("i's type %T, f's type %T, c's type %T", i, f, c)

}
```

在 [playground](https://play.golang.org/p/kJq69Vpqit) 运行

在上面的程序中，每个变量的类型由数值常量的语法决定。5 按语法是整数，5.6 是浮点数，5 + 6i 按语法是复数。当上面的程序运行时，它输出 i 的 int 类型、f 的 float64 类型、c 的 complex128 类型。

现在，我希望大家能清楚地了解下面的程序是如何工作的。

```
package main

import (  
    "fmt"
)

func main() {  
    const a = 5
    var intVar int = a
    var int32Var int32 = a
    var float64Var float64 = a
    var complex64Var complex64 = a
    fmt.Println("intVar",intVar, "\nint32Var", int32Var, "\nfloat64Var", float64Var, "\ncomplex64Var",complex64Var)
}
```

在 [playground](https://play.golang.org/p/_zu0iK-Hyj) 运行

在这个程序中，a 的值是 5，a 的语法是通用的（它可以表示浮点数、整数，甚至没有虚数的复数），因此可以分配给任何兼容的类型。这类常量的默认类型可以看作是根据上下文动态生成的。`var intVar int = a` 要求 a 是整型的，所以它变成了一个整型常量。`var complex64 = a` 要求 a 是一个复数，因此它变成了一个复数常量。非常整洁 :)。

## 数值表达式

数值常量可以自由地在表达式中混合和匹配，只有当它们被分配给变量或在需要类型的代码中的任何地方使用时，才需要类型。

```
package main

import (  
    "fmt"
)

func main() {  
    var a = 5.9/8
    fmt.Printf("a's type %T value %v",a, a)
```

在 [playground](https://play.golang.org/p/-8i-iX-jIG) 运行

在上面的程序中，5.9 按语法是浮点数，8 按语法是整数。仍然允许 `5.9/8`，因为它们都是数值常量。除法的结果是 `0.7375` 是一个浮点数，因此变量 a 是 `float` 类型。程序的输出是 a 类型的 `float64` 值 `0.7375`。

本章是常量。请分享您宝贵的反馈和意见。

### 下一教程 - [函数](https://github.com/LeaningGo/go-learn/blob/master/go-functions.md)
