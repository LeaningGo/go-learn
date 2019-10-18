## Part 9: 循环

这里是 [Golang 教程系列](https://blog.csdn.net/baobaoxiannv/article/category/9365771)的第九部分。

循环语句用于重复执行代码块。

`for` 是 Go 中唯一可用的循环。Go 没有 `while` 或 `do while` 循环，而这些循环在其他语言中是存在的，比如 C。

## for 循环语法

```
for initialisation; condition; post {  
}
```

`initialisation` 只执行一次。在循环初始化之后，将检查 `condition`。如果 `condition` 的值为 `true`，`{ ....... }` 中的循环体将在 `post` 语句之后执行。`post` 语句将在循环的每次成功迭代之后执行。`post` 语句执行后，将重新检查 `condition`。如果为 `true`，则循环将继续执行，否则 `for` 循环将终止。

`initialisation`、`condition` 和 `post` 这三个组件在 Go 中都是可选的。让我们看一个例子来更好地理解 for 循环。

## 例子

让我们来写一个程序，它使用 for 循环打印数字 1 ~10。

```
package main

import (  
    "fmt"
)

func main() {  
    for i := 1; i <= 10; i++ {
        fmt.Printf(" %d",i)
    }
}
```

在 [playground](https://play.golang.org/p/mV6Zgcx2DY) 运行

在上面的程序中，i 被初始化为 1。条件语句将检查 i 是否 `<= 10`。如果条件为 `true`，则输出 i 的值，否则循环终止。`post` 语句在每次迭代结束时将 i 增量 1。当 i 大于 10 时，循环终止。

上面的程序将打印：`1 2 3 4 5 6 7 8 9 10`

`for` 循环中声明的变量只在循环范围内可用。因此，不能在 `for` 循环代码块之外访问。

## break

`break` 语句用于在 `for` 循环完成正常执行之前突然终止 `for` 循环，并将控制移动到 `for` 循环之后的代码行。

让我们编写一个程序，使用 `break` 打印从 1 到 5 的数字。

```
package main

import (  
    "fmt"
)

func main() {  
    for i := 1; i <= 10; i++ {
        if i > 5 {
            break //loop is terminated if i > 5
        }
        fmt.Printf("%d ", i)
    }
    fmt.Printf("\nline after for loop")
}
```

在 [playground](https://play.golang.org/p/sujKy92f--) 运行

在上面的程序中，每次迭代都检查 i 的值。如果 i 大于 5，则执行 `break` 并终止循环。然后在执行 `for` 循环之后执行 `print` 语句。以上程序将输出：

```
1 2 3 4 5  
line after for loop  
```

## continue

`continue` 语句用于跳过 `for` 循环的当前迭代。在 `continue` 语句之后，`for` 循环中出现的所有代码都不会在当前迭代中执行。循环将继续到下一个迭代。

让我们编写一个程序，使用 `continue` 打印从 1 到 10 的所有奇数

```
package main

import (  
    "fmt"
)

func main() {  
    for i := 1; i <= 10; i++ {
        if i%2 == 0 {
            continue
        }
        fmt.Printf("%d ", i)
    }
}
```

在 [playground](https://play.golang.org/p/DRLN2ZHwVS) 运行

在上面的程序中，`if i%2 == 0` 检查 i 除以 2 的提示符是否为 0。如果为零，则 number 为偶数，执行 continue 语句，然后控制移动到循环的下一个迭代。因此，continue 之后的 print 语句将不会被调用，循环将继续到下一个迭代。上面程序的输出是`1 3 5 7 9`

## 循环嵌套

一个 for 循环内部有另一个 for 循环，称为嵌套 for 循环。

让我们通过编写一个程序来理解嵌套 for 循环，该程序打印下面的序列。

```
*
**
***
****
*****
```

下面的程序使用嵌套 for 循环来打印序列。变量 n 在第一行。第八行是存储序列中的行数。在我们的例子中是 5。外部 for 循环将 i 从 0 迭代到 4，内部 for 循环将 j 从 0 迭代到 i 的当前值。运行此程序，您将看到输出的序列。

```
package main

import (  
    "fmt"
)

func main() {  
    n := 5
    for i := 0; i < n; i++ {
        for j := 0; j <= i; j++ {
            fmt.Print("*")
        }
        fmt.Println()
    }
}
```

在 [playground](https://play.golang.org/p/0rq8fWjVDLb) 运行

## Labels（标签）
标签可用于从内部 for 循环中断开外部循环。让我们用一个简单的例子来理解我的意思。

```
package main

import (  
    "fmt"
)

func main() {  
    for i := 0; i < 3; i++ {
        for j := 1; j < 4; j++ {
            fmt.Printf("i = %d , j = %d\n", i, j)
        }

    }
}
```

在 [playground](https://play.golang.org/p/BnCKho2x5hM) 运行

上面的程序是自解释的，它将打印：

```
i = 0 , j = 1  
i = 0 , j = 2  
i = 0 , j = 3  
i = 1 , j = 1  
i = 1 , j = 2  
i = 1 , j = 3  
i = 2 , j = 1  
i = 2 , j = 2  
i = 2 , j = 3  
```

没什么特别的 :)

如果我们想在 i 和 j 相等时停止打印。为此，我们需要从外部 for 循环中断开。当 i 和 j 相等时，在内部 for 循环中添加一个 break 只会从内部 for 循环中断开。

```
package main

import (  
    "fmt"
)

func main() {  
    for i := 0; i < 3; i++ {
        for j := 1; j < 4; j++ {
            fmt.Printf("i = %d , j = %d\n", i, j)
            if i == j {
                break
            }
        }

    }
}
```

在 [playground](https://play.golang.org/p/uMjbF8Ii41d) 运行

在上面的程序中，当 i 和 j 在第一行相等时，我在 for 循环内部添加了一个 break。这将只从内部 for 循环中断，而外部循环将继续。这个程序将打印：

```
i = 0 , j = 1  
i = 0 , j = 2  
i = 0 , j = 3  
i = 1 , j = 1  
i = 2 , j = 1  
i = 2 , j = 2  
```

这是不期而遇的输出。当 i 和 j 都等于 i 时，我们需要停止打印。e 当它们等于 1 时。

这就是标签拯救我们的地方。标签可以用来从外部循环中断开。让我们使用标签重写上面的程序。

```
package main

import (  
    "fmt"
)

func main() {  
outer:  
    for i := 0; i < 3; i++ {
        for j := 1; j < 4; j++ {
            fmt.Printf("i = %d , j = %d\n", i, j)
            if i == j {
                break outer
            }
        }

    }
}
```

在 [playground](https://play.golang.org/p/BI10Rmp_Z3y) 运行

在上面的程序中，我们在第一行增加了一个标签。第八行在外部的 for 循环。我们通过指定标签来中断外部 for 循环。当 i 和 j 相等时，程序将停止打印。这个程序将输出：

```
i = 0 , j = 1  
i = 0 , j = 2  
i = 0 , j = 3  
i = 1 , j = 1  
```

## 更多实例

下面的程序打印从0到10的所有偶数。

```
package main

import (  
    "fmt"
)

func main() {  
    i := 0
    for ;i <= 10; { // initialisation and post are omitted
        fmt.Printf("%d ", i)
        i += 2
    }
}
```

在 [playground](https://play.golang.org/p/PNXliGINku) 运行

我们已经知道 for 循环的所有三个步骤（初始化、条件和post）都是可选的。在上面的程序中，省略了初始化和 post。i 在 for 循环外初始化为 0。只要 i <= 10，循环就会执行。i在 for 循环中增加 2。上面的程序输出 0 2 4 6 8 10。

上面程序 for 循环中的分号也可以省略。这种格式可以作为 while 循环的一种替代。上面的程序可以重写为：

```
package main

import (  
    "fmt"
)

func main() {  
    i := 0
    for i <= 10 { //semicolons are ommitted and only condition is present
        fmt.Printf("%d ", i)
        i += 2
    }
}
```

在 [playground](https://play.golang.org/p/UYiz-Wtnoj) 运行

可以在 for 循环中声明和操作多个变量。让我们编写一个程序，它使用多个变量声明打印下面的序列。

```
10 * 1 = 10  
11 * 2 = 22  
12 * 3 = 36  
13 * 4 = 52  
14 * 5 = 70  
15 * 6 = 90  
16 * 7 = 112  
17 * 8 = 136  
18 * 9 = 162  
19 * 10 = 190  
```

```
package main

import (  
    "fmt"
)

func main() {  
    for no, i := 10, 1; i <= 10 && no <= 19; i, no = i+1, no+1 { //multiple initialisation and increment
        fmt.Printf("%d * %d = %d\n", no, i, no*i)
    }

}
```

在 [playground](https://play.golang.org/p/e7Pf0UDjj0) 运行

在上述计划中，`no` 及 `i` 分别被赋值为 `10` 及 `1`。它们在每次迭代结束时递增 1。`boolean` 符 `&&` 用于确保 i 小于或等于 `10`，`no` 也小于或等于 19。

## 无限循环

创建无限循环的语法是

```
for {  
}
```

下面的程序将继续连续打印 `Hello World` 而不终止。

```
package main

import "fmt"

func main() {  
    for {
        fmt.Println("Hello World")
    }
}
```

如果您试图在 go playground 运行上面的程序，您将得到错误 `process took too long`。请尝试在您的本地系统中运行它，无限地打印 `Hello World`。

还有一个构造范围可以在 for 循环中用于数组操作。当我们学习数组时，我们会讲到这一点。

这就是循环。希望你喜欢阅读。请留下您宝贵的意见和反馈。

### 下一个教程 - [switch 声明](https://blog.csdn.net/baobaoxiannv/article/details/102499860)
