
lang 教程系列](https://blog.csdn.net/baobaoxiannv/article/category/9365771)的第十四部分。

字符串在 Go 中值得特别提及，因为与其他语言相比，字符串在实现上有所不同。

## 什么是字符串
Go 中的字符串是字节切片。字符串可以通过将其内容封装在 `""` 中来创建。让我们来看一个创建字符串并打印它的简单示例。

```go
package main

import (  
    "fmt"
)

func main() {  
    name := "Hello World"
    fmt.Println(name)
}
```
在 [playground](https://play.golang.org/p/o9OVDgEMU0) 运行。

上面程序的输出是 `Hello World`。

Go 中的字符串是 Unicode 兼容的，并且是 UTF-8 编码的。

## 访问字符串的各个字节
由于字符串是字节的一部分，因此可以访问字符串的每个字节。

```go
package main

import (  
    "fmt"
)

func printBytes(s string) {  
    for i:= 0; i < len(s); i++ {
        fmt.Printf("%x ", s[i])
    }
}

func main() {  
    name := "Hello World"
    printBytes(name)
}
```
在 [playground](https://play.golang.org/p/XbJO2b0ZDW) 运行。

在上述程序的第八行中，`len(s)` 返回字符串中的字节数，我们使用 for 循环以十六进制表示法打印这些字节。`％x` 是十六进制的格式说明符。以上程序输出 `48 65 6c 6c 6f 20 57 6f 72 6c 64`。这些是 `Hello World` 的 `Unicode UT8` 编码值。为了更好地理解字符串，需要对 Unicode 和 UTF-8 有基本的了解。我建议阅读 https://naveenr.net/unicode-character-set-and-utf-8-utf-16-utf-32-encoding/ 以了解什么是 `Unicode` 和 `UTF-8`。


让我们稍微修改一下上面的程序，打印字符串的字符。
```go
package main

import (  
    "fmt"
)

func printBytes(s string) {  
    for i:= 0; i < len(s); i++ {
        fmt.Printf("%x ", s[i])
    }
}


func printChars(s string) {  
    for i:= 0; i < len(s); i++ {
        fmt.Printf("%c ",s[i])
    }
}

func main() {  
    name := "Hello World"
    printBytes(name)
    fmt.Printf("\n")
    printChars(name)
}
```
在 [playground](https://play.golang.org/p/Jss0HG1q80) 运行。

在该 `printChars` 函数中，`％c` 格式符说明用于打印字符串的字符。程序输出
```
48 65 6c 6c 6f 20 57 6f 72 6c 64  
H e l l o   W o r l d  
```

尽管上面的程序看起来像是访问字符串中各个字符的合法方法，但这有一个严重的错误。让我们打破这段代码，看一下究竟做错了什么。

```go
package main

import (  
    "fmt"
)

func printBytes(s string) {  
    for i:= 0; i < len(s); i++ {
        fmt.Printf("%x ", s[i])
    }
}

func printChars(s string) {  
    for i:= 0; i < len(s); i++ {
        fmt.Printf("%c ",s[i])
    }
}

func main() {  
    name := "Hello World"
    printBytes(name)
    fmt.Printf("\n")
    printChars(name)
    fmt.Printf("\n")
    name = "Señor"
    printBytes(name)
    fmt.Printf("\n")
    printChars(name)
}
```
在 [playground](https://play.golang.org/p/UQOVvRVaFH) 运行。

上面程序的输出是
```
48 65 6c 6c 6f 20 57 6f 72 6c 64  
H e l l o   W o r l d  
53 65 c3 b1 6f 72  
S e Ã ± o r  
```

上面程序第二十八行中，我们正在尝试打印 Senor 的字符，它的输出是 se A±or，这是错误的。为什么这个程序中断时，它是完全正确的与 `Hello World`。因为 n 的 Unicode 编码指针是 U+00F1，它的 UTF-8 编码占用了 2 个字节的 c3 和 b1。我们试图打印字符假设每个代码指针将是一个字节长，这是错误的。在 UTF-8 编码中，一个编码指针可以占用 1 个字节以上。我们怎么解这个。这就是 rune 拯救我们的地方。

## rune

rune 是 go 的内置类型，它是 int32 的别名。rune 表示 Go 中的一个 Unicode 编码指针。代码指针占用多少字节并不重要，它可以用一个 rune 来表示。让我们修改上面的程序来使用 rune 打印字符。

```go
package main

import (  
    "fmt"
)

func printBytes(s string) {  
    for i:= 0; i < len(s); i++ {
        fmt.Printf("%x ", s[i])
    }
}

func printChars(s string) {  
    runes := []rune(s)
    for i:= 0; i < len(runes); i++ {
        fmt.Printf("%c ",runes[i])
    }
}

func main() {  
    name := "Hello World"
    printBytes(name)
    fmt.Printf("\n")
    printChars(name)
    fmt.Printf("\n\n")
    name = "Señor"
    printBytes(name)
    fmt.Printf("\n")
    printChars(name)
}
```
在 [playground](https://play.golang.org/p/t4z-f8I-ih) 运行。

在上面程序第十四行中，字符串被转换成一个 rune 切片。然后循环并显示字符。这个程序输出
```
48 65 6c 6c 6f 20 57 6f 72 6c 64  
H e l l o   W o r l d 

53 65 c3 b1 6f 72  
S e ñ o r  
```

上面的输出是完美的。只是想要我们想要的 :)

## 对于字符串的 range 循环

上面的程序是迭代字符串的各个 rune 的完美方法。但是 Go 使用 for range 循环为我们提供了一种更简单的方法。
```go
package main

import (  
    "fmt"
)

func printCharsAndBytes(s string) {  
    for index, rune := range s {
        fmt.Printf("%c starts at byte %d\n", rune, index)
    }
}

func main() {  
    name := "Señor"
    printCharsAndBytes(name)
}
```
在 [playground](https://play.golang.org/p/BPpQ0dZr8W) 运行。

在上述程序第八行中，使用 for range 循环对字符串进行迭代。循环返回 rune 与 rane 一起开始的字节位置。该程序输出

```
S starts at byte 0  
e starts at byte 1  
ñ starts at byte 2
o starts at byte 4  
r starts at byte 5  
```

从上面的输出中很明显，`ñ` 占用了2个字节 :)

## 从字节切片构造字符串
```go
package main

import (  
    "fmt"
)

func main() {  
    byteSlice := []byte{0x43, 0x61, 0x66, 0xC3, 0xA9}
    str := string(byteSlice)
    fmt.Println(str)
}
```
上面程序中的 `byteSlice` 包含字符串 `Café` 的 UTF-8 编码十六进制字节。程序输出 `Café`

如果我们具有十六进制值的十进制等效值，该怎么办。上述程序可以工作吗？让我们来看看。

```go
package main

import (  
    "fmt"
)

func main() {  
    byteSlice := []byte{67, 97, 102, 195, 169}//decimal equivalent of {'\x43', '\x61', '\x66', '\xC3', '\xA9'}
    str := string(byteSlice)
    fmt.Println(str)
}
```
在 [playground](https://play.golang.org/p/BPpQ0dZr8W) 运行。

上面的程序也会输出 `Café`

## 从切片的 rune 构造一个字符串

```go
package main

import (  
    "fmt"
)

func main() {  
    runeSlice := []rune{0x0053, 0x0065, 0x00f1, 0x006f, 0x0072}
    str := string(runeSlice)
    fmt.Println(str)
}
```
在 [playground](https://play.golang.org/p/m8wTMOpYJP) 运行。

在上面的程序中，runeSlice 包含十六进制字符串 Señor 的 Unicode 代码指针。程序输出 `Señor`。

## 字符串长度
utf8 包的函数 func RuneCountInString(s string) (n int) 用于查找字符串的长度。该方法以字符串为参数，并返回其中的 rune 数量。
```go
package main

import (  
    "fmt"
    "unicode/utf8"
)



func length(s string) {  
    fmt.Printf("length of %s is %d\n", s, utf8.RuneCountInString(s))
}
func main() {  

    word1 := "Señor" 
    length(word1)
    word2 := "Pets"
    length(word2)
}
```
在 [playground](https://play.golang.org/p/m8wTMOpYJP) 运行。

上面程序的输出是：
```
length of Señor is 5  
length of Pets is 4  
```

## 字符串是不可变的
字符串在 Go 中是不可变的。一旦创建了一个字符串，就不可能更改它。

```
package main

import (  
    "fmt"
)

func mutate(s string)string {  
    s[0] = 'a'//any valid unicode character within single quote is a rune 
    return s
}
func main() {  
    h := "hello"
    fmt.Println(mutate(h))
}
```
在 [playground](https://play.golang.org/p/m8wTMOpYJP) 运行。

在上述程序的第八行中，我们尝试将字符串的第一个字符更改为 'a'。这是不允许的，因为字符串是不可变的，因此程序抛出错误：`main.go:8: cannot assign to s[0]`

为了解决这个字符串的不变性，字符串被转换成一个 rune 切片。然后，用所需的任何更改对该切片进行修改，并将其转换回新的字符串。

```go
package main

import (  
    "fmt"
)

func mutate(s []rune) string {  
    s[0] = 'a' 
    return string(s)
}
func main() {  
    h := "hello"
    fmt.Println(mutate([]rune(h)))
}
```
在 [playground](https://play.golang.org/p/m8wTMOpYJP) 运行。

在上述程序第七行中：mutate 函数接受一个 rune 切片作为参数。然后将切片的第一个元素更改为 'a'，将 rune 转换回字符串并返回。这个程序输出 `aello`

这就是字符串，祝你有美好的一天 :)

## 下一个教程 - [指针](https://blog.csdn.net/baobaoxiannv/article/details/102601008)
