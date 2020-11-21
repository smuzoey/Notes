# 小记录

```Go 
var arr [10]int
var arr = [...]int{2, 3, 4}

// 数组定义切片
a := [5]int{1,2,3,4,5}
b := a[:]

--------------------------------------

fmt.Print()
fmt.Printf()
fmt.Println()

fmt.Sprint()
fmt.Sprintf()
fmt.Sprintln()

%d %v %T %f %t(bool) %c(byte)

for i := 0; i < len(a); i++ 
for k, v : range a 


--------------------------------
rune
strings.HasPrefix(s, prefix string) bool
strings.HasSuffix(s, suffix string) bool
strings.Contains(s, substr string) bool
strings.Index(s, str string) int
strings.LastIndex(s, str string) int
strings.Repeat(s, count int) string
strings.Count(s, str string) int
strings.Replace(str, old, new, n) string
strings.ToLower(s) string
strings.ToUpper(s) string
strings.Split(s, sep) 
strings.Join(sl []string, sep string) string

unsafe.Sizeof()
strconv.FormatInt(i, 10)  // 将 (int64)i 转换为10进制类型的字符串
strconv.FormatFloat(i,类型,保留小数点,格式化类型)  // FormatFloat(float64(f), 'f', 2, 64)
num, err := strconv.ParseInt(str, 10, 64)  // str, 进制, int64
num, _ := strconv.ParseFloat(str, 64)


sort.Ints(intList)
sort.Float64s(flag8List)
sort.Strins(stringList)

--------------------------------
// 切片

// make 创建切片  len = 4, cap = 8
var sliceA = make([]int, 4, 8)   

// 创建切片 len = 0, cap = 0
var sliceB []int 
// golang 无法使用下标扩容
sliceB = append(sliceB, 12)
SliceA = append(sliceA, sliceB...)  // a = a + b


// 切片赋值
sliceA = slieceB

sliceA = make([]int, 4, 4)
copy(sliceA, sliceB)

a.apend(a[:2], a[3:]...)  

------------------------------------------
// map
var userInfo1 = make(map[string]string)
userInfo["username"] = "张三",
userInfo["age"] = "21",

var userInfo2 = map[string]string {
    "username": "张三",
    "age":	"20",
}

userInfo3 := map[string]string {
    "username": "张三",
    "age":	"20",
}

for k, v := range userInfo {}
v, flag := userInfo["age"]  // 判断 key 是否存在
delete(userInfo, "age")  

var userInfo = make([]map[string]string, 2, 2)
userInfo[0]["username"] = "张三"
userInfo[0]["age"] = "22"
userInfo[1]["username"] = "李四"
userInfo[1]["age"] = "21"

var userInfo = make(map[string]string)
userInfo["username"] = "张三"
userInfo["hobby"] = []string {   //值为切片的 map
    "吃饭",
    "睡觉",
    "敲代码",
} 

-------------------------------------------
// 函数 
func sumFn(x ...int) int {   // x 为切片
	sum := 0
	for _, v := range x {
		sum += v
	}
	return sum
}

-----------------------
func add(x int, y int) int { return x + y }
func sub(x int, y int) int { return x - y }
func calc(x int, y int, cb func(int, int) int) int { return cb(x, y) }

func main() {
	fmt.Println(calc(1, 2, add))
}

------------------------
// 匿名函数
return func(x int, y int) int {
		return x * y
}

--------------------------
// 闭包
// 常驻内存、不污染全局 (与局部变量、全局变量不同)

func adder() func(x int) int {
	var i = 10
	return func(x int) int {
		i += x
		return i
	}
}

func main() {
    var fn = adder()   // fn此时为一个函数 (x int) int 
	fmt.Println(fn(10))  // 20
	fmt.Println(fn(5))  // 25
}

--------------------------------
// defer	延迟执行代码
// panic	抛出异常
// recover	接收异常

func fn2() {
	defer func() {
		err := recover()
		if err != nil {
			fmt.Println("err：", err)
		}
	}()
	panic("发生异常")
}

--------------------------
timeObj := time.Now()
str := timeObj.Format("2006-01-01 03:05:02")  // 格式化方式 举个栗子
unixTime := timeObj.Unix()	// 获得时间戳
timeObj2 := time.Unix(unixTime, 0)	// 根据时间戳返回时间

// 日期字符串转换为时间戳
str := "2020-04-30 15:38:04"
tmp := "2006-01-02 15:04:05"
timeObj, _ := time.ParseInLocation(tmp, str, time.Local)
fmt.Println(timeObj)
fmt.Println(timeObj.Unix())

// 定时器
ticker := time.NewTicker(time.Second)
for t := range ticker.C {
	fmt.Println(t)
}

相当于
for {
    time.Sleep(time.Second)
    fmt.Println("每隔一秒发一次")
}

-------------------------------------
make 和 new 均是用来分配内存的
new 指针
var  a = new(int)  // a 是 *int
var v *int = new(int)

make 用于 map、slice、channel

---------------------------------------
// 结构体 值类型
type myInt int
type myFn func(int, int) int

type Person struct {
    name string
    age int
    sex string
}
var p1 = Person 
var p2 = new(Person)
var p3 = &Person{}

// struct 转换为 json
jsonByte, _ := json.Marshal(s1)
jsonStr := string(jsonByte)

// json 转换为 struct
str := `{"ID":12,"Gender":"男","name":"李四","Sno":"s001"}`
var s2 Student
err := json.Unmarshal([]byte(str), &s2)
if err != nil {
	fmt.Println(err)
}

---------------------------------
pkg 包

go mod init ProjectName
import pkg "fmt"  // 使用别名
包中 init() 方法自动执行，系统调用

go get github.com/name/name
go mod download   //下载到 pkg/mod 
go mod vendor  // 直接下载到当前项目目录下

---------------------------------------
interface 接口  
名称后面带 er 表示为接口类型

type Usber interface {
    start()
    stop()
}

type Phone struct {
    Name string
}

func (p Phone) start() {
    fmt.Println(p.Name, "启动")
}
func (p Phone) start() {
    fmt.Println(p.Name, "关机")
}


// 空接口
type A interface{}
var a interface{}
空接口可以接收任意数据

// 断言  判断传入空接口的类型
var a interface{}
a = "你好"
v, ok := a.(string)  


-------------------------------
goroutine 协程
协程：2KB    线程：2MB
多协程切换调度开销方面比线程小，一个协程比线程占用内存小。
主线程可以开启多个协程
go test() // 开启协程

// 解决：协程未结束，主线程结束，则协程结束
1、主线程结束前 time.Sleep(time.Second)
2、sync.WaitGroup() 
	var wg sync.WaitGroup
	wg.Done()  // 协程计数器 -1
	wg.Add()  // +1
	wg.Wait()


-------------------------------------
channel 协程之间的通信 FIFO  引用数据类型  
ch := make(chan int, 4)
ch <- 21 
a := <-ch

close(ch)
for v := range ch {}  // 管道没有 key


// 管道阻塞 deadlock
1、管道放不下
2、管道取不出


--------------------------------------
反射
reflect.TypeOf()
v = reflect.ValueOf(x)
v.Kind()
v.Int

--------------------------------------
读取文件

1、
	只读方式打开文件 os.Open("")
	读取文件 file.Read()
	关闭流文件 defer file.Close()


2、
	只读方式打开文件 os.Open("")
	创建 reader 对象 reader := bufio.NewReader(file)
	ReadString 读取文件 str, err := reader.ReadString('\n') // 读取一行
	关闭文件流 defer file.Close()

3、
	封装好的  就一行就可以
	ioutil.ReadFile("")

写
```



值类型  引用类型。基本类型和数组、结构体  都是值类型

切片本质就是对底层数组的封装，包括：指针头  长度len   容量cap

String 无法修改，转化为 []byte 或 []rune 进行修改

```
s2 := "你好golang"
runeStr := rune(s2)
runeStr[0] = '大'
```

