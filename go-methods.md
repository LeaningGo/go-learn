
这里是 [golang 学习教程系列](https://blog.csdn.net/baobaoxiannv/article/category/9365771)的第十七部分。

## 入门

方法只是在 func 关键字和方法名之间具有特殊接收器类型的[函数](https://blog.csdn.net/baobaoxiannv/article/details/101059929)。接收方可以是结构体类型，也可以是非结构体类型。

下面提供了方法声明的语法。

```go
func (t Type) methodName(parameter list) {  
}
```

上面的代码段创建了一个名为 methodName 的方法，该方法具有接收者类型。调用 t 作为接收方，可以在方法中访问它。（题外话，译者在这里认为方法就相当于 Java 中的继承关系，上面的代码用 Java 语言来说就是：methodName 继承了 Type）

## 方法示例
让我们编写一个简单的程序，在结构体类型上创建一个方法并调用它。

**未完成！**
