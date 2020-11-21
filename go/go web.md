# 导包

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







# golang http.handler 



```
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





# 单元测试

filename :  _test.go

funcname: Testxxx(*testing.T)

go test