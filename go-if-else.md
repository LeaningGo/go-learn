## part 8: if else 使用

这里是 [Golang 教程系列](https://github.com/LeaningGo/go-learn)的第八部分。

**if** 是一个条件语句。**if** 语句的语法是：

```
if condition {  
}
```

如果 `condition` 为 `true` 则执行 `{ ....... }` 之间的代码块

与 C 之类的其他语言不同，`{  }` 即使之间只有一条语句，也必须执行 `{ }`。

`if` 语句还具有可选的 `else if` 和 `else`

```
if condition {  
} else if condition {
} else {
}
```

可以有任意数量的 `else if`，从上到下判断的条件为 `true`。当有任何 `else if` 或者 `if` 条件为 `true` 时，则执行对应的代码块内容。如果没有一个条件为 `true`，则执行 `else` 代码块。

让我们编写一个简单的程序来查找数字是奇数还是偶数。

```
package main

import (  
    "fmt"
)

func main() {  
    num := 10
    if num % 2 == 0 { //checks if number is even
        fmt.Println("the number is even") 
    }  else {
        fmt.Println("the number is odd")
    }
}
```

在 [playground](https://play.golang.org/p/vWfN8UqZUr) 上运行

`if num % 2 == 0` 语句检查数字除以 2 的提示符是否为零。如果是，则打印 `the number is even`，否则打印 `the number is odd`。在上面的程序中，数字是偶数。

`if` 还有一个多样的，它包含一个可选语句组件，该组件在条件被求值之前执行。它的语法是：

```
if statement; condition {  
}
```

让我们重写使用上面的语法查找数字是偶数还是奇数的程序。

```
package main

import (  
    "fmt"
)

func main() {  
    if num := 10; num % 2 == 0 { //checks if number is even
        fmt.Println(num,"is even") 
    }  else {
        fmt.Println(num,"is odd")
    }
}
```

在 [playground](https://play.golang.org/p/_X9q4MWr4s) 上运行

在上面的程序 `num` 中 `if` 声明了初始化。需要注意的一件事是，`num` 仅可从 `if` 和内部访问 `else`。即：num 范围仅限于 `if else` 块。如果尝试 `num` 从 `if` 或外部进行访问` else`，编译器则会抛出错误。

让我们再编写一个使用的程序 `else if`。

```
package main

import (  
    "fmt"
)

func main() {  
    num := 99
    if num <= 50 {
        fmt.Println("number is less than or equal to 50")
    } else if num >= 51 && num <= 100 {
        fmt.Println("number is between 51 and 100")
    } else {
        fmt.Println("number is greater than 100")
    }

}
```

在 [playground](https://play.golang.org/p/Eji7vmb17Q) 上运行

在上面的程序中 `else if num >= 51 && num <= 100` 是 `true`，因此该程序将输出 `number is between 51 and 100`。

## 疑难杂症

`else` 语句应该在 `if` 语句的右大括号 `}` 之后的同一行中开始。如果没有，编译器就会抛出错误。

让我们通过一个程序来理解这一点。

```
package main

import (  
    "fmt"
)

func main() {  
    num := 10
    if num % 2 == 0 { //checks if number is even
        fmt.Println("the number is even") 
    }  
    else {
        fmt.Println("the number is odd")
    }
}
```

在 [playground](https://play.golang.org/p/RYNqZZO2F9) 上运行

在上面的程序中，`else` 语句不在 `if` 语句的结束 `}` 之后的同一行中开始。而是从下一行开始。这是不允许的。如果你运行这个程序，编译器会输出错误

```
main.go:12:5: syntax error: unexpected else, expecting }  
```

原因是因为 Go 自动插入分号的方式。您可以在 https://golang.org/ref/spec#Semicolons 中阅读有关分号插入规则的信息。

在规则中，如果分号是行的最后一个标记，则指定分号将插入到 `}` 之后。因此，在 `if` 语句的 `}` 后面自动插入分号。

所以我们的程序实际上变成了

```
if num%2 == 0 {  
      fmt.Println("the number is even") 
};  //semicolon inserted by Go
else {  
      fmt.Println("the number is odd")
}
```

分号之后。可以在第一行看到分号插入。以上片段第三行个。

因为如果 `{ ....... } else { ....... }` 是一个单独的语句，分号不应该出现在它的中间。因此，需要在结束 `}` 之后将 `else` 放在相同的行中。

我重写了程序，在 `if` 语句的结束 `}` 后面移动 `else` ，防止自动插入分号。

```
package main

import (  
    "fmt"
)

func main() {  
    num := 10
    if num%2 == 0 { //checks if number is even
        fmt.Println("the number is even") 
    } else {
        fmt.Println("the number is odd")
    }
}
```

在 [playground](https://play.golang.org/p/hv_27vbIBC) 上运行

现在编译器会很愉快,所以我们 :)

这就是本教程的结尾。希望你喜欢阅读。请留下您宝贵的意见和反馈。

### 下一个教程 - [循环](https://github.com/LeaningGo/go-learn/blob/master/go-loops.md)

