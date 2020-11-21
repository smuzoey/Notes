

# 1、导包

```
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

# 2、go web 基础



## 2.1 golang http.handler 

1、http.HanderFunc 																																	

```Go
/*
	HanderFunc 可以将普通函数（具有适当签名的）作为处理器使用
	使用 http.HandleFunc( func(http.ResponseWriter, *http.Request) )，传入处理器函数，参数必须满足 HandleFunc的要求
	
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