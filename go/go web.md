# 1、Go 基础知识



## 1.1 Go 语言特性

​	Go 为静态语言，静态语言在编译时确定数据类型，是强类型语言，如Java、C等；动态语言在运行时才确定数据类型，如PHP、Python等。

- **弱类型语言**是数据类型可以被忽略的语言。它与强类型语言相反，一个变量可以赋不同数据类型的值。一个变量的类型是由其上下文决定的，效率更高。
- **强类型语言**是必须强制确定数据类型的语言，一旦一个变量被指定了某个数据类型，如果不经过强制转换，那么它就永远是这种数据类型。一个变量的类型是申明的时候就已经确定的，更安全。

​	Go语言没有类和继承的概念，它通过接口来实现多态，



## 1.2 基本数据类型

```Go
1、常量	
const Pi = 3.1415926
const a string = "abc"

2、变量
var a int = 15
var c = 5
var c bool = false
var d string = "Go says hello to the world!"
e := 2
值类型：int、float、bool、string
引用类型：指针、slices、maps、channel
每个包可以包含一个 init() ，在 main() 前执行

3、字符串
长度	len(str)
是否以prefix开头		strings.HasPrefix(s, prefix string) bool
是否以suffix结尾		strings.HasSuffix(s, suffix string) bool
是否包含substr		strings.Contains(s, substr string) bool
分割字符串			strings.Split(s, sep)/strings.Field(s) 返回 slice
修剪字符串，剔除开头和结尾			string.Trem(s, "cut")/strings.TrimSpace(s)
字符串为 UTF-8 字符，当字符为 ASCII 编码占1个字节，其他字符根据需要占 2-4 个字节。

4、时间和日期
fmt.Println(t.Format("02 Jan 2006 15:04")) 

```



## 1.3 导包

```Go
go mod init ProjectName
import pkg "fmt"  // 使用别名
包中 init() 方法自动执行，系统调用

go get github.com/name/name
go mod download   //下载到 pkg/mod 
go mod vendor  // 直接下载到当前项目目录下

--------------------------------------------

go mod init ProjectName  // 系统自动生成 go.mod 
go mod download github.com/go-sql-driver/mysql

```



## 1.4 内置函数

Go 语言拥有一些不需要进行导入操作就可以使用的内置函数。它们有时可以针对不同的类型进行操作，例如：len、cap 和 append，或必须用于系统级的操作，例如：panic。因此，它们需要直接获得编译器的支持。

| 名称               | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| close              | 用于管道通信                                                 |
| len、cap           | len 用于返回某个类型的长度或数量（字符串、数组、切片、map 和管道）；cap 是容量的意思，用于返回某个类型的最大容量（只能用于切片和 map） |
| new、make          | new 和 make 均是用于分配内存：new 用于值类型和用户定义的类型，如自定义结构，make 用于内置引用类型（切片、map 和管道）。它们的用法就像是函数，但是将类型作为参数：new(type)、make(type)。new(T) 分配类型 T 的零值并返回其地址，也就是指向类型 T 的指针（详见第 10.1 节）。它也可以被用于基本类型：`v := new(int)`。make(T) 返回类型 T 的初始化之后的值，因此它比 new 进行更多的工作（详见第 7.2.3/4 节、第 8.1.1 节和第 14.2.1 节）**new() 是一个函数，不要忘记它的括号** |
| copy、append       | 用于复制和连接切片                                           |
| panic、recover     | 两者均用于错误处理机制                                       |
| print、println     | 底层打印函数（详见第 4.2 节），在部署环境中建议使用 fmt 包   |
| complex、real imag | 用于创建和操作复数（详见第 4.5.2.2 节）                      |

# 2、go web 基础



## 2.1  服务端的几种方法

1、http.HanderFunc 																																	

```Go
/*
	HanderFunc 可以将普通函数（具有适当签名的）作为处理器使用
	使用 http.HandleFunc( func(http.ResponseWriter, *http.Request) )，传入处理器函数
	
	ListenAndServe监听TCP地址addr，并且会使用handler参数调用Serve函数处理接收到的连接。handler参数一般会设为nil，此时会使用DefaultServeMux。
*/

// 处理器函数
func handler(w http.ResponseWriter, r *http.Request) {
   fmt.Fprintln(w, "hello", r.URL.Path)
}

func main() {
   http.HandleFunc("/", handler)
   // 创建路由, nil 使用默认的复用器
   http.ListenAndServe(":8080", nil)
}
```





```Go
package main

import (
    "fmt"
    "net/http"
)

type HelloHandler struct{}
func (h HelloHandler) ServeHTTP (w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello Handler!")
}

func hello (w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello!")
}

func main() {
    server := http.Server{
        Addr: "127.0.0.1:8080",
    }
    helloHandler := HelloHandler{}
    http.Handle("/hello1", helloHandler)
    http.HandleFunc("/hello2", hello)
    server.ListenAndServe()
}
```



**Handler、HandlerFunc、ServeHTTP**

```
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}

type HandlerFunc func(ResponseWriter, *Request)

func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
    f(w, r)
}
```





# 3、单元测试

filename :  _test.go

funcname: Testxxx(*testing.T)

go test