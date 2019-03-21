#### 参数传递
```
函数调用参数均为值传递，不是指针传递或引用传递。经测试引申出来，当参数变量为指针或隐式指针类型，参数传递方式也是传值（指针本身的copy）
```

#### 数组与slice
```
//数组是初始化就定长
var a [5] int
a:=[5]int{1,2}
a:=[...]int{2:3,3:4}

func test(a [] int)  //值传递
func test(a *[] int)

test(a)
test(&a)

//slice切片又称为变长数组
var a [] int
a:=[]int{1,2,3}
a:=make([]int,4)

func test(a [] int) //slice按引用传递

test(a)

append(a,1)

//数组与slice转换
var a [6] int
var b [] int = a[:]

//slice、map、interface、channel都是按引用传递
```

//一些功能


#### map
```
//声明
var my map[string] int;
my := make(map[string] int)
//赋值
my['dada'] = 1

rating := map[string]float32 {"C":5, "Go":4.5, "Python":4.5, "C++":2 }
// map有两个返回值，第二个返回值，如果不存在key，那么ok为false，如果存在ok为true
csharpRating, ok := rating["C#"]
if ok {
}

m := make(map[string]string)
m["Hello"] = "Bonjour"
m1 := m
m1["Hello"] = "Salut" // 现在m["hello"]的值已经是Salut了

//map 指针
myMap := make(map[string]*Test)
var x Test
x.a = 2
x.b = "abc"
myMap["a"] = &x
(*myMap["a"]).a = 10

```

#### make、new
make用于内建类型（map、slice 和channel）的内存分配。new用于各种类型的内存分配。
```
list:= make([]int,5,10) //初始化5个int的数组，可扩展性为10个
len(list)  //5
cap(list)  //10
```

#### 指针
```
type A struct{x int}
a:=new(A)
a.x = 1     //正确
(*a).x = 1  //正确
```

#### defer panic recover
```
defer func(){
    if a:=recover();a!=nil{
        .....
    }
}()

panic("dadad")

```

#### init
初始化导入的包（递归导入）
对包块中声明的变量进行计算和分配初始值
执行包中的init函数
```
package main

import "fmt"

var _ int64=s()

func init(){
    fmt.Println("init function --->")
}

func s() int64{
    fmt.Println("function s() --->")
    return 1
}

func main(){
    fmt.Println("main --->")
}
//输出
function s() --->
init function --->
main --->
```

#### import
```
import(
    . "fmt"
)
import(
    f "fmt"  // f.Println()
)
import (
    "database/sql"
    _ "github.com/ziutek/mymysql/godrv"   //不使用包的函数 只是用init()
)

```

#### struct
```
type person struct{
    name string
    age int
}

p:= person{"malx",25}
p:= person{name:"malx",age:25}

type Human struct {
    name string
    age int
    weight int
}
type Student struct {
    Human // 匿名字段，那么默认Student就包含了Human的所有字段
    speciality string
}

p:=Student{Human{"malx",25},"dada"}
p:=Student{Human:Human{"malx",25},speciality:"dada"}

```

#### 复杂结构
```
type Values map[string][]string
func (v Values) Get(s string)string{
    if vs:=map[s];len(vs)>0{
        return vs[0]
    }
    return ""
}

func (v Values) Add(key, str string){
    v[key] = append(v[key],str)
}


m:=Values{"lang":{"en"}}
m.Add("item1","1")
m.Add("item2","2")

fmt.Println(m.Get("item")) // "1" (first value)
fmt.Println(m["item"]) // "[1 2]" (direct map access)
m = nil
fmt.Println(m.Get("item")) // ""
m.Add("item", "3") // panic: assignment to entry in nil map

```

#### 函数值
```
type A struct{
    x int
}
//第一种
func (a A) test(){
    fmt.Println(a.x)
}

var f func(a A)
var a A
a.x = 2
f(a)

//第二种
func (a *A) test(){
    fmt.Println(a.x)
}

var f func(a *A)
var a A
a.x = 2
f(&a)


```


#### 指针对象
```
type IntList struct{
    value int
    next *IntList
}
```

#### OOP
```
type Rectangle struct {
    width, height float64
}
type Circle struct {
    radius float64
}
func (r Rectangle) area() float64 {
    return r.width*r.height
}
func (c Circle) area() float64 {
    return c.radius * c.radius * math.Pi
}

func (c *Circle) setWidth(a float64){
    c.width = a
}

```
### 继承
```
type Human struct {
    name string
    age int
    phone string
}
type Student struct {
    Human //匿名字段
    school string
}
type Employee struct {
    Human //匿名字段
    company string
}
//在human上面定义了一个method
func (h *Human) SayHi() {
    fmt.Printf("Hi, I am %s you can call me on %s\n", h.name, h.phone)
}
func main() {
    mark := Student{Human{"Mark", 25, "222-222-YYYY"}, "MIT"}
    sam := Employee{Human{"Sam", 45, "111-888-XXXX"}, "Golang Inc"}
    mark.SayHi()
    sam.SayHi()
}
```

#### 私有变量
大小写来实现(大写开头的为共有，小写开头的为私有)

#### interface
interface{}类型的变量也被当作go语言中的void\*指针来使用interface{}类型的变量也被当作go语言中的void\*指针来使用
其实 interface就是个协议，无论你输入指针，还是非指针，都一样
```
//interface == void*
type Human interface{
    setName(name string)
    setAge(age int)
    getName()string
    getAge()int
}

func NewHuman(){

}

```

#### interface{} 类型转换
```
type __s struct{
    x int
}

element := make([]interface{},4)
a := __s{2}
element[0] = &a   // interface{} 赋值为 * __s
element[1] = a    // interface{} 赋值为 __s(复制)
b,ok := element[0].(*__s)
if ok{
    fmt.Println((*b).x)
}
c,ok := element[1].(__s)
if ok{
    fmt.Println(c.x)
}

```
#### 泛型
```
func main() {
    list := make(List, 3)
    list[0] = 1 //an int
    list[1] = "Hello" //a string
    list[2] = Person{"Dennis", 70}
    for index, element := range list{
        switch value := element.(type) {
            case int:
                fmt.Printf("list[%d] is an int and its value is %d\n", index, value)
            case string:
                fmt.Printf("list[%d] is a string and its value is %s\n", index, value)
            case Person:
                fmt.Printf("list[%d] is a Person and its value is %s\n", index, value)
            default:
                fmt.Println("list[%d] is of a different type", index)
        }
    }
}
```

#### 函数类型转换
```
type Handler interface{
    ServeHTTP(res, *req)
}

type HandlerFunc func(res, *req)

func (h HandlerFun  c) ServeHTTP(res, *req){
    h(res, *req)
}

func Handle(pattern string, handler Handler){
    ....
}

func Myfunc(res, *req){
    ...
}

func main(){
    Handle("/dsad/dsada",HandlerFunc(Myfunc))


}

//实例

type i_bin interface{
	funcx(int)
}

type funcxx func(int)

func (f funcxx) funcx(a int){
	f(a)
}

func ff(i i_bin, x int){
	i.funcx(x)
}

func myf(a int){
	fmt.Println(">>> ",a+1)
}

func myf1(a int){
	fmt.Println(">>> ",a+2)
}

func main(){
	mymap := make(map[string]i_bin)
	mymap["a"] = funcxx(myf)
	mymap["b"] = funcxx(myf1)

	mymap["a"].funcx(1)
	mymap["b"].funcx(1)
}

```



#### 反射
```
1、从relfect.Value中获取接口interface的信息
（1）已知原有类型【进行“强制转换”】
var a int32 = 2
pointer := reflect.ValueOf(&a)
value := reflect.ValueOf(a)
convertPointer := pointer.Interface().(*int32)
convertValue := value.Interface().(int32)


（2）未知原有类型【遍历探测其Filed】
type __s struct{
	Name string
	Age int
	Sex bool
}

func TestRef(in interface{}){

	Type := reflect.TypeOf(in)
	Value := reflect.ValueOf(in)

	for i:=0;i<Type.NumField();i++{
		fieldType := Type.Field(i)
		fieldValue := Value.Field(i).Interface()
		fmt.Printf("%s %v = %v\n",fieldType.Name,fieldType.Type,fieldValue)
	}

	for i:=0;i<Type.NumMethod();i++{
		m := Type.Method(i)
		fmt.Printf("%s: %v\n",m.Name,m.Type)
	}

}

2、通过reflect.Value设置实际变量的值

3、实例（结构体的修改）
type Animal struct{
    Name    string
    Age     int
}

a := Animal{"aaa",14}

x := reflect.ValueOf(&a)
if x.Kind()!=reflect.Ptr{
    wrong
}

elem := x.Elem()
if elem.Kind()!=reflect.Struct{
    wrong
}
for i:=0;i<elem.NumField();i++{
    Name := elem.Type().Field(i).Name 

}







```


#### goroutine

#### channels

#### channels buffer

#### range close

#### select

#### time.After

#### strconv
```
strconv.Atoi //string to int
```

#### regexp
```

```


#### io
```
```

#### container
```
"container/list"   //链表
"container/heap"  //堆
"container/ring"  //环
```

#### Tag
```
import (
    "fmt"
    "reflect"
)

type My struct{
    Name string `json:"Name"`
}

a:=My{Name:"malx"}
b:=reflect.TypeOf(a)
b.Field(0).Type
b.Field(0).Name
b.Field(0).Tag
b.Field(0).Tag.Get("json")

a := MyStruct{Name:"malx",Age:29}
aT:= reflect.TypeOf(a)
aV:=reflect.ValueOf(a)
for i:=0;i<aT.NumField();i++{
	c:=aT.Field(i)
	d:=aV.Field(i)
	fmt.Println(c,d)
}
```


#### 文件上传
```
func UpLoadFile(w http.ResponseWriter, r *http.Request){
    r.ParseMultipartForm(32 << 20);
    files := r.MultipartForm.File['filename']
    myfile := files[0]

    f,err := myfile.Open();
    if err{
        wrong
    }
    defer f.Close()
    cur, err := os.Create("./upload/" + header.Filename)
    defer cur.Close()
    io.Copy(cur, f)

}
```

#### unsafe.Pointer
相当于void*, 指针间的转换要先转换成unsafe.Pointer,再转换成其他指针
```
import (
    "unsafe"
)
x := []int{1, 2, 3, 4}
y := unsafe.Pointer(&x)
z := *(*[]int)(y)
z[0] = 111
```

#### reflect类型判断
```
x := []map[string]int{}
fmt.Println(reflect.TypeOf(x).Kind())
a := reflect.ValueOf(&x)
b := reflect.TypeOf(&x)
fmt.Println(a.Type().Kind())
fmt.Println(b.Kind())

slice
ptr
ptr

```

#### reflect指针
```
x := []int{1, 2, 3, 4}
a := reflect.ValueOf(&x)
p := *(*[]int)(unsafe.Pointer(a.Elem().Addr().Pointer()))
p[0] = 111
fmt.Println(x)

```

#### io
(1)read
```
import(
    "bufio"
    "os"
    "ioutil"
)

//一次性读取
f,err = os.Open("file")
s := ioutil.ReadAll(f)

//分块读取
f,err = os.Open("file")
buf := make([]byte,10)
rd := bufio.NewReader(f)
n,err := rd.Read(buf)

//按行读取
rd := bufio.NewReaderSize(f,4096)
rd: = bufio.NewReader(f)
line,err := rd.ReadString('\n')
line,err := rd.ReadLine()

```
(2)write
```
//带缓冲区读写
fd,_ := os.OpenFile("bbb.txt")
w := bufio.NewWriterSize(fd,4096) //带缓冲的写
w.WriteString("dadadadad")
w.Write([]byte("dsadadada\n"))
w.flush()

//输出屏幕
w := bufio.NewWriterSize(os.Stdout,111)
w1 := bufio.NewReader(f,111)
w1.WriteTo(w)
```
(3)其他函数
```
rd.Buffered()   //表示已经缓冲的数据的大小
w.Available()   //表示可使用的缓冲区的大小

//输出文件
ioutil.WriteFile("11.gv", []byte(graph.String()), 0666)


//实例
package main

import (
	"bufio"
	"fmt"
	"os"
	"unsafe"

	"github.com/awalterschulze/gographviz"
)

func main() {

	graphAst, _ := gographviz.Parse([]byte(`digraph G{}`))
	graph := gographviz.NewGraph()
	gographviz.Analyse(graphAst, graph)
	graph.AddNode("G", "a", nil)
	graph.AddNode("G", "b", nil)
	graph.AddEdge("a", "b", true, nil)
	fmt.Println(graph.String())

	f, _ := os.Open("sjm.txt")
	f1, _ := os.OpenFile("sjm.txt1", os.O_WRONLY|os.O_CREATE, 0664)
	rd := bufio.NewReader(f)
	rd1 := bufio.NewWriter(f1)
	for line, err := []byte{0}, error(nil); len(line) > 0 && err == nil; {
		line, _, err = rd.ReadLine()
		x := (*string)(unsafe.Pointer(&line))
		if *x == "" {
			continue
		}
		line = append(line, '\n')
		rd1.Write(line)
		//fmt.Printf("%v %v\n", *x, p)
	}

	f.Close()
	f1.Close()
}
```


#### net/http
(1)基础结构
```
//handler重中之重的基础
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}

//重要的serveMux路由结构
type ServeMux struct {
    mu    sync.RWMutex
    m     map[string]muxEntry
    hosts bool 
}

type muxEntry struct {
    explicit bool
    h        Handler
    pattern  string
}

//服务器
func ListenAndServe(addr string, handler Handler) error {
    server := &Server{Addr: addr, Handler: handler}
    return server.ListenAndServe()
}

type Server struct {
    Addr         string        
    Handler      Handler       
    ReadTimeout  time.Duration 
    WriteTimeout time.Duration 
    TLSConfig    *tls.Config   

    MaxHeaderBytes int

    TLSNextProto map[string]func(*Server, *tls.Conn, Handler)

    ConnState func(net.Conn, ConnState)
    ErrorLog *log.Logger
    disableKeepAlives int32     nextProtoOnce     sync.Once 
    nextProtoErr      error     
}


```


(1)函数作为处理器
```
func helloHandler(w http.ResponseWriter, req *http.Request) {
    io.WriteString(w, "hello, world!\n")
}

http.HandlerFunc()

```
(2)自定义处理器





#### defer panic recover
```
//Defer function按照后进先出的规则执行。例如下面的代码打印“3210”。
func b() {
    for i := 0; i < 4; i++ {
        defer fmt.Print(i)
    }
}

//Defer function在方法的return之后执行，如果defer function修改了return的值，返回的是defer function修改后的值。例如下面的例子返回2而不是1。
func c() (i int) {
    defer func() { i++ }()
    return 1
}

```

### serveFile
```
type Dir string
func (d Dir) Open(name string) (File, error) {
  // ...
}
type FileSystem interface {
  Open(name string) (File, error)
}
http.FileServer()
http.FileServer() 方法返回的是 fileHandler 实例，而 fileHandler 结构体实现了 Handler 接口的方法 ServeHTTP()。ServeHTTP 方法内的核心是 serveFile() 方法。
// 所属文件: src/net/http/fs.go, 690-716行
type fileHandler struct {
  root FileSystem
}
func FileServer(root FileSystem) Handler {
  return &fileHandler{root}
}
func (f *fileHandler) ServeHTTP(w ResponseWriter, r *Request) {
  upath := r.URL.Path
  if !strings.HasPrefix(upath, "/") {
    upath = "/" + upath
    r.URL.Path = upath
  }
  serveFile(w, r, f.root, path.Clean(upath), true)
}
// 所属文件: src/net/http/server.go, 82行
type Handler interface {
  ServeHTTP(ResponseWriter, *Request)
}

//实例
var rootPath = "/data_dir/malx/test/"

func down(w http.ResponseWriter, r *http.Request) {
	fmt.Println(">>>")
	mpath := path.Join(rootPath,r.URL.Path)
	fmt.Println(mpath)
	http.ServeFile(w, r, mpath)
}

http.HandleFunc("/",down)
```

### url
```
r.URL *url.URL
```

### 查看GC
```
GODEBUG=gctrace=1 go run cmd/agent_bin.go
```

### pprof
```
//pprof是golang程序一个性能分析的工具，可以查看堆栈、cpu信息等。
_ "net/http/pprof"
go tool pprof http://localhost:8080/debug/pprof/heap

```

### GC调优
```
1、减少对象分配
2、函数尽量不要返回map， slice对象, 这种频繁调用的函数会给gc 带来压力。
小对象要合并。
3、函数频繁创建的简单的对象，直接返回对象，效果比返回指针效果要好。
4、避不开，能用sync.Pool 就用，虽然有人说1.10 后不推荐使用sync.Pool，但是压测来看，确实还是用效果，堆累计分配大小能减少一半以上。
5、类型转换要注意，官方用法消耗特别大，推荐使用雨痕的方式。
6、避免反复创建slice。
7、建议多用unsafe.pointer
8、对于一些短小的对象，复制成本远小于在堆上分配和回收操作。
9、预定义make大小 make(map[string]string,10000)


//小对象的操作
避免使用make及指针的操作，可以让他在栈中生成，减少堆内存的分配
对于小对象，直接将数据交由 map 保存，远比用指针高效。这不但减少了堆内存分配，关键还在于垃圾回收器不会扫描非指针类型 key/value 对象。
函数返回对象指针时，必然在堆上分配。

//mutex和chan
channel 算是一种很 “重” 的实现。在小粒度层面，其性能真算不得好，不如用mutex

//接口的调用
接口调用和普通调用存在很大差别
首先，相比静态绑定，动态绑定性能要差很多；其次，运行期需额外开销，比如接口会复制对象，哪怕仅是个指针，也会在堆上增加一个需 GC 处理的目标。

//sync.Pool
对于可能反复创建的变量可以用缓存池
for i:=0;i<10000;i++{
    a = make([]int,1000)
}

//map诟病
1、slice的gc时间是远远快于map的，而map存储指针则是最慢的，不推荐。
2、避免使用大map
即使映射键和值类型不包含指针，并且映射在GC运行之间没有更改，也会发生这种情况
3、map 不会收缩 “不再使用” 的空间。就算把所有键值删除，它依然保留内存空间以待后用。map=nil

```

### array和slice
```
array在栈上
slice在堆上
```

### cgo
```
cgo针对该场景定义了专门的规则：在CGO调用的C语言函数返回前，cgo保证传入的Go语言内存在此期间不会发生移动，C语言函数可以大胆地使用Go语言的内存！

Go调用C Code时，Go传递给C Code的Go指针所指的Go Memory中不能包含任何指向Go Memory的Pointer。
不能嵌套其他指向Go Memory的指针

func C.CString(string) *C.char
func C.CBytes([]byte) unsafe.Pointer
func C.GoString(*C.char) string
func C.GoStringN(*C.char, C.int) string
func C.GoBytes(unsafe.Pointer, C.int) []byte

dir := make([]*C.char,2)
for i,_ := range dir{
    dir[i] = (*C.char)(C.malloc(10))
    defer C.free(unsafe.Pointer(dir[i]))
}
C.fill_array1((**C.char)(unsafe.Pointer(&dir[0])))
clen := C.strlen(dir[0])

x:=C.GoBytes(unsafe.Pointer(dir[0]),10)
fmt.Println(x)

dir := make([]*C.char,2)
C.fill_array1((**C.char)(unsafe.Pointer(&dir[0])))
// void fill_array1(char* array[2]){
//      array[0] = (char*)malloc(10);
//}
defer C.free(unsafe.Pointer(dir[0]))
x:=C.GoBytes(unsafe.Pointer(dir[0]),10)
fmt.Println(x)

a := "dadad"
b := []byte(a)
C.fill_array((*C.char)(unsafe.Pointer(&b[0])))


//注意要活学活用
func C.GoString(*C.char) string
func C.GoStringN(*C.char, C.int) string
func C.GoBytes(unsafe.Pointer, C.int) []byte

```

### go build -a
```
强行对所有涉及到的代码包（包含标准库中的代码包）进行重新构建，即使它们已经是最新的了。
cgo中非常重要
```