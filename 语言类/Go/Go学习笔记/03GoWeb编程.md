# 使用 net/http

## GoWeb基础知识

### Content-Type 标头

`Content-Type:` 响应标头是告知客户端内容的类型，客户端再根据这个信息将内容正确地呈现给用户。

常见的内容类型有：

- `text/html` —— HTML 文档
- `text/plain` —— 文本内容
- `text/css`—— CSS 样式文件
- `text/javascript` —— JS 脚本文件
- `application/json`—— JSON 格式的数据
- `application/xml` —— XML 格式的数据
- `image/png` —— PNG 图片



## 第 2 章：Web 服务器的创建 

#### 2.1 简介

Go 提供了一系列用于创建 Web 服务器的标准库，而且通过 Go 创建一个服务器的步骤非常简单，只要通过 net/http 包调用 **ListenAndServe** 函数并传入**网络地址**以及负责处理请求的**处理器**( handler )作为参数就可以了。如果网络地址参数为空字符串，那么服务器默认**http使用 80 端口**，**https使用443端口**进行网络连接；如果处理器参数为 nil，那么服务器将使用默认的多路复用器 **DefaultServeMux**，当然，我们也可以通过调用 **NewServeMux** 函数创建一个多路复用器。多路复用器接收到用户的请求之后根据请求的 URL 来判断使用哪个**处理器**来处理请求，找到后就会重定向到对应的处理器来处理请求。

**web的工作原理**：

![image-20220508000100950](https://img.herrluk.icu/typoraPicture/image-20220508000100950-3662827.png)

Web 服务器的工作原理可以简单地归纳为：

客户机通过 TCP/IP 协议建立到服务器的 TCP 连接
客户端向服务器发送 HTTP 协议请求包，请求服务器里的资源文档
服务器向客户机发送 HTTP 协议应答包，如果请求的资源包含有动态语言的内容，那么服务器会调用动态语言的解释引擎负责处理 “动态内容”，并将处理得到的数据返回给客户端
客户机与服务器断开。由客户端解释 HTML 文档，在客户端屏幕上渲染图形结果
一个简单的 HTTP 事务就是这样实现的，看起来很复杂，原理其实是挺简单的。需要注意的是客户机与服务器之间的通信是非持久连接的，也就是当服务器发送了应答后就与客户机断开连接，等待下一次请求。

我们浏览网页都是通过 URL 访问的，那么 URL 到底是怎么样的呢？

URL (Uniform Resource Locator) 是 “统一资源定位符” 的英文缩写，用于描述一个网络上的资源，基本格式如下

```
scheme://host[:port##]/path/.../[?query-string][##anchor]
scheme         指定底层使用的协议(例如：http, https, ftp)
host           HTTP 服务器的 IP 地址或者域名
port##          HTTP 服务器的默认端口是 80，这种情况下端口号可以省略。如果使用了别的端口，必须指明，例如 http://www.cnblogs.com:8080/
path           访问资源的路径
query-string   发送给 http 服务器的数据
anchor         锚
```

要编写一个 Web 服务器很简单，只要调用 http 包的两个函数就可以了

```go
http.HandleFunc("/", handler) // 设置访问的路由
err := http.ListenAndServe(":9090", nil) // 设置监听的端口
```

###### http 包建立 Web 服务器

```go
package main

import (
    "fmt"
    "net/http"
    "strings"
    "log"
)

func sayhelloName(w http.ResponseWriter, r *http.Request) {
    r.ParseForm()  // 解析参数，默认是不会解析的
    fmt.Println(r.Form)  // 这些信息是输出到服务器端的打印信息
    fmt.Println("path", r.URL.Path)
    fmt.Println("scheme", r.URL.Scheme)
    fmt.Println(r.Form["url_long"])
    for k, v := range r.Form {
        fmt.Println("key:", k)
        fmt.Println("val:", strings.Join(v, ""))
    }
    fmt.Fprintf(w, "Hello astaxie!") // 这个写入到 w 的是输出到客户端的
}

func main() {
    http.HandleFunc("/", sayhelloName) // 设置访问的路由
    err := http.ListenAndServe(":9090", nil) // 设置监听的端口
    if err != nil {
        log.Fatal("ListenAndServe: ", err)
    }
}
```

上面这个代码，我们 build 之后，然后执行 web.exe, 这个时候其实已经在 9090 端口监听 http 链接请求了。

在浏览器输入 http://localhost:9090

可以看到浏览器页面输出了 Hello astaxie!



###### web 工作方式的几个概念

以下均是服务器端的几个概念

`Request`：用户请求的信息，用来解析用户的请求信息，包括 post、get、cookie、url 等信息

`Response`：服务器需要反馈给客户端的信息

`Conn`：用户的每次请求链接

`Handler`：处理请求和生成返回信息的处理逻辑

###### 分析 http 包运行机制

如下图所示，是 Go 实现 Web 服务的工作模式的流程图

![img](https://img.herrluk.icu/typoraPicture/3.3.http-3662827.png)

图 3.9 http 包执行流程

1. 创建 **Listen Socket**, 监听指定的端口，等待客户端请求到来。

2. **Listen Socket** 接受客户端的请求，得到 **Client Socket**, 接下来通过 **Client Socket** 与客户端通信。
3. 处理客户端的请求，首先从 **Client Socket** 读取 HTTP 请求的协议头，如果是 POST 方法，还可能要读取客户端提交的数据，然后交给相应的 **handler** 处理请求，**handler** 处理完毕准备好客户端需要的数据，通过 **Client Socket** 写给客户端。

这整个的过程里面我们只要了解清楚下面三个问题，也就知道 Go 是如何让 Web 运行起来了

- 如何监听端口？

- 如何接收客户端请求？
- 如何分配 handler？

前面小节的代码里面我们可以看到，Go 是通过一个函数 `ListenAndServe` 来处理这些事情的。这个底层其实这样处理的：

初始化一个 server 对象，然后调用了 `net.Listen("tcp", addr)`，也就是底层用 TCP 协议搭建了一个服务，然后监控我们设置的端口。

下面代码来自 Go 的 http 包的源码，通过下面的代码我们可以看到整个的 http 处理过程：

```go
func (srv *Server) Serve(l net.Listener) error {
    defer l.Close()
    var tempDelay time.Duration // how long to sleep on accept failure
    for {
        rw, e := l.Accept()
        if e != nil {
            if ne, ok := e.(net.Error); ok && ne.Temporary() {
                if tempDelay == 0 {
                    tempDelay = 5 * time.Millisecond
                } else {
                    tempDelay *= 2
                }
                if max := 1 * time.Second; tempDelay > max {
                    tempDelay = max
                }
                log.Printf("http: Accept error: %v; retrying in %v", e, tempDelay)
                time.Sleep(tempDelay)
                continue
            }
            return e
        }
        tempDelay = 0
        c, err := srv.newConn(rw)
        if err != nil {
            continue
        }
        go c.serve()
    }
}
```

监控之后如何接收客户端的请求呢？

上面代码执行监控端口之后，调用了 `srv.Serve(net.Listener) `函数，这个函数就是处理接收客户端的请求信息。这个函数里面起了一个 `for{}`，首先通过` Listener `接收请求，其次创建一个` Conn`，最后单独开了一个` goroutine`，把这个请求的数据当做参数扔给这个` conn `去服务：`go c.serve()`。这个就是高并发体现了，用户的每一次请求都是在一个新的 goroutine 去服务，相互不影响。

那么如何具体分配到相应的函数来处理请求呢？

conn 首先会解析` request:c.readRequest()`, 然后获取相应的 handler:`handler := c.server.Handler`，也就是我们刚才在调用函数` ListenAndServe `时候的第二个参数，我们前面例子传递的是` nil`，也就是为空，那么默认获取 `handler = DefaultServeMux`, 那么这个变量用来做什么的呢？

对，这个变量就是一个路由器，它用来匹配` url `跳转到其相应的` handle `函数，那么这个我们有设置过吗？

有，我们调用的代码里面第一句不是调用了` http.HandleFunc("/", sayhelloName)` 嘛。这个作用就是注册了请求` / `的路由规则，当请求 uri 为` "/"`，路由就会转到函数 `sayhelloName`，`DefaultServeMux `会调用` ServeHTTP `方法，这个方法内部其实就是调用 `sayhelloName `本身，最后通过写入` response `的信息反馈到客户端。

详细的整个流程如下图所示：

![img](https://img.herrluk.icu/typoraPicture/3.3.illustrator-3662827.png)

#### 2.2 使用默认的多路复用器（DefaultServeMux） 

**1）使用处理器函数处理请求**

```go
//创建处理器函数
func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "hello World!", r.URL.Path)
}

func main() {
	http.HandleFunc("/", handler)
    //创建路由
	http.ListenAndServe("localhost:8080", nil)
}
```



**处理器函数的实现原理**：

###### type HandlerFunc

```
type HandlerFunc func(ResponseWriter, *Request)
```

`HandlerFunc type`是一个适配器，通过类型转换让我们可以将普通的函数作为HTTP处理器使用。如果`f`是一个具有适当签名的函数（如下自己写的handler函数，适当签名是指变量类型相同），`HandlerFunc(f)`通过调用f实现了Handler接口。

*`http.HandleFunc` 里传参的 `/` 意味着 **任意路径**。*

##### http.Request

`http.Request` 是用户的请求信息，一般用 `r` 作为简写。

一些常见的操作如：

- `r.URL.Query()` 获取用户参数
- 获取客户端信息 `r.Header.Get("User-Agent")`

##### http.ResponseWriter

`http.ResponseWriter` 是返回用户的响应，一般用 `w` 作为简写。

常见操作如：

- 返回 500 状态码 `w.WriteHeader(http.StatusInternalServerError)`
- 设置返回标头 `w.Header().Set("name", "my name is smallsoup")`

###### func ListenAndServe

```
func ListenAndServe(addr string, handler Handler) error
```

`ListenAndServe`监听TCP地址`addr`，并且会使用`handler`参数调用`Serve`函数处理接收到的连接。`handler`参数一般会设为nil，此时会使用`DefaultServeMux`。

一个简单的服务端例子：

```go
package main
import (
	"io"
	"net/http"
	"log"
)
// hello world, the web server
func HelloServer(w http.ResponseWriter, req *http.Request) {
	io.WriteString(w, "hello, world!\n")
}
func main() {
	http.HandleFunc("/hello", HelloServer)
	err := http.ListenAndServe(":12345", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```

**2） 使用处理器处理请求**

```go
package main

import (
	"fmt"
	"net/http"
)

type MyHandler struct{}

func (h *MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "正在通过处理器处理你的请求")
}
func main() {
	myHandler := MyHandler{}
	//调用处理器
	http.Handle("/", &myHandler)
	http.ListenAndServe(":8080", nil)
}
```

###### type Handler

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

实现了Handler接口的对象可以注册到HTTP服务端，为特定的路径及其子树提供服务。**即一个处理器**。

`ServeHTTP`应该将回复的头域和数据写入`ResponseWriter`接口然后返回。返回标志着该请求已经结束，HTTP服务端可以转移向该连接上的下一个请求。

也就是说**只要某个结构体实现了 Handler 接口中的 ServeHTTP 方法那么它就是一个处理器**。

###### func Handle

```
func Handle(pattern string, handler Handler)
```

Handle注册HTTP处理器handler和对应的模式pattern（注册到DefaultServeMux）。如果该模式已经注册有一个处理器，Handle会panic。ServeMux的文档解释了模式的匹配机制。



**我们还可以通过 Server 结构对服务器进行更详细的配置**

###### type [Server](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##1581)

```go
type Server struct {
    Addr           string        // 监听的TCP地址，如果为空字符串会使用":http"
    Handler        Handler       // 调用的处理器，如为nil会调用http.DefaultServeMux
    ReadTimeout    time.Duration // 请求的读取操作在超时前的最大持续时间
    WriteTimeout   time.Duration // 回复的写入操作在超时前的最大持续时间
    MaxHeaderBytes int           // 请求的头域最大长度，如为0则用DefaultMaxHeaderBytes
    TLSConfig      *tls.Config   // 可选的TLS配置，用于ListenAndServeTLS方法
    // TLSNextProto（可选地）指定一个函数来在一个NPN型协议升级出现时接管TLS连接的所有权。
    // 映射的键为商谈的协议名；映射的值为函数，该函数的Handler参数应处理HTTP请求，
    // 并且初始化Handler.ServeHTTP的*Request参数的TLS和RemoteAddr字段（如果未设置）。
    // 连接在函数返回时会自动关闭。
    TLSNextProto map[string]func(*Server, *tls.Conn, Handler)
    // ConnState字段指定一个可选的回调函数，该函数会在一个与客户端的连接改变状态时被调用。
    // 参见ConnState类型和相关常数获取细节。
    ConnState func(net.Conn, ConnState)
    // ErrorLog指定一个可选的日志记录器，用于记录接收连接时的错误和处理器不正常的行为。
    // 如果本字段为nil，日志会通过log包的标准日志记录器写入os.Stderr。
    ErrorLog *log.Logger
    // 内含隐藏或非导出字段
}
```

Server类型定义了运行HTTP服务端的参数。Server的零值是合法的配置。



```go
package main

import (
	"fmt"
	"net/http"
	"time"
)

type MyHandler struct{}

func (h *MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "测试通过Server结构详细配置服务器")
}

func main() {
	myHandler := MyHandler{}
	//调用处理器
	server := http.Server{
		Addr:              ":8080",
		Handler:           &myHandler,
		ReadHeaderTimeout: 2 * time.Second,
	}
	server.ListenAndServe()
}

```

#### 2.3 使用自己创建的多路复用器

1.在创建服务器时，我们还可以通过 **NewServeMux** 方法创建一个多路复用器

net/http 标准库提供了一个默认的多路复用器，这个多路复用器可以通过调用`NewServeMux` 函数来创建：`mux := http.NewServeMux()` 
为了将发送至根 URL 的请求重定向到处理器，程序使用了 `HandleFunc` 函数：
`mux.HandleFunc ("/" , index)` 。`HandleFunc` 函数接受一个 URL 和一个处理器的名字作为参数，并将针对给定 URL的请求转发至指定的处理器进行处理 因此对上述调用来说，当有针对根 URL 的请求到达时，该请求就会被重定向到名为 index 的处理器函数。

此外，因为所有处理器都接收一个 ResponseWriter 和一个指 Request 结构的指针作为参数，并且所有请求参数都可以通过访问 Request 结构得到，所以程序并不需要向处理器显式地传入任何请求参数

```go
package main

import (
	"fmt"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "通过自己创建的多路复用器俩处理请求")
}

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/myMux", handler)
	http.ListenAndServe(":8080", mux)
}
```



###### type [ServeMux](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##1391)

```
type ServeMux struct {
    // 内含隐藏或非导出字段
}
```

ServeMux类型是**HTTP请求的多路转接器**。它会将每一个接收的请求的URL与一个注册模式的列表进行匹配，并调用和URL最匹配的模式的处理器。

模式是固定的、由根开始的路径，如"/favicon.ico"，或由根开始的子树，如"/images/"（注意结尾的斜杠）。较长的模式优先于较短的模式，因此如果模式"/images/"和"/images/thumbnails/"都注册了处理器，后一个处理器会用于路径以"/images/thumbnails/"开始的请求，前一个处理器会接收到其余的路径在"/images/"子树下的请求。

注意，因为以斜杠结尾的模式代表一个由根开始的子树，模式"/"会匹配所有的未被其他注册的模式匹配的路径，而不仅仅是路径"/"。

模式也能（可选地）以主机名开始，表示只匹配该主机上的路径。指定主机的模式优先于一般的模式，因此一个注册了两个模式"/codesearch"和"codesearch.google.com/"的处理器不会接管目标为"http://www.google.com/"的请求。

ServeMux还会注意到请求的URL路径的无害化，将任何路径中包含"."或".."元素的请求重定向到等价的没有这两种元素的URL。（参见path.Clean函数）

#####  func [NewServeMux](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##1404)

```
func NewServeMux() *ServeMux
```

NewServeMux创建并返回一个新的*ServeMux

##### func (*ServeMux) [Handle](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##1516)

```
func (mux *ServeMux) Handle(pattern string, handler Handler)
```

Handle注册HTTP处理器handler和对应的模式pattern。如果该模式已经注册有一个处理器，Handle会panic。

Example

##### func (*ServeMux) [HandleFunc](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##1554)

```
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request))
```

HandleFunc注册一个处理器函数handler和对应的模式pattern。

#####  func (*ServeMux) [Handler](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##1468)

```
func (mux *ServeMux) Handler(r *Request) (h Handler, pattern string)
```

Handler根据r.Method、r.Host和r.URL.Path等数据，返回将用于处理该请求的HTTP处理器。它总是返回一个非nil的处理器。如果路径不是它的规范格式，将返回内建的用于重定向到等价的规范路径的处理器。

Handler也会返回匹配该请求的的已注册模式；在内建重定向处理器的情况下，pattern会在重定向后进行匹配。如果没有已注册模式可以应用于该请求，本方法将返回一个内建的"404 page not found"处理器和一个空字符串模式。

#####  func (*ServeMux) [ServeHTTP](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##1502)

```
func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request)
```

ServeHTTP将请求派遣到与请求的URL最匹配的模式对应的处理器。

#### 2.4 Go 的 http 包详解

前面小节介绍了 Go 怎么样实现了 Web 工作模式的一个流程，这一小节，我们将详细地解剖一下 http 包，看它到底是怎样实现整个过程的。

Go 的 http 有两个核心功能：Conn、ServeMux

###### Conn 的 goroutine

与我们一般编写的 http 服务器不同，**Go 为了实现高并发和高性能，使用goroutines来处理 Conn 的读写事件，这样每个请求都能保持独立，相互不会阻塞，可以高效的响应网络事件**。这是 Go 高效的保证。

Go 在等待客户端请求里面是这样写的：

```go
c, err := srv.newConn(rw)
if err != nil {
    continue
}
go c.serve()
```

这里我们可以看到客户端的每次请求都会创建一个 Conn，这个 Conn 里面保存了该次请求的信息，然后再传递到对应的 handler，该 handler 中便可以读取到相应的 header 信息，这样保证了每个请求的独立性。

###### ServeMux 的自定义

我们前面小节讲述` conn.server `的时候，其实内部是调用了 http 包默认的路由器，通过路由器把本次请求的信息传递到了后端的处理函数。那么这个路由器是怎么实现的呢？

它的结构如下：*mux*是multiplexor 多路复用器的缩写

```go
type ServeMux struct {
    mu sync.RWMutex   // 锁，由于请求涉及到并发处理，因此这里需要一个锁机制
    m  map[string]muxEntry  // 路由规则，一个 string 对应一个 mux 实体，这里的 string 就是注册的路由表达式
    hosts bool // 是否在任意的规则中带有 host 信息
}
```

下面看一下 `muxEntry`

```go
type muxEntry struct {
    explicit bool   // 是否精确匹配
    h        Handler // 这个路由表达式对应哪个 handler
    pattern  string  // 匹配字符串
}
```

接着看一下` Handler `的定义

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)  // 路由实现器
}
```

`Handler`是一个接口，但是前一小节中的` sayhelloName `函数并没有实现` ServeHTTP `这个接口，为什么能添加呢？原来在 http 包里面还定义了一个类型` HandlerFunc`, 我们定义的函数 sayhelloName 就是这个` HandlerFunc `调用之后的结果，这个类型默认就实现了` ServeHTTP `这个接口，即我们调用了` HandlerFunc (f)`, **强制类型转换 f 成为 HandlerFunc 类型，这样 f 就拥有了 ServeHTTP 方法**。

```go
type HandlerFunc func(ResponseWriter, *Request)
// ServeHTTP calls f(w, r).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
    f(w, r)
}
```

路由器里面存储好了相应的路由规则之后，那么具体的请求又是怎么分发的呢？请看下面的代码，默认的路由器实现了 `ServeHTTP`：

```
func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request) {
    if r.RequestURI == "*" {
        w.Header().Set("Connection", "close")
        w.WriteHeader(StatusBadRequest)
        return
    }
    h, _ := mux.Handler(r)
    h.ServeHTTP(w, r)
}
```

如上所示路由器接收到请求之后，如果是` * `那么关闭链接，不然调用`mux.Handler(r)`返回对应设置路由的处理` Handler`，然后执行` h.ServeHTTP(w, r)`也就是调用对应路由的 handler 的 ServerHTTP 接口，那么` mux.Handler (r) `怎么处理的呢？

```go
func (mux *ServeMux) Handler(r *Request) (h Handler, pattern string) {
    if r.Method != "CONNECT" {
        if p := cleanPath(r.URL.Path); p != r.URL.Path {
            _, pattern = mux.handler(r.Host, p)
            return RedirectHandler(p, StatusMovedPermanently), pattern
        }
    }   
    return mux.handler(r.Host, r.URL.Path)
}

func (mux *ServeMux) handler(host, path string) (h Handler, pattern string) {
    mux.mu.RLock()
    defer mux.mu.RUnlock()

    // Host-specific pattern takes precedence over generic ones
    if mux.hosts {
        h, pattern = mux.match(host + path)
    }
    if h == nil {
        h, pattern = mux.match(path)
    }
    if h == nil {
        h, pattern = NotFoundHandler(), ""
    }
    return
}
```

原来他是根据用户请求的 URL 和路由器里面存储的 map 去匹配的，当匹配到之后返回存储的 handler，调用这个 handler 的 ServeHTTP 接口就可以执行到相应的函数了。

通过上面这个介绍，我们了解了整个路由过程，**Go 其实支持外部实现的路由器 ListenAndServe 的第二个参数就是用以配置外部路由器的，它是一个 Handler 接口，即外部路由器只要实现了 Handler 接口就可以**，我们可以在自己实现的路由器的 ServeHTTP 里面实现自定义路由功能。

如下代码所示，我们自己实现了一个简易的路由器：

```go
package main

import (
    "fmt"
    "net/http"
)

type MyMux struct {
}

func (p *MyMux) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    if r.URL.Path == "/" {
        sayhelloName(w, r)
        return
    }
    http.NotFound(w, r)
    return
}

func sayhelloName(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello myroute!")
}

func main() {
    mux := &MyMux{}
    http.ListenAndServe(":9090", mux)
}
```



**Go 代码的执行流程**

通过对 http 包的分析之后，现在让我们来梳理一下整个的代码执行过程。

首先调用` Http.HandleFunc`，按顺序做了几件事：

1. 调用了 DefaultServeMux 的 HandleFunc
2. 调用了 DefaultServeMux 的 Handle
3. 往 DefaultServeMux 的 map [string] muxEntry 中增加对应的 handler 和路由规则

其次调用` http.ListenAndServe (":9090", nil)`，按顺序做了几件事情：

1. 实例化 Server

2. 调用 Server 的` ListenAndServe ()`

3. 调用` net.Listen ("tcp", addr) `监听端口

4. 启动一个 for 循环，在循环体中 Accept 请求

5. 对每个请求实例化一个 Conn，并且开启一个 goroutine 为这个请求进行服务 go c.serve ()

6. 读取每个请求的内容` w, err := c.readRequest ()`

7. 判断 handler 是否为空，如果没有设置 handler（这个例子就没有设置 handler）handler 就设置为` DefaultServeMux`

8. 调用 handler 的 ServeHttp

9. 在这个例子中，下面就进入到 `DefaultServeMux.ServeHttp`

10. 根据 request 选择 handler，并且进入到这个 handler 的 ServeHTTP:

    `mux.handler(r).ServeHTTP(w, r)`

11. 选择 handler：

- A 判断是否有路由能满足这个 request（循环遍历 ServeMux 的 muxEntry）

- B 如果有路由满足，调用这个路由 handler 的 ServeHTTP

- C 如果没有路由满足，调用 NotFoundHandler 的 ServeHTTP




  ## 第 3 章：HTTP 协议 

  HTTP 协议是 Web 工作的核心，所以要了解清楚 Web 的工作方式就需要详细的了解清楚 HTTP 是怎么样工作的。

  #### 3.1 HTTP 协议简介

  HTTP **超文本传输协议** (HTTP-Hypertext transfer protocol)，是一个属于应用层的

  面向对象的协议，由于其简捷、快速的方式，适用于分布式超媒体信息系统。它于 1990

  年提出，经过几年的使用与发展，得到不断地完善和扩展。**它是一种详细规定了浏览器**

  **和万维网服务器之间互相通信的规则**，通过因特网传送万维网文档的数据传送协议。

  客户端与服务端通信时传输的内容我们称之为**报文**

  HTTP 就是一个通信规则，这个规则规定了客户端发送给服务器的报文格式，也规

  定了服务器发送给客户端的报文格式。实际我们要学习的就是这两种报文。客户端发送

  给服务器的称为”**请求报文**“，服务器发送给客户端的称为”**响应报文**“。

  HTTP 协议是无状态的，同一个客户端的这次请求和上次请求是没有对应关系，对 HTTP 服务器来说，它并不知道这两个请求是否来自同一个客户端。为了解决这个问题， Web 程序引入了 Cookie 机制来维护连接的可持续状态。

  #### HTTP 请求包（浏览器信息）

  我们先来看看 Request 包的结构，Request 包分为 3 部分，第一部分叫 `Request line（请求行）`, 第二部分叫` Request header（请求头）`, 第三部分是` body（主体）`。header 和 body 之间有个空行，请求包的例子所示:

  ```go
GET /domains/example/ HTTP/1.1      // 请求行: 请求方法 请求 URI HTTP 协议/协议版本
Host：www.iana.org               // 服务端的主机名
User-Agent：Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.4 (KHTML, like Gecko) Chrome/22.0.1229.94 Safari/537.4          // 浏览器信息
Accept：text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8  // 客户端能接收的 mine
Accept-Encoding：gzip,deflate,sdch       // 是否支持流压缩
Accept-Charset：UTF-8,*;q=0.5        // 客户端字符编码集
// 空行,用于分割请求头和消息体
// 消息体,请求资源参数,例如 POST 传递的参数
  ```

  HTTP 协议定义了很多与服务器交互的请求方法，最基本的有 4 种，分别是 `GET`, `POST`, `PUT`, `DELETE`。一个 URL 地址用于描述一个网络上的资源，而 HTTP 中的 GET, POST, PUT, DELETE 就对应着对这个资源的查，增，改，删 4 个操作。我们最常见的就是 GET 和 POST 了。GET 一般用于获取或查询资源信息，而 POST 一般用于更新资源信息。

  通过 fiddler 抓包可以看到如下请求信息:

  ![img](https://img.herrluk.icu/typoraPicture/3.1.http-3662827.png)

  图 3.4 fiddler 抓取的 GET 信息

  ![img](https://img.herrluk.icu/typoraPicture/3.1.httpPOST-3662827.png)

  图 3.5 fiddler 抓取的 POST 信息

  **我们看看 GET 和 POST 的区别**:

    1. 可以看到 **GET 请求消息体为空**，**POST 请求带有消息体**。

    2. **GET 提交的数据会放在 URL 之后，以` ? `分割 URL 和传输数据，参数之间以` & `相连**，如` EditPosts.aspx?name=test1&id=123456`。**POST 方法是把提交的数据放在 HTTP 包的 body 中**。
    3. GET 提交的数据大小有限制（因为浏览器对 URL 的长度有限制），而 **POST 方法提交的数据没有限制**。
    4. **GET 方式提交数据，会带来安全问题，比如一个登录页面，通过 GET 方式提交数据时，用户名和密码将出现在 URL 上**，如果页面可以被缓存或者其他人可以访问这台机器，就可以从历史记录获得该用户的账号和密码。

  ###### HTTP 响应包（服务器信息）

  我们再来看看 HTTP 的 response 包，他的结构如下：

  ```
HTTP/1.1 200 OK                     // 状态行
Server: nginx/1.0.8                 // 服务器使用的 WEB 软件名及版本
Date: Tue, 30 Oct 2012 04:14:25 GMT     // 发送时间
Content-Type: text/html             // 服务器发送信息的类型
Transfer-Encoding: chunked          // 表示发送 HTTP 包是分段发的
Connection: keep-alive              // 保持连接状态
Content-Length: 90                  // 主体内容长度
// 空行 用来分割消息头和主体
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"... // 消息体
  ```

  Response 包中的第一行叫做状态行，由 HTTP 协议版本号， 状态码， 状态消息三部分组成。


  状态码用来告诉 HTTP 客户端，HTTP 服务器是否产生了预期的 Response。HTTP/1.1 协议中定义了 5 类状态码， 状态码由三位数字组成，第一个数字定义了响应的类别

  - 1XX 提示信息 - 表示请求已被成功接收，继续处理
  - 2XX 成功 - 表示请求已被成功接收，理解，接受
  - 3XX 重定向 - 要完成请求必须进行更进一步的处理
  - 4XX 客户端错误 - 请求有语法错误或请求无法实现
  - 5XX 服务器端错误 - 服务器未能实现合法的请求

  我们看下面这个图展示了详细的返回信息，左边可以看到有很多的资源返回码，200 是常用的，表示正常信息，302 表示跳转。response header 里面展示了详细的信息。

  ![img](https://img.herrluk.icu/typoraPicture/3.1.response-3662827.png)

  图 3.6 访问一次网站的全部请求信息

  **HTTP** 协议是无状态的和 Connection: keep-alive 的区别

  **无状态是指协议对于事务处理没有记忆能力，服务器不知道客户端是什么状态。从另一方面讲，打开一个服务器上的网页和你之前打开这个服务器上的网页之间没有任何联系**。

  HTTP 是一个无状态的面向连接的协议，无状态不代表 HTTP 不能保持 TCP 连接，更不能代表 HTTP 使用的是 UDP 协议（面对无连接）。

  **从 HTTP/1.1 起，默认都开启了 Keep-Alive 保持连接特性，简单地说，当一个网页打开完成后，客户端和服务器之间用于传输 HTTP 数据的 TCP 连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的 TCP 连接**。

  Keep-Alive 不会永久保持连接，它有一个保持时间，可以在不同服务器软件（如 Apache）中设置这个时间。

  

  **请求实例**

  ![img](https://img.herrluk.icu/typoraPicture/3.1.web-3662827.png)


  图 3.7 一次请求的 request 和 response

  上面这张图我们可以了解到整个的通讯过程，同时细心的读者是否注意到了一点，**一个 URL 请求但是左边栏里面为什么会有那么多的资源请求** (这些都是静态文件，go 对于静态文件有专门的处理方式)。

  **这个就是浏览器的一个功能，第一次请求 url，服务器端返回的是 html 页面，然后浏览器开始渲染 HTML：当解析到 HTML DOM 里面的图片连接，css 脚本和 js 脚本的链接，浏览器就会自动发起一个请求静态资源的 HTTP 请求，获取相对应的静态资源，然后浏览器就会渲染出来，最终将所有资源整合、渲染，完整展现在我们面前的屏幕上**。

  网页优化方面有一项措施是减少 HTTP 请求次数，就是把尽量多的 css 和 js 资源合并在一起，目的是尽量减少网页请求静态资源的次数，提高网页加载速度，同时减缓服务器的压力。

  ## 第4章：表单

  表单是一个包含表单元素的区域。表单元素是允许用户在表单中（比如：文本域、下拉列表、单选框、复选框等等）输入信息的元素。表单使用表单标签（\<form>）定义。

  ```
<form>
...
input 元素
...
</form>
  ```

  Go 里面对于 form 处理已经有很方便的方法了，在 Request 里面的有专门的 form 处理，可以很方便的整合到 Web 开发里面来，4.1 小节里面将讲解 Go 如何处理表单的输入。由于不能信任任何用户的输入，所以我们需要对这些输入进行有效性验证，4.2 小节将就如何进行一些普通的验证进行详细的演示。

  HTTP 协议是一种无状态的协议，那么如何才能辨别是否是同一个用户呢？同时又如何保证一个表单不出现多次递交的情况呢？4.3 和 4.4 小节里面将对 cookie (cookie 是存储在客户端的信息，能够每次通过 header 和服务器进行交互的数据) 等进行详细讲解。

  表单还有一个很大的功能就是能够上传文件，那么 Go 是如何处理文件上传的呢？针对大文件上传我们如何有效的处理呢？4.5 小节我们将一起学习 Go 处理文件上传的知识。

  #### 4.1处理表单的输入

  先来看一个表单递交的例子，我们有如下的表单内容，命名成文件` login.gtpl`

  ```
<html>
<head>
<title></title>
</head>
<body>
<form action="/login" method="post">
    用户名:<input type="text" name="username">
    密码:<input type="password" name="password">
    <input type="submit" value="登录">
</form>
</body>
</html>
  ```

  上面递交表单到服务器的 /login，当用户输入信息点击登录之后，会跳转到服务器的路由 login 里面，我们首先要判断这个是什么方式传递过来，POST 还是 GET 呢？

  http 包里面有一个很简单的方式就可以获取，我们在前面 web 的例子的基础上来看看怎么处理 login 页面的 form 数据

  ```go
package main

import (
    "fmt"
    "html/template"
    "log"
    "net/http"
    "strings"
)

func sayhelloName(w http.ResponseWriter, r *http.Request) {
    r.ParseForm()       // 解析 url 传递的参数，对于 POST 则解析响应包的主体（request body）
    // 注意:如果没有调用 ParseForm 方法，下面无法获取表单的数据
    fmt.Println(r.Form) // 这些信息是输出到服务器端的打印信息
    fmt.Println("path", r.URL.Path)
    fmt.Println("scheme", r.URL.Scheme)
    fmt.Println(r.Form["url_long"])
    for k, v := range r.Form {
        fmt.Println("key:", k)
        fmt.Println("val:", strings.Join(v, ""))
    }
    fmt.Fprintf(w, "Hello astaxie!") // 这个写入到 w 的是输出到客户端的
}

func login(w http.ResponseWriter, r *http.Request) {
    fmt.Println("method:", r.Method) // 获取请求的方法
    if r.Method == "GET" {
        t, _ := template.ParseFiles("login.gtpl")
        log.Println(t.Execute(w, nil))
    } else {
        err := r.ParseForm()   // 解析 url 传递的参数，对于 POST 则解析响应包的主体（request body）
        if err != nil {
           // handle error http.Error() for example
          log.Fatal("ParseForm: ", err)
        }
        // 请求的是登录数据，那么执行登录的逻辑判断
        fmt.Println("username:", r.Form["username"])
        fmt.Println("password:", r.Form["password"])
    }
}

func main() {
    http.HandleFunc("/", sayhelloName)       // 设置访问的路由
    http.HandleFunc("/login", login)         // 设置访问的路由
    err := http.ListenAndServe(":9090", nil) // 设置监听的端口
    if err != nil {
        log.Fatal("ListenAndServe: ", err)
    }
}
  ```

  通过上面的代码我们可以看出获取请求方法是通过 r.Method 来完成的，这是个字符串类型的变量，返回 GET, POST, PUT 等 method 信息。

  login 函数中我们根据 r.Method 来判断是显示登录界面还是处理登录逻辑。当 GET 方式请求时显示登录界面，其他方式请求时则处理登录逻辑，如查询数据库、验证登录信息等。

  当我们在浏览器里面打开 http://127.0.0.1:9090/login 的时候，出现如下界面

  ![img](https://img.herrluk.icu/typoraPicture/4.1.login-3662827.png)

  如果你看到一个空页面，可能是你写的 login.gtpl 文件中有错误，请根据控制台中的日志进行修复。

  图 4.1 用户登录界面

  我们输入用户名和密码之后发现在服务器端是不会打印出来任何输出的，为什么呢？默认情况下，Handler 里面是不会自动解析 form 的，必须显式的调用 `r.ParseForm() `后，你才能对这个表单数据进行操作。我们修改一下代码，在 fmt.Println("username:", r.Form["username"]) 之前加一行 r.ParseForm(), 重新编译，再次测试输入递交，现在是不是在服务器端有输出你的输入的用户名和密码了。

  r.Form 里面包含了所有请求的参数，比如 URL 中 query-string、POST 的数据、PUT 的数据，所以当你在 URL 中的 query-string 字段和 POST 冲突时，会保存成一个 slice，里面存储了多个值，Go 官方文档中说在接下来的版本里面将会把 POST、GET 这些数据分离开来。

  现在我们修改一下 login.gtpl 里面 form 的 action 值 http://127.0.0.1:9090/login 修改为 http://127.0.0.1:9090/login?username=astaxie，再次测试，服务器的输出 username 是不是一个 slice。服务器端的输出如下：

  ![img](https://img.herrluk.icu/typoraPicture/4.1.slice-3662827.png)

  图 4.2 服务器端打印接受到的信息

  `request.Form `是一个` url.Values `类型，里面存储的是对应的类似` key=value `的信息，下面展示了可以对 form 数据进行的一些操作:

  ```
v := url.Values{}
v.Set("name", "Ava")
v.Add("friend", "Jess")
v.Add("friend", "Sarah")
v.Add("friend", "Zoe")
// v.Encode() == "name=Ava&friend=Jess&friend=Sarah&friend=Zoe"
fmt.Println(v.Get("name"))
fmt.Println(v.Get("friend"))
fmt.Println(v["friend"])
  ```

  Tips:

  Request 本身也提供了 FormValue () 函数来获取用户提交的参数。

  如`r.Form["username"] `也可写成` r.FormValue ("username")`。调用 `r.FormValue` 时会自动调用 `r.ParseForm`，所以不必提前调用。`r.FormValue` 只会返回同名参数中的第一个，若参数不存在则返回空字符串。

  #### 4.2 验证表单的输入

  开发 Web 的一个原则就是，不能信任用户输入的任何信息，所以验证和过滤用户的输入信息就变得非常重要，我们经常会在微博、新闻中听到某某网站被入侵了，存在什么漏洞，这些大多是因为网站对于用户输入的信息没有做严格的验证引起的，所以为了编写出安全可靠的 Web 程序，验证表单输入的意义重大。

  我们平常编写 Web 应用主要有两方面的数据验证，一个是在页面端的 js 验证 (目前在这方面有很多的插件库，比如 ValidationJS 插件)，一个是在服务器端的验证，我们这小节讲解的是如何在服务器端验证。

  ###### 必填字段

  你想要确保从一个表单元素中得到一个值，例如前面小节里面的用户名，我们如何处理呢？Go 有一个内置函数 len 可以获取字符串的长度，这样我们就可以通过 len 来获取数据的长度，例如：

  ```go
if len(r.Form["username"][0])==0{
    // 为空的处理
}
  ```


  r.Form 对不同类型的表单元素的留空有不同的处理， 对于空文本框、空文本区域以及文件上传，元素的值为空值，而如果是未选中的复选框和单选按钮，则根本不会在 r.Form 中产生相应条目，如果我们用上面例子中的方式去获取数据时程序就会报错。所以我们需要通过 r.Form.Get() 来获取值，因为如果字段不存在，通过该方式获取的是空值。但是通过 r.Form.Get() 只能获取单个的值，如果是 map 的值，必须通过上面的方式来获取。

  ###### 数字

  你想要确保一个表单输入框中获取的只能是数字，例如，你想通过表单获取某个人的具体年龄是 50 岁还是 10 岁，而不是像 “一把年纪了” 或 “年轻着呢” 这种描述

  如果我们是判断正整数，那么我们先转化成 int 类型，然后进行处理

  ```
getint,err:=strconv.Atoi(r.Form.Get("age"))
if err!=nil{
    // 数字转化出错了，那么可能就不是数字
}

// 接下来就可以判断这个数字的大小范围了
if getint >100 {
    // 太大了
}
  ```

  还有一种方式就是正则匹配的方式

  ```
if m, _ := regexp.MatchString("^[0-9]+$", r.Form.Get("age")); !m {
    return false
}
  ```


  对于性能要求很高的用户来说，这是一个老生常谈的问题了，他们认为应该尽量避免使用正则表达式，因为使用正则表达式的速度会比较慢。但是在目前机器性能那么强劲的情况下，对于这种简单的正则表达式效率和类型转换函数是没有什么差别的。如果你对正则表达式很熟悉，而且你在其它语言中也在使用它，那么在 Go 里面使用正则表达式将是一个便利的方式。

  Go 实现的正则是 RE2，所有的字符都是 UTF-8 编码的。

  ###### 中文

  有时候我们想通过表单元素获取一个用户的中文名字，但是又为了保证获取的是正确的中文，我们需要进行验证，而不是用户随便的一些输入。对于中文我们目前有两种方式来验证，可以使用` unicode `包提供的` func Is(rangeTab *RangeTable, r rune) bool `来验证，也可以使用正则方式来验证，这里使用最简单的正则方式，如下代码所示

  ```
if m, _ := regexp.MatchString("^\\p{Han}+$", r.Form.Get("realname")); !m {
    return false
}
  ```

  ###### 英文

  我们期望通过表单元素获取一个英文值，例如我们想知道一个用户的英文名，应该是 astaxie，而不是 asta 谢。

  我们可以很简单的通过正则验证数据：

  ```
if m, _ := regexp.MatchString("^[a-zA-Z]+$", r.Form.Get("engname")); !m {
    return false
}
  ```

  ###### 电子邮件地址

  你想知道用户输入的一个 Email 地址是否正确，通过如下这个方式可以验证：

  ```
if m, _ := regexp.MatchString(`^([\w\.\_]{2,10})@(\w{1,})\.([a-z]{2,4})$`, r.Form.Get("email")); !m {
    fmt.Println("no")
}else{
    fmt.Println("yes")
}
  ```

  ###### 手机号码

  你想要判断用户输入的手机号码是否正确，通过正则也可以验证：

  ```
if m, _ := regexp.MatchString(`^(1[3|4|5|8][0-9]\d{4,8})$`, r.Form.Get("mobile")); !m {
    return false
}
  ```

  ###### 下拉菜单

  如果我们想要判断表单里面` <select> `元素生成的下拉菜单中是否有被选中的项目。有些时候黑客可能会伪造这个下拉菜单不存在的值发送给你，那么如何判断这个值是否是我们预设的值呢？

  我们的 select 可能是这样的一些元素

  ```
<select name="fruit">
<option value="apple">apple</option>
<option value="pear">pear</option>
<option value="banana">banana</option>
</select>
  ```

  那么我们可以这样来验证

  ```
slice:=[]string{"apple","pear","banana"}

v := r.Form.Get("fruit")
for _, item := range slice {
    if item == v {
        return true
    }
}

return false
  ```

  ###### 单选按钮

  如果我们想要判断 radio 按钮是否有一个被选中了，我们页面的输出可能就是一个男、女性别的选择，但是也可能一个 15 岁大的无聊小孩，一手拿着 http 协议的书，另一只手通过 telnet 客户端向你的程序在发送请求呢，你设定的性别男值是 1，女是 2，他给你发送一个 3，你的程序会出现异常吗？因此我们也需要像下拉菜单的判断方式类似，判断我们获取的值是我们预设的值，而不是额外的值。

  ```
<input type="radio" name="gender" value="1">男
<input type="radio" name="gender" value="2">女
  ```


  那我们也可以类似下拉菜单的做法一样

  ```
slice:=[]string{"1","2"}

for _, v := range slice {
    if v == r.Form.Get("gender") {
        return true
    }
}
return false
  ```

  ###### 复选框

  有一项选择兴趣的复选框，你想确定用户选中的和你提供给用户选择的是同一个类型的数据。

  ```
<input type="checkbox" name="interest" value="football">足球
<input type="checkbox" name="interest" value="basketball">篮球
<input type="checkbox" name="interest" value="tennis">网球
  ```


  对于复选框我们的验证和单选有点不一样，因为接收到的数据是一个 slice

  ```
slice:=[]string{"football","basketball","tennis"}
a:=Slice_diff(r.Form["interest"],slice)
if a == nil{
    return true
}

return false
  ```

  上面这个函数` Slice_diff `包含在我开源的一个库里面 (操作 slice 和 map 的库)，`github.com/astaxie/beeku`

  ###### 日期和时间

  你想确定用户填写的日期或时间是否有效。例如，用户在日程表中安排 8 月份的第 45 天开会，或者提供未来的某个时间作为生日。

  Go 里面提供了一个 time 的处理包，我们可以把用户的输入年月日转化成相应的时间，然后进行逻辑判断

  ```
t := time.Date(2009, time.November, 10, 23, 0, 0, 0, time.Local)
fmt.Printf("Go launched at %s\n", t.Local())
  ```


  获取 time 之后我们就可以进行很多时间函数的操作。具体的判断就根据自己的需求调整。

  ###### 身份证号码

  如果我们想验证表单输入的是否是身份证，通过正则也可以方便的验证，但是身份证有 15 位和 18 位，我们两个都需要验证

  ```
// 验证 15 位身份证，15 位的是全部数字
if m, _ := regexp.MatchString(`^(\d{15})$`, r.Form.Get("usercard")); !m {
    return false
}

// 验证 18 位身份证，18 位前 17 位为数字，最后一位是校验位，可能为数字或字符 X。
if m, _ := regexp.MatchString(`^(\d{17})([0-9]|X)$`, r.Form.Get("usercard")); !m {
    return false
}
  ```

  上面列出了我们一些常用的服务器端的表单元素验证，希望通过这个引导入门，能够让你对 Go 的数据验证有所了解，特别是 Go 里面的正则处理。

  #### 4.3. 预防跨站脚本

  现在的网站包含大量的动态内容以提高用户体验，比过去要复杂得多。**所谓动态内容，就是根据用户环境和需要，Web 应用程序能够输出相应的内容**。动态站点会受到一种**名为 “跨站脚本攻击”（Cross Site Scripting, 安全专家们通常将其缩写成 XSS）的威胁，而静态站点则完全不受其影响**。

  攻击者通常会在有漏洞的程序中插入 JavaScript、VBScript、 ActiveX 或 Flash 以欺骗用户。一旦得手，他们可以盗取用户帐户信息，修改用户设置，盗取 / 污染 cookie 和植入恶意广告等。

  对 XSS 最佳的防护应该结合以下两种方法：一是验证所有输入数据，有效检测攻击 (这个我们前面小节已经有过介绍); 另一个是对所有输出数据进行适当的处理，以防止任何已成功注入的脚本在浏览器端运行。

  那么 Go 里面是怎么做这个有效防护的呢？Go 的` html/template `里面带有下面几个函数可以帮你转义

  ```go
func HTMLEscape (w io.Writer, b [] byte) // 把 b 进行转义之后写到 w
func HTMLEscapeString (s string) string // 转义 s 之后返回结果字符串
func HTMLEscaper (args ...interface {}) string // 支持多个参数一起转义，返回结果字符串
  ```

  我们看 4.1 小节的例子

  ```go
fmt.Println("username:",template.HTMLEscapeString(r.Form.Get("username"))) // 输出到服务器端
fmt.Println("password:",template.HTMLEscapeString(r.Form.Get("password")))
template.HTMLEscape(w, []byte(r.Form.Get("username"))) // 输出到客户端
  ```


  如果我们输入的 username 是` <script>alert()</script>`, 那么我们可以在浏览器上面看到输出如下所示：

  ![img](https://img.herrluk.icu/typoraPicture/4.3.escape-3662827.png)

  图 4.3 Javascript 过滤之后的输出

  Go 的 html/template 包默认帮你过滤了 html 标签，但是有时候你只想要输出这个 `<script>alert()</script> `看起来正常的信息，该怎么处理？请使用 text/template。请看下面的例子：

  ```go
import "text/template"
...
t, err := template.New("foo").Parse(`{{define "T"}}Hello, {{.}}!{{end}}`)
err = t.ExecuteTemplate(out, "T", "<script>alert('you have been pwned')</script>")
  ```


  输出

  ```
Hello, <script>alert('you have been pwned')</script>!
  ```

  或者使用` template.HTML `类型

  ```
import "html/template"
...
t, err := template.New("foo").Parse(`{{define "T"}}Hello, {{.}}!{{end}}`)
err = t.ExecuteTemplate(out, "T", template.HTML("<script>alert('you have been pwned')</script>"))
  ```

  输出

  `Hello, <script>alert('you have been pwned')</script>!`
  转换成 template.HTML 后，变量的内容也不会被转义

  转义的例子：

  ```
import "html/template"
...
t, err := template.New("foo").Parse(`{{define "T"}}Hello, {{.}}!{{end}}`)
err = t.ExecuteTemplate(out, "T", "<script>alert('you have been pwned')</script>")
  ```


  转义之后的输出：

  `Hello, <script>alert('you have been pwned')</script>!`

  #### 4.4. 防止多次递交表单

  不知道你是否曾经看到过一个论坛或者博客，在一个帖子或者文章后面出现多条重复的记录，这些大多数是因为用户重复递交了留言的表单引起的。由于种种原因，用户经常会重复递交表单。通常这只是鼠标的误操作，如双击了递交按钮，也可能是为了编辑或者再次核对填写过的信息，点击了浏览器的后退按钮，然后又再次点击了递交按钮而不是浏览器的前进按钮。当然，也可能是故意的 —— 比如，在某项在线调查或者博彩活动中重复投票。那我们如何有效的防止用户多次递交相同的表单呢？

  **解决方案是在表单中添加一个带有唯一值的隐藏字段**。在验证表单时，先检查带有该唯一值的表单是否已经递交过了。如果是，拒绝再次递交；如果不是，则处理表单进行逻辑处理。另外，如果是采用了 Ajax 模式递交表单的话，当表单递交后，通过 javascript 来禁用表单的递交按钮。

  我继续拿 4.2 小节的例子优化：

  ```
<input type="checkbox" name="interest" value="football">足球
<input type="checkbox" name="interest" value="basketball">篮球
<input type="checkbox" name="interest" value="tennis">网球    
用户名:<input type="text" name="username">
密码:<input type="password" name="password">
<input type="hidden" name="token" value="{{.}}">
<input type="submit" value="登录">
  ```

  **我们在模版里面增加了一个隐藏字段 token，这个值我们通过 MD5 (时间戳) 来获取唯一值，然后我们把这个值存储到服务器端 (session 来控制，我们将在第六章讲解如何保存)，以方便表单提交时比对判定**。

  ```go
 func login(w http.ResponseWriter, r *http.Request) {
	fmt.Println("method:", r.Method) // 获取请求的方法
	if r.Method == "GET" {
		crutime := time.Now().Unix()
		h := md5.New()
		io.WriteString(h, strconv.FormatInt(crutime, 10))
		token := fmt.Sprintf("%x", h.Sum(nil))
		t, _ := template.ParseFiles("login.gtpl")
		t.Execute(w, token)
	} else {
		// 请求的是登录数据，那么执行登录的逻辑判断
		r.ParseForm()
		token := r.Form.Get("token")
		if token != "" {
			// 验证 token 的合法性
		} else {
			// 不存在 token 报错
		}
		fmt.Println("username length:", len(r.Form["username"][0]))
		fmt.Println("username:", template.HTMLEscapeString(r.Form.Get("username"))) // 输出到服务器端
		fmt.Println("password:", template.HTMLEscapeString(r.Form.Get("password")))
		template.HTMLEscape(w, []byte(r.Form.Get("username"))) // 输出到客户端
	}
}  
  ```

  上面的代码输出到页面的源码如下：

  ![img](https://img.herrluk.icu/typoraPicture/4.4.token-3662827.png)

  图 4.4 增加 token 之后在客户端输出的源码信息

  我们看到 token 已经有输出值，你可以不断的刷新，可以看到这个值在不断的变化。这样就保证了每次显示 form 表单的时候都是唯一的，用户递交的表单保持了唯一性。

  我们的解决方案可以防止非恶意的攻击，并能使恶意用户暂时不知所措，然后，它却不能排除所有的欺骗性的动机，对此类情况还需要更复杂的工作。

  #### 4.5. 处理文件上传

  你想处理一个由用户上传的文件，比如你正在建设一个类似 Instagram 的网站，你需要存储用户拍摄的照片。这种需求该如何实现呢？

  要使表单能够上传文件，首先第一步就是要添加 form 的 enctype 属性，enctype 属性有如下三种情况:

  ```
application/x-www-form-urlencoded   表示在发送前编码所有字符（默认）
multipart/form-data   不对字符编码。在使用包含文件上传控件的表单时，必须使用该值。
text/plain    空格转换为 "+" 加号，但不对特殊字符编码。
  ```


  所以，创建新的表单 html 文件，命名为 upload.gtpl, html 代码应该类似于:

  ```
<html>

<head>
    <title>上传文件</title>
</head>

<body>

<form enctype="multipart/form-data" action="/upload" method="post">
  <input type="file" name="uploadfile" />
  <input type="hidden" name="token" value="{{.}}"/>
  <input type="submit" value="upload" />
</form>

</body>
</html>
  ```

  在服务器端，我们增加一个 handlerFunc:

  

  ```go
http.HandleFunc("/upload", upload)

// 处理 /upload  逻辑
func upload(w http.ResponseWriter, r *http.Request) {
	fmt.Println("method:", r.Method) // 获取请求的方法
	if r.Method == "GET" {
		crutime := time.Now().Unix()
		h := md5.New()
		io.WriteString(h, strconv.FormatInt(crutime, 10))
		token := fmt.Sprintf("%x", h.Sum(nil))
		t, _ := template.ParseFiles("upload.gtpl")
		t.Execute(w, token)
	} else {
		r.ParseMultipartForm(32 << 20)
		file, handler, err := r.FormFile("uploadfile")
		if err != nil {
			fmt.Println(err)
			return
		}
		defer file.Close()
		fmt.Fprintf(w, "%v", handler.Header)
		f, err := os.OpenFile("./test/"+handler.Filename, os.O_WRONLY|os.O_CREATE, 0666) // 此处假设当前目录下已存在test目录
		if err != nil {
			fmt.Println(err)
			return
		}
		defer f.Close()
		io.Copy(f, file)
	}
}
  ```

  

  通过上面的代码可以看到，处理文件上传我们需要调用` r.ParseMultipartForm`，里面的参数表示` maxMemory`，调用` ParseMultipartForm `之后，上传的文件存储在 `maxMemory `大小的内存里面，如果文件大小超过了 maxMemory，那么剩下的部分将存储在系统的临时文件中。我们可以通过` r.FormFile `获取上面的文件句柄，然后实例中使用了` io.Copy` 来存储文件。

  获取其他非文件字段信息的时候就不需要调用` r.ParseForm`，因为在需要的时候 Go 自动会去调用。而且` ParseMultipartForm `调用一次之后，后面再次调用不会再有效果。

  通过上面的实例我们可以看到我们上传文件主要三步处理：

    1. 表单中增加`enctype="multipart/form-data"`

    2. 服务端调用` r.ParseMultipartForm`, 把上传的文件存储在内存和临时文件中
    3. 使用` r.FormFile `获取文件句柄，然后对文件进行存储等处理。

  文件 handler 是 `multipart.FileHeader`, 里面存储了如下结构信息

  ```
type FileHeader struct {
    Filename string
    Header   textproto.MIMEHeader
    // contains filtered or unexported fields
}
  ```


  我们通过上面的实例代码打印出来上传文件的信息如下

  ![img](https://img.herrluk.icu/typoraPicture/4.5.upload2-3662827.png)

  图 4.5 打印文件上传后服务器端接受的信息

  ###### 客户端上传文件

  我们上面的例子演示了如何通过表单上传文件，然后在服务器端处理文件，其实 Go 支持模拟客户端表单功能支持文件上传，详细用法请看如下示例：

  

      package main
      
      import (
          "bytes"
          "fmt"
          "io"
          "io/ioutil"
          "mime/multipart"
          "net/http"
          "os"
      )
      
      func postFile(filename string, targetUrl string) error {
          bodyBuf := &bytes.Buffer{}
          bodyWriter := multipart.NewWriter(bodyBuf)
      // 关键的一步操作
      fileWriter, err := bodyWriter.CreateFormFile("uploadfile", filename)
      if err != nil {
          fmt.Println("error writing to buffer")
          return err
      }
      
      // 打开文件句柄操作
      fh, err := os.Open(filename)
      if err != nil {
          fmt.Println("error opening file")
          return err
      }
      defer fh.Close()
      
      // iocopy
      _, err = io.Copy(fileWriter, fh)
      if err != nil {
          return err
      }
      
      contentType := bodyWriter.FormDataContentType()
      bodyWriter.Close()
      
      resp, err := http.Post(targetUrl, contentType, bodyBuf)
      if err != nil {
          return err
      }
      defer resp.Body.Close()
      resp_body, err := ioutil.ReadAll(resp.Body)
      if err != nil {
          return err
      }
      fmt.Println(resp.Status)
      fmt.Println(string(resp_body))
      return nil
      }
      
      // sample usage
      func main() {
          target_url := "http://localhost:9090/upload"
          filename := "./astaxie.pdf"
          postFile(filename, target_url)
      }

  上面的例子详细展示了客户端如何向服务器上传一个文件的例子，客户端通过 `multipart.Write` 把文件的文本流写入一个缓存中，然后调用 http 的 Post 方法把缓存传到服务器。

  如果你还有其他普通字段例如 username 之类的需要同时写入，那么可以调用` multipart `的 `WriteField `方法写很多其他类似的字段。

  ## 第 5 章：操作数据库 

  Go 语言中的 **database/sql** 包定义了对数据库的一系列操作。database/sql/driver

  包定义了应被数据库驱动实现的接口，这些接口会被 sql 包使用。但是 Go 语言没有提

  供任何官方的数据库驱动，所以我们需要导入**第三方**的数据库驱动。不过我们连接数据

  库之后对数据库操作的大部分代码都使用 sql 包。

------

  连接mysql遇到`panic: sql: unknown driver “mysql” (forgotten import?)`错误，需要在调用mysql的main函数所在的文件夹下导入如下包执行init函数。

  ```
_ "github.com/go-sql-driver/mysql"
  ```

  再来说一下为什么要在该包前加入下划线_,因为**加入下划线表示只执行该库的 init 函数而不对其它导出对象进行真正地导入**。因为 Go 语言的数据库驱动都会在 init 函数中注册自己，所以我们只需要加入下划线导入即可。

------

  #### 5.1. database/sql 接口

  ###### sql.Register

  这个存在于` database/sql `的函数是用来注册数据库驱动的，当第三方开发者开发数据库驱动时，都会实现 init 函数，在 init 里面会调用这个` Register(name string, driver driver.Driver) `完成本驱动的注册。

  我们来看一下 mymysql、sqlite3 的驱动里面都是怎么调用的：

  ```go
// https://github.com/mattn/go-sqlite3 驱动
func init() {
    sql.Register("sqlite3", &SQLiteDriver{})
}

// https://github.com/mikespook/mymysql 驱动
// Driver automatically registered in database/sql
var d = Driver{proto: "tcp", raddr: "127.0.0.1:3306"}
func init() {
    Register("SET NAMES utf8")
    sql.Register("mymysql", &d)
}
  ```

  我们看到第三方数据库驱动都是通过调用这个函数来注册自己的数据库驱动名称以及相应的 driver 实现。在 database/sql 内部通过一个 map 来存储用户定义的相应驱动。

  ```go
var drivers = make(map[string]driver.Driver)

drivers[name] = driver
  ```

  因此通过 database/sql 的注册函数可以同时注册多个数据库驱动，只要不重复。

  在我们使用 database/sql 接口和第三方库的时候经常看到如下:

  ```go
  import (
      "database/sql"
      _ "github.com/mattn/go-sqlite3"
  )
  ```

  新手都会被这个 _ 所迷惑，其实这个就是 Go 设计的巧妙之处，我们在变量赋值的时候经常看到这个符号，它是用来忽略变量赋值的占位符，**那么包引入用到这个符号也是相似的作用，这儿使用 _ 的意思是引入后面的包名而不直接使用这个包中定义的函数，变量等资源**。

  我们在 2.3 节流程和函数一节中介绍过 init 函数的初始化过程，**包在引入的时候会自动调用包的 init 函数以完成对包的初始化。因此，我们引入上面的数据库驱动包之后会自动去调用 init 函数，然后在 init 函数里面注册这个数据库驱动，这样我们就可以在接下来的代码中直接使用这个数据库驱动了**。

  ###### driver.Driver

  Driver 是一个数据库驱动的接口，他定义了一个 method：` Open (name string)`，这个方法返回一个数据库的` Conn `接口。

  ```
type Driver interface {
    Open(name string) (Conn, error)
}
  ```

  

  **返回的 Conn 只能用来进行一次 goroutine 的操作，也就是说不能把这个 Conn 应用于 Go 的多个 goroutine 里面**。如下代码会出现错误

  ```
...
go goroutineA (Conn)  // 执行查询操作
go goroutineB (Conn)  // 执行插入操作
...
  ```

  上面这样的代码可能会使 Go 不知道某个操作究竟是由哪个 goroutine 发起的，从而导致数据混乱，比如可能会把 goroutineA 里面执行的查询操作的结果返回给 goroutineB 从而使 B 错误地把此结果当成自己执行的插入数据。

  第三方驱动都会定义这个函数，它会解析 name 参数来获取相关数据库的连接信息，解析完成后，它将使用此信息来初始化一个 Conn 并返回它。

  ###### driver.Conn

  Conn 是一个数据库连接的接口定义，他定义了一系列方法，这个 Conn 只能应用在一个 goroutine 里面，不能使用在多个 goroutine 里面，详情请参考上面的说明。

  ```go
type Conn interface {
    Prepare(query string) (Stmt, error)
    Close() error
    Begin() (Tx, error)
}
  ```

  `Prepare `函数返回与当前连接相关的执行 Sql 语句的准备状态，可以进行查询、删除等操作。

  `Close `函数关闭当前的连接，执行释放连接拥有的资源等清理工作。因为驱动实现了 database/sql 里面建议的` conn pool`，所以你不用再去实现缓存 conn 之类的，这样会容易引起问题。

  `Begin `函数返回一个代表事务处理的` Tx`，通过它你可以进行查询，更新等操作，或者对事务进行回滚、递交。

  ###### driver.Stmt

  Stmt 是**一种准备好的状态**，和 Conn 相关联，而且只能应用于一个 goroutine 中，不能应用于多个 goroutine。

  ```go
type Stmt interface {
    Close() error
    NumInput() int
    Exec(args []Value) (Result, error)
    Query(args []Value) (Rows, error)
}
  ```

  `Close `函数关闭当前的链接状态，但是如果当前正在执行 query，query 还是有效返回 rows 数据。

  `NumInput `函数返回当前预留参数的个数，当返回 >=0 时数据库驱动就会智能检查调用者的参数。当数据库驱动包不知道预留参数的时候，返回 -1。

  `Exec `函数执行 Prepare 准备好的 sql，传入参数执行 update/insert 等操作，返回 Result 数据

  `Query `函数执行 Prepare 准备好的 sql，传入需要的参数执行 select 操作，返回 Rows 结果集

  ###### driver.Tx

  事务处理一般就两个过程，递交或者回滚。数据库驱动里面也只需要实现这两个函数就可以

  ```
type Tx interface {
    Commit() error
    Rollback() error
}
  ```


  这两个函数**一个用来递交一个事务，一个用来回滚事务**。

  ###### driver.Execer

  这是一个 Conn 可选择实现的接口

  ```
type Execer interface {
    Exec(query string, args []Value) (Result, error)
}
  ```


  如果这个接口没有定义，那么在调用 `DB.Exec`, 就会首先调用 Prepare 返回 Stmt，然后执行 Stmt 的 Exec，然后关闭 Stmt。

  ###### driver.Result

  这个是执行 `Update/Insert` 等操作返回的结果接口定义

  ```
type Result interface {
    LastInsertId() (int64, error)
    RowsAffected() (int64, error)
}
  ```

  `LastInsertId `函数返回由数据库执行插入操作得到的自增 ID 号。

  `RowsAffected `函数返回 query 操作影响的数据条目数。

  ###### driver.Rows

  Rows 是执行查询返回的结果集接口定义

  ```go
type Rows interface {
    Columns() []string
    Close() error
    Next(dest []Value) error
}
  ```

  `Columns `函数返回查询数据库表的字段信息，这个返回的 slice 和 sql 查询的字段一一对应，而不是返回整个表的所有字段。

  `Close `函数用来关闭 Rows 迭代器。

  `Next `函数用来返回下一条数据，把数据赋值给 dest。dest 里面的元素必须是 driver.Value 的值除了 string，返回的数据里面所有的 string 都必须要转换成 [] byte。如果最后没数据了，Next 函数最后返回 io.EOF。

  ###### driver.RowsAffected

  `RowsAffected `其实就是一个 int64 的别名，但是他实现了 Result 接口，用来底层实现 Result 的表示方式

  ```
type RowsAffected int64

func (RowsAffected) LastInsertId() (int64, error)

func (v RowsAffected) RowsAffected() (int64, error)
  ```

  ###### driver.Value

  **`Value` 其实就是一个空接口，他可以容纳任何的数据**

  ```
type Value interface{}
  ```

  **drive 的 Value 是驱动必须能够操作的 Value，Value 要么是 nil，要么是下面的任意一种**:

  ```
int64
float64
bool
[]byte
string   [*]除了Rows.Next 返回的不能是 string.
time.Time
  ```

  ###### driver.ValueConverter

  `ValueConverter `接口定义了**如何把一个普通的值转化成 driver.Value 的接口**

  ```go
type ValueConverter interface {
    ConvertValue(v interface{}) (Value, error)
}
  ```


  在开发的数据库驱动包里面实现这个接口的函数在很多地方会使用到，这个 ValueConverter 有很多好处：

  - 转化 driver.value 到数据库表相应的字段，例如 int64 的数据如何转化成数据库表 uint16字段

  - 把数据库查询结果转化成 driver.Value 值
  - 在 scan 函数里面如何把 driver.Value 值转化成用户定义的值

  ###### driver.Valuer

  Valuer 接口定义了返回一个 driver.Value 的方式

  ```
type Valuer interface {
    Value() (Value, error)
}
  ```


  很多类型都实现了这个 Value 方法，用来自身与 driver.Value 的转化。

  通过上面的讲解，你应该对于驱动的开发有了一个基本的了解，一个驱动只要实现了这些接口就能完成增删查改等基本操作了，剩下的就是与相应的数据库进行数据交互等细节问题了，在此不再赘述。

  ###### database/sql

  database/sql 在 database/sql/driver 提供的接口基础上定义了一些更高阶的方法，用以简化数据库操作，同时内部还建议性地实现一个 conn pool。

  ```
type DB struct {
    driver   driver.Driver
    dsn      string
    mu       sync.Mutex // protects freeConn and closed
    freeConn []driver.Conn
    closed   bool
}
  ```


  我们可以看到 Open 函数返回的是 DB 对象，里面有一个 freeConn，它就是那个简易的连接池。它的实现相当简单或者说简陋，就是当执行` db.prepare -> db.prepareDC `的时候会` defer dc.releaseConn`，然后调用` db.putConn`，也就是把这个连接放入连接池，每次调用` db.conn `的时候会先判断` freeConn `的长度是否大于 0，大于 0 说明有可以复用的 conn，直接拿出来用就是了，如果不大于 0，则创建一个 conn，然后再返回之。

  #### 5.2. 使用 MySQL 数据库

  目前 Internet 上流行的网站构架方式是 LAMP，其中的 M 即 MySQL, 作为数据库，MySQL 以免费、开源、使用方便为优势成为了很多 Web 开发的后端数据库存储引擎。

  ###### MySQL 驱动

  Go 中支持 MySQL 的驱动目前比较多，有如下几种，有些是支持 database/sql 标准，而有些是采用了自己的实现接口，常用的有如下几种:

  - github.com/go-sql-driver/mysql 支持 database/sql，全部采用 go 写。
  - github.com/ziutek/mymysql 支持 database/sql，也支持自定义的接口，全部采用 go 写。
  - github.com/Philio/GoMySQL 不支持 database/sql，自定义接口，全部采用 go 写。

  接下来的例子我主要以第一个驱动为例 (我目前项目中也是采用它来驱动)，也推荐大家采用它，主要理由：

  - 这个驱动比较新，维护的比较好
  - 完全支持 database/sql 接口
  - 支持 keepalive，保持长连接，虽然 mymysql 也支持 keepalive，但不是线程安全的，这个从底层就支持了 keepalive。

  ###### 示例代码

  接下来的几个小节里面我们都将采用同一个数据库表结构：数据库` test`，用户表 `userinfo`，关联用户信息表 `userdetail`。

  

  ```
CREATE TABLE `userinfo` (
    `uid` INT(10) NOT NULL AUTO_INCREMENT,
    `username` VARCHAR(64) NULL DEFAULT NULL,
    `department` VARCHAR(64) NULL DEFAULT NULL,
    `created` DATE NULL DEFAULT NULL,
    PRIMARY KEY (`uid`)
);

CREATE TABLE `userdetail` (
    `uid` INT(10) NOT NULL DEFAULT '0',
    `intro` TEXT NULL,
    `profile` TEXT NULL,
    PRIMARY KEY (`uid`)
)
  ```

  如下示例将示范如何使用 database/sql 接口对数据库表进行增删改查操作

  ```
package main

import (
    "database/sql"
    "fmt"
    // "time"

    _ "github.com/go-sql-driver/mysql"
)

func main() {
    db, err := sql.Open("mysql", "astaxie:astaxie@/test?charset=utf8")
    checkErr(err)

    // 插入数据
    stmt, err := db.Prepare("INSERT userinfo SET username=?,department=?,created=?")
    checkErr(err)

    res, err := stmt.Exec("astaxie", "研发部门", "2012-12-09")
    checkErr(err)

    id, err := res.LastInsertId()
    checkErr(err)

    fmt.Println(id)
    // 更新数据
    stmt, err = db.Prepare("update userinfo set username=? where uid=?")
    checkErr(err)

    res, err = stmt.Exec("astaxieupdate", id)
    checkErr(err)

    affect, err := res.RowsAffected()
    checkErr(err)

    fmt.Println(affect)

    // 查询数据
    rows, err := db.Query("SELECT * FROM userinfo")
    checkErr(err)

    for rows.Next() {
        var uid int
        var username string
        var department string
        var created string
        err = rows.Scan(&uid, &username, &department, &created)
        checkErr(err)
        fmt.Println(uid)
        fmt.Println(username)
        fmt.Println(department)
        fmt.Println(created)
    }

    // 删除数据
    stmt, err = db.Prepare("delete from userinfo where uid=?")
    checkErr(err)

    res, err = stmt.Exec(id)
    checkErr(err)

    affect, err = res.RowsAffected()
    checkErr(err)

    fmt.Println(affect)

    db.Close()

}

func checkErr(err error) {
    if err != nil {
        panic(err)
    }
}
  ```

  通过上面的代码我们可以看出，Go 操作 Mysql 数据库是很方便的。

  关键的几个函数我解释一下：

  sql.Open () 函数用来打开一个注册过的数据库驱动，go-sql-driver 中注册了 mysql 这个数据库驱动，第二个参数是 DSN (Data Source Name)，它是 go-sql-driver 定义的一些数据库链接和配置信息。它支持如下格式：

  ```
user@unix(/path/to/socket)/dbname?charset=utf8
user:password@tcp(localhost:5555)/dbname?charset=utf8
user:password@/dbname
user:password@tcp([de:ad:be:ef::ca:fe]:80)/dbname
  ```

  db.Prepare () 函数用来返回准备要执行的 sql 操作，然后返回准备完毕的执行状态。

  db.Query () 函数用来直接执行 Sql 返回 Rows 结果。

  stmt.Exec () 函数用来执行 stmt 准备好的 SQL 语句

  我们可以看到我们传入的参数都是 =? 对应的数据，这样做的方式可以一定程度上防止 SQL 注入。

  #### 5.6. NOSQL 数据库操作 

  NoSQL (Not Only SQL)，指的是非关系型的数据库。随着 Web 2.0 的兴起，传统的关系数据库在应付 Web 2.0 网站，特别是超大规模和高并发的 SNS 类型的 Web 2.0 纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。

  而 Go 语言作为 21 世纪的 C 语言，对 NOSQL 的支持也是很好，目前流行的 NOSQL 主要有 redis、mongoDB、Cassandra 和 Membase 等。这些数据库都有高性能、高并发读写等特点，目前已经广泛应用于各种应用中。我接下来主要讲解一下 redis 和 mongoDB 的操作。

  redis
  redis 是一个 key-value 存储系统。和 Memcached 类似，它支持存储的 value 类型相对更多，包括 string (字符串)、list (链表)、set (集合 ) 和 zset (有序集合)。

  目前应用 redis 最广泛的应该是新浪微博平台，其次还有 Facebook 收购的图片社交网站 instagram。以及其他一些有名的 互联网企业

  Go 目前支持 redis 的驱动有如下

  - github.com/gomodule/redigo (推荐)

  - github.com/go-redis/redis
  - github.com/hoisie/redis
  - github.com/alphazero/Go-Redis
  - github.com/simonz05/godis

  我以 redigo 驱动为例来演示如何进行数据的操作:

  ```
package main

import (
    "fmt"
    "os"
    "os/signal"
    "syscall"
    "time"

    "github.com/gomodule/redigo/redis"
)

var (
    Pool *redis.Pool
)

func init() {
    redisHost := ":6379"
    Pool = newPool(redisHost)
    close()
}

func newPool(server string) *redis.Pool {

    return &redis.Pool{

        MaxIdle:     3,
        IdleTimeout: 240 * time.Second,

        Dial: func() (redis.Conn, error) {
            c, err := redis.Dial("tcp", server)
            if err != nil {
                return nil, err
            }
            return c, err
        },

        TestOnBorrow: func(c redis.Conn, t time.Time) error {
            _, err := c.Do("PING")
            return err
        },
    }
}

func close() {
    c := make(chan os.Signal, 1)
    signal.Notify(c, os.Interrupt)
    signal.Notify(c, syscall.SIGTERM)
    signal.Notify(c, syscall.SIGKILL)
    go func() {
        <-c
        Pool.Close()
        os.Exit(0)
    }()
}

func Get(key string) ([]byte, error) {

    conn := Pool.Get()
    defer conn.Close()

    var data []byte
    data, err := redis.Bytes(conn.Do("GET", key))
    if err != nil {
        return data, fmt.Errorf("error get key %s: %v", key, err)
    }
    return data, err
}

func main() {
    test, err := Get("test")
    fmt.Println(test, err)
}
  ```

  

  #### 5.1 获取数据库连接

  1.创建一个 db.go 文件，导入 `database/sql` 包以及第三方驱动包

  ```
import (
	"database/sql"
	"goWeb/pkg/github.com/go-sql-driver/mysql"
)
  ```

  2.定义两个全局变量

  ```
var (
	Db  *sql.DB
	err error
)
  ```

  ###### type [DB](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##196) [¶](https://studygolang.com/static/pkgdoc/pkg/database_sql.htm##pkg-index)

  ```
type DB struct {
    // 内含隐藏或非导出字段
}
  ```

  DB是一个数据库（操作）句柄，代表一个具有零到多个底层连接的连接池。它可以安全的被多个go程同时使用。

  sql包会自动创建和释放连接；它也会维护一个闲置连接的连接池。如果数据库具有单连接状态的概念，该状态只有在事务中被观察时才可信。一旦调用了BD.Begin，返回的Tx会绑定到单个连接。当调用事务Tx的Commit或Rollback后，该事务使用的连接会归还到DB的闲置连接池中。连接池的大小可以用SetMaxIdleConns方法控制。

  3.创建 init 函数，在函数体中调用 sql 包的 Open 函数获取连接

  ```go
func init() {
	Db, err = sql.Open("mysql","root:root@tcp(localhost:3306)/test")
	if err != nil {
		panic(err.Error())
	}
}
  ```

  ######## func [Open](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##429)

  ```
func Open(driverName, dataSourceName string) (*DB, error)
  ```

  Open打开一个dirverName指定的数据库，dataSourceName指定数据源，一般包至少括数据库文件名和（可能的）连接信息。

  大多数用户会通过数据库特定的连接帮助函数打开数据库，返回一个*DB。Go标准库中没有数据库驱动。参见http://golang.org/s/sqldrivers获取第三方驱动。

  Open函数可能只是验证其参数，而不创建与数据库的连接。如果要检查数据源的名称是否合法，应调用返回值的Ping方法。

  返回的DB可以安全的被多个go程同时使用，并会维护自身的闲置连接池。这样一来，Open函数只需调用一次。很少需要关闭DB。

   参数 dataSourceName 的格式：（中括号代表可选）

  `数据库用户名:数据库密码@[tcp(localhost:3306)]/数据库名`

  ######## func (*DB) [Ping](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##448)

  ```
func (db *DB) Ping() error
  ```

  Ping检查与数据库的连接是否仍有效，如果需要会创建连接。

  ######## func (*DB) [Exec](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##862)

  ```
func (db *DB) Exec(query string, args ...interface{}) (Result, error)
  ```

  Exec执行一次命令（包括查询、删除、更新、插入等），不返回任何执行结果。参数args表示query中的占位参数。

  ######## func (*DB) [Query](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##911)

  ```
func (db *DB) Query(query string, args ...interface{}) (*Rows, error)
  ```

  Query执行一次查询，返回多行结果（即Rows），一般用于执行select命令。参数args表示query中的占位参数。

  Example

  ```
age := 27
rows, err := db.Query("SELECT name FROM users WHERE age=?", age)
if err != nil {
    log.Fatal(err)
}
defer rows.Close()
for rows.Next() {
    var name string
    if err := rows.Scan(&name); err != nil {
        log.Fatal(err)
    }
    fmt.Printf("%s is %d\n", name, age)
}
if err := rows.Err(); err != nil {
    log.Fatal(err)
}
  ```

  ######## func (*DB) [QueryRow](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##992)

  ```
func (db *DB) QueryRow(query string, args ...interface{}) *Row
  ```

  QueryRow执行一次查询，并期望返回最多一行结果（即Row）。QueryRow总是返回非nil的值，直到返回值的Scan方法被调用时，才会返回被延迟的错误。（如：未找到结果）

  ###### type [Row](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##1625)

  ```
type Row struct {
    // 内含隐藏或非导出字段
}
  ```

  QueryRow方法返回Row，代表单行查询结果。

  ######## func (*Row) [Scan](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##1635)

  ```
func (r *Row) Scan(dest ...interface{}) error
  ```

  Scan将**该行查询结果各列分别保存进dest参数指定的值中**。如果该查询匹配多行，Scan会使用第一行结果并丢弃其余各行。如果没有匹配查询的行，Scan会返回ErrNoRows。

  ######## func (*DB) [Prepare](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##820)

  ```
func (db *DB) Prepare(query string) (*Stmt, error)
  ```

  Prepare创建一个准备好的状态用于之后的查询和命令（即**预处理**）。返回值可以同时执行多个查询和命令。

  ###### type [Stmt](https://github.com/golang/go/blob/master/src/database/sql/sql.go?name=release##1237)

  ```
type Stmt struct {
    // 内含隐藏或非导出字段
}
  ```

  Stmt是准备好的状态。Stmt可以安全的被多个go程同时使用。

  

  

  #### 5.2 单元测试

  ###### 4.3.1 简介 

  顾名思义，单元测试( unit test)，就是一种为验证单元的正确性而设置的自动化测试，一个单元就是程序中的一个模块化部分。一般来说，一个单元通常会与程序中的一个函数或者一个方法相对应，但这并不是必须的。Go 的单元测试需要用到 **testing** 包以及 **go test** 命令，而且对测试文件也有以下要求：

  1.被测试的源文件和测试文件必须**位于同一个包下**

  2.测试文件必须要以**_test.go** 结尾

  虽然 Go 对测试文件_test.go 的前缀没有强制要求，不过一般我们都设置为被测试的文件的文件名，如：要对 user.go 进行测试，那么测试文件的名字我们通常设置为 user_test.go

  3.测试文件中的测试函数为 **TestXxx**(t *testing.T)

  其中 Xxx 的首字母必须是大写的英文字母

  函数参数必须是 test.T 的指针类型（如果是 Benchmark 测试则参数是testing.B 的指针类型）

  **一个问题：测试时必须在命令行，直接运行test文件不行。**

  ###### package testing

  ```
import "testing"
  ```

  testing 提供对 Go 包的自动化测试的支持。通过 `go test` 命令，能够自动执行如下形式的任何函数：

  ```
func TestXxx(*testing.T)
  ```

  其中 Xxx 可以是任何字母数字字符串（但第一个字母不能是 [a-z]），用于识别测试例程。

  在这些函数中，使用 Error, Fail 或相关方法来发出失败信号。

  要编写一个新的测试套件，需要创建一个名称以 _test.go 结尾的文件，该文件包含 `TestXxx` 函数，如上所述。 将该文件放在与被测试的包相同的包中。**该文件将被排除在正常的程序包之外，但在运行 “go test” 命令时将被包含（可以直接运行）**。 有关详细信息，请运行 “go help test” 和 “go help testflag” 了解。

  ```
package main

import (
	"fmt"
	"testing"
)
//如果函数名不是以Test开头，那么函数默认不执行
func TestAAddUser(t *testing.T) {
	fmt.Println("测试添加用户：")
	user := &User{}
	//调用添加用户的方法
	user.AddUser()
	user.AddUser2()
}

  ```

  如果函数名不是以Test开头，那么函数默认不执行。但是我们可以将它设置成一个子测试函数。

  ```go
package main

import (
	"fmt"
	"testing"
)

func TestUser(t *testing.T) {
	t.Run("测试子测试函数：", TestAddUser)
	fmt.Println("开始测试User中的测试方法")
	//通过t.Run()来执行子测试函数

}

func TestAddUser(t *testing.T) {
	fmt.Println("子测试函数执行：")
}
  ```

  

  3.我们还可以通过 **TestMain(m \*testing.M)**函数在测试之前和之后做一些其他的操作

  - 测试文件中有 TestMain 函数时，执行 go test 命令将直接运行 TestMain函数，不直接运行测试函数，只有在 TestMain 函数中执行 **m.Run()**时才会执行测试函数
  - 如果想查看测试的详细过程，可以使用 **go test -v** 命令

  ######## Main

  测试程序有时需要在测试之前或之后进行额外的设置（setup）或拆卸（teardown）。有时, 测试还需要控制在主线程上运行的代码。为了支持这些和其他一些情况, 如果测试文件包含函数:

  ```
func TestMain(m *testing.M)
  ```

  那么生成的测试将调用 TestMain(m)，而不是直接运行测试。TestMain 运行在主 goroutine 中, 可以在调用 m.Run 前后做任何设置和拆卸。应该使用 m.Run 的返回值作为参数调用 os.Exit。在调用 TestMain 时, flag.Parse 并没有被调用。所以，如果 TestMain 依赖于 command-line 标志 (包括 testing 包的标记), 则应该显示的调用 flag.Parse。

  一个简单的 TestMain 的实现：

  ```
func TestMain(m *testing.M) {
	// call flag.Parse() here if TestMain uses flags
    // 如果 TestMain 使用了 flags，这里应该加上 flag.Parse()
	os.Exit(m.Run())
}
  ```

  ## 第 5 章：处理请求 

  Go 语言的 net/http 包提供了一系列用于表示 HTTP 报文的结构，我们可以使用它处理请求和发送相应，其中 Request 结构代表了客户端发送的请求报文，下面让我们看一下 Request 结构体。

  ###### type [Request](https://github.com/golang/go/blob/master/src/net/http/request.go?name=release##76)

  ```go
type Request struct {
	  URL *url.URL
  // Form是解析好的表单数据，包括URL字段的query参数和POST或PUT的表单数据。
    // 本字段只有在调用ParseForm后才有效。在客户端，会忽略请求中的本字段而使用Body替代。
    Form url.Values
}
  ```

  Request类型代表一个服务端接受到的或者客户端发送出去的HTTP请求。

  Request各字段的意义和用途在服务端和客户端是不同的。除了字段本身上方文档，还可参见Request.Write方法和RoundTripper接口的文档。

  #### 获取请求 URL

  Request 结构中的 URL 字段用于表示请求行中包含的 URL，该字段是一个指向`url.URL `结构的指针，让我们来看一下 URL 结构

  ###### type [URL](https://github.com/golang/go/blob/master/src/net/url/url.go?name=release##230)

  ```
type URL struct {
    Scheme   string
    Opaque   string    // 编码后的不透明数据
    User     *Userinfo // 用户名和密码信息
    Host     string    // host或host:port
    Path     string
    RawQuery string // 编码后的查询字符串，没有'?'
    Fragment string // 引用的片段（文档位置），没有'##'
}
  ```

  URL类型代表一个解析后的URL（或者说，一个URL参照）。URL基本格式如下：

  ```
scheme://[userinfo@]host/path[?query][##fragment]
  ```

  scheme后不是冒号加双斜线的URL被解释为如下格式：

  ```
scheme:opaque[?query][##fragment]
  ```

  注意路径字段是以解码后的格式保存的，如/%47%6f%2f会变成/Go/。这导致我们无法确定Path字段中的斜线是来自原始URL还是解码前的%2f。除非一个客户端必须使用其他程序/函数来解析原始URL或者重构原始URL，这个区别并不重要。此时，HTTP服务端可以查询req.RequestURI，而HTTP客户端可以使用`URL{Host: "example.com", Opaque: "//example.com/Go%2f"}`代替`{Host: "example.com", Path: "/Go/"}`。

  

  - Path 字段：获取请求的 URL

    例如：http://localhost:8080/hello?username=admin&password=123456

    通过 `r.URL.Path` 只能得到 **/hello**

    

  - RawQuery 字段：获取请求的 URL 后面?后面的查询字符串

    例如：http://localhost:8080/hello?username=admin&password=123456

    通过 `r.URL.RawQuery` 得到的是 `username=admin&password=123456`

  

  #### 获取请求头中的信息

  通过 Request 结果中的 Header 字段用来获取请求头中的所有信息，Header 字段的类型是 Header 类型，而 Header 类型是一个 `map[string][]string`，string 类型的 key，string 切片类型的值。下面是 Header 类型及它的方法

  ###### type [Header](https://github.com/golang/go/blob/master/src/net/http/header.go?name=release##19)

  ```
type Header map[string][]string
  ```

  Header代表HTTP头域的键值对。

#####    func (Header) [Get](https://github.com/golang/go/blob/master/src/net/http/header.go?name=release##38)

  ```
func (h Header) Get(key string) string
  ```

  Get返回键对应的第一个值，如果键不存在会返回`" "`。如要获取该键对应的值切片，请直接用规范格式的键访问map。

#####  func (Header) [Set](https://github.com/golang/go/blob/master/src/net/http/header.go?name=release##30)

  ```
func (h Header) Set(key, value string)
  ```

  Set添加键值对到h，如键已存在则会用只有新值一个元素的切片取代旧值切片。

#####  func (Header) [Add](https://github.com/golang/go/blob/master/src/net/http/header.go?name=release##23)

  ```
func (h Header) Add(key, value string)
  ```

  Add添加键值对到h，如键已存在则会将新的值附加到旧值切片后面。

#####  func (Header) [Del](https://github.com/golang/go/blob/master/src/net/http/header.go?name=release##51)

  ```
func (h Header) Del(key string)
  ```

  Del删除键值对。

#####  func (Header) [Write](https://github.com/golang/go/blob/master/src/net/http/header.go?name=release##56)

  ```
func (h Header) Write(w io.Writer) error
  ```

  Write以有线格式将头域写入w。

    1. 获取请求头中的所有信息：

  获取请求头中的所有信息：`r.Header`得到的结果如下：

  ```
map[Accept:[text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9] Accept-Encoding:[gzip, deflate, br] Accept-Language:[zh-CN,zh;q=0.9] Cache-Control:[max-age=0] Connection:[keep-alive] Sec-Ch-Ua:[" Not A;Brand";v="99", "Chromium";v="101", "Google Chrome";v="101"] Sec-Ch-Ua-Mobile:[?0] Sec-Ch-Ua-Platform:["Windows"] Sec-Fetch-Dest:[document] Sec-Fetch-Mode:[navigate] Sec-Fetch-Site:[none] Sec-Fetch-User:[?1] Upgrade-Insecure-Requests:[1] User-Agent:[Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36]]
  ```

  

    2. 获取请求头中的某个具体属性的值，如获取 Accept-Encoding 的值

  方式一：`r.Header[“Accept-Encoding”]`得到的是一个字符串切片：`[gzip, deflate, br]`

  方式二：`r.Header.Get(“Accept-Encoding”)`得到的是字符串形式的值，多个值使用逗号分隔  `gzip, deflate, br`

  

  #### 获取请求体中的信息

  请求和响应的主体都是有 Request 结构中的 Body 字段表示，这个字段的类型是`io.ReadCloser` 接口，该接口包含了` Reader `接口和` Closer `接口，Reader 接口拥有` Read`方法，Closer 接口拥有` Close `方法。

  ###### type [ReadCloser](https://github.com/golang/go/blob/master/src/io/io.go?name=release##112) [¶](https://studygolang.com/static/pkgdoc/pkg/io.htm##pkg-index)

  ```
type ReadCloser interface {
    Reader
    Closer
}
  ```

  ReadCloser接口聚合了基本的读取和关闭操作。

  ###### type [Reader](https://github.com/golang/go/blob/master/src/io/io.go?name=release##67)

  ```
type Reader interface {
    Read(p []byte) (n int, err error)
}
  ```

  Reader接口用于包装基本的读取方法。

  ###### type [Closer](https://github.com/golang/go/blob/master/src/io/io.go?name=release##86)

  ```
type Closer interface {
    Close() error
}
  ```

  Closer接口用于包装基本的关闭方法。

  在第一次调用之后再次被调用时，Close方法的的行为是未定义的。某些实现可能会说明他们自己的行为。

  

    1. 由于 GET 请求没有请求体，所以我们需要在 HTML 页面中创建一个 form 表单，通过指定` method="post"`来发送一个 POST 请求

  ```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="http://localhost:8080/hello" method="post">
    用户名：<input type="text" name="username"/><br/>
    密码：<input type="password" name="password"/><br/>
    <input type="submit">
</form>
</body>
</html>
  ```

    2. 服务器处理请求的代码:

  ```go
//获取请求体里内容的长度
	len := r.ContentLength
	//创建byte切片
	body := make([]byte, len)
	//将请求体中的内容读到body中
	r.Body.Read(body)
	//在浏览器中显示请求体中的内容
	fmt.Fprintln(w, "请求体中的内容有：", string(body))
  ```

    3. 在浏览器上显示的结果

  请求体中的内容是： `username=hanzong&password=666666`

  #### 获取请求参数

  下面我们就通过 net/http 库中的 Request 结构的字段以及方法获取**请求 URL 后面**的请求参数以及 **form 表单**中提交的请求参数

  ###### Form 字段 

    1. 类型是 **url.Values** 类型，Form 是解析好的表单数据，包括 URL 字段的 query参数和 POST 或 PUT 的表单数据。

#####  type [Values](https://github.com/golang/go/blob/master/src/net/url/url.go?name=release##482)

  ```
type Values map[string][]string
  ```

  Values将建映射到值的列表。它一般用于查询的参数和表单的属性。不同于http.Header这个字典类型，Values的键是大小写敏感的。

    2. **Form 字段只有在调用 Request 的 ParseForm 方法后才有效。在客户端，会忽略请求中的本字段而使用 Body 替代**

#####  func (*Request) [ParseForm](https://github.com/golang/go/blob/master/src/net/http/request.go?name=release##736)

  ```
func (r *Request) ParseForm() error
  ```

  ParseForm解析URL中的查询字符串，并将解析结果更新到r.Form字段。

  对于POST或PUT请求，ParseForm还会将body当作表单解析，并将结果既更新到r.PostForm也更新到r.Form。解析结果中，POST或PUT请求主体要优先于URL查询字符串（同名变量，主体的值在查询字符串的值前面）。

  如果请求的主体的大小没有被MaxBytesReader函数设定限制，其大小默认限制为开头10MB。

  ParseMultipartForm会自动调用ParseForm。重复调用本方法是无意义的。

    3. 获取 5.3 中的表单中提交的请求参数（username 和 password）:

  - 代码：

    ```go
    func handler(w http.ResponseWriter, r *http.Request) {
    //解析表单
    r.ParseForm()
    //获取请求参数
    fmt.Fprintln(w, "请求参数为：", r.Form) }
    ```

    **注意**：在执行 r.Form 之前一定要调用 ParseForm 方法

  4. 如果对 form 表单做一些修改，在 action 属性的 URL 后面也添加相同的请求参数，如下：

     ```html
     <form action="http://localhost:8080/getBody?username=admin&pwd=123456" method="POST">
         用 户 名 ： <input type="text" name="username" value="hanzong"><br/>
         密码： <input type="password" name="password" value="666666"><br/>
         <input type="submit">
     </form>
     ```

     则执行结果如下：

     ```
     请求参数为：map[username:[hanzong admin] password:[666666] pwd:[123456]]
     ```

     #### 给客户端相应

     前面我们一直说的是如何使用处理器中的 *http.Request 处理用户的请求，下面我们来说一下`http.ResponseWriter` 来给用户响应。

     ###### type [ResponseWriter](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##51)

     ```
     type ResponseWriter interface {
         // Header返回一个Header类型值，该值会被WriteHeader方法发送。
         // 在调用WriteHeader或Write方法后再改变该对象是没有意义的。
         Header() Header
         // WriteHeader该方法发送HTTP回复的头域和状态码。
         // 如果没有被显式调用，第一次调用Write时会触发隐式调用WriteHeader(http.StatusOK)
         // WriterHeader的显式调用主要用于发送错误码。
         WriteHeader(int)
         // Write向连接中写入作为HTTP的一部分回复的数据。
         // 如果被调用时还未调用WriteHeader，本方法会先调用WriteHeader(http.StatusOK)
         // 如果Header中没有"Content-Type"键，
         // 本方法会使用包函数DetectContentType检查数据的前512字节，将返回值作为该键的值。
         Write([]byte) (int, error)
     }
     ```

     ResponseWriter接口被HTTP处理器用于构造HTTP回复。

     1. 给客户端响应一个字符串：

        ```
        func handler(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("你的请求我已经收到"))
        }
        ```

        浏览器结果：

        ```
        你的请求我已经收到
        ```

        响应报文中的内容：

        ```
        HTTP/1.1 200 OK
        Date: Fri, 10 Aug 2018 01:09:27 GMT
        Content-Length: 27
        Content-Type: text/plain; charset=utf-8
        ```

     2. 给客户端响应一个 HTML 界面

        1. 代码

        ```
        func myHandler(w http.ResponseWriter, r *http.Request) {
        	html := `<html>
         <head>
         <title>测试响应内容为网页</title>
         <meta charset="utf-8"/>
         </head>
         <body>
         我是以网页的形式响应过来的！
         </body>
        </html>`
        	w.Write([]byte(html))
        }
        ```

        2. 浏览器中的结果：

        ```
        我是以网页的形式响应过来的！
        ```

        通过在浏览器中右键→查看网页代码发现确实是一个 html 页面

        3. 响应报文中的内容

        ```
        HTTP/1.1 200 OK
        Date: Fri, 10 Aug 2018 01:26:58 GMT
        Content-Length: 194
        Content-Type: text/html; charset=utf-8
        ```

        

  5. 给客户端响应 JSON 格式的数据

     1. 处理器端代码：

     ```
     func handler(w http.ResponseWriter, r *http.Request) {
     	//设置响应头中内容的类型
     	w.Header().Set("Content-Type", "application/json")
     	user := User{
     		ID:       1,
     		Username: "admin",
     		Password: "123456",
     	}
     	//将 user 转换为 json 格式
     	json, _ := json.Marshal(user)
     	w.Write(json)
     }
     ```

     2. 浏览器中的结果

     ```
     {"ID":1,"Username":"admin","Password":"123456"}
     ```

     3. 响应报文中的内容

     ```
     HTTP/1.1 200 OK
     Content-Type: application/json
     Date: Fri, 10 Aug 2018 01:58:02 GMT
     Content-Length: 47
     ```

  6. 让客户端重定向

     1. 处理器端代码

     ```
     func testRedire(w http.ResponseWriter, r *http.Request) {
     	//设置响应头中的Location属性
     	w.Header().Set("Location", "https://www.baidu.com")
     	//设置响应的状态码
     	w.WriteHeader(302)
     }
     
     http.HandleFunc("/testRedirect", testRedire)
     ```

     2. 响应报文中的内容

     ```
     HTTP/1.1 302 Found
     Location: https:www.baidu.com
     Date: Fri, 10 Aug 2018 01:45:04 GMT
     Content-Length: 0
     Content-Type: text/plain; charset=utf-8
     ```

  ## 第 6 章：模板引擎 

  Go 为我们提供了 **text/template** 库和 **html/template** 库这两个模板引擎，模板引擎通过将数据和模板组合在一起生成最终的 HTML，而处理器负责调用模板引擎并将引擎生成的 HTMl 返回给客户端。其中，html/template 是在 text/template 模板库的基础上增加了对 html 输出的安全处理，主要目的是为了防止被攻击。

   

  ### Go 模版引擎使用流程

  - 编写模版代码；
  - 导入包；
  - 加载模版代码；
  - 根据模版参数渲染模版。

  Go 的模板都是文本文档（其中 Web 应用的模板通常都是 HTML），它们都嵌入了一些称为**动作**的指令。从模板引擎的角度来说，模板就是嵌入了动作的文本（这些文本通常包含在模板文件里面），而模板引擎则通过分析并执行这些文本来生成出另外一些文本。

  #### Go模板使用范例

    1. 编写模板代码

  在项目下先创建 views 目录，用于存放模板文件。比如：将下面模版代码保存至 views/demo.tpl 文件中。

  ```
{{define "demo"}}
这是测试内容：{{.}}
{{end}}
  ```

  代码中 define "模板名" 用于定义子模板，后面渲染模板会用到这个名字。

    2. 导入包

  ```
import "text/template"
  ```

  本文主要以 text/template 为例，如果要使用 html/template 直接替换包名就行，他们接口一样。

    3. 加载模板代码

  ```
// 加载模版代码，并且创建 template 对象 t
// template.ParseGlob 函数加载 views 目录下的所有 tpl 为后缀的模版文件
// template.Must 函数主要用于检测加载的模版有没有错误，有错误输出 panic 错误，并且结束程序。
t := template.Must(template.ParseGlob("./views/*.tpl"))
  ```

  

  ####  HelloWorld

  使用 Go 的 Web 模板引擎需要以下两个步骤:

    1. 对文本格式的模板源进行语法分析，创建一个经过语法分析的模板结构，其中模板源既可以是一个字符串，也可以是模板文件中包含的内容。

    2. 执行经过语法分析的模板，将 ResponseWriter 和模板所需的动态数据传递给模板引擎，被调用的模板引擎会把经过语法分析的模板和传入的数据结合起来，生成出最终的 HTML，并将这些 HTML 传递ResponseWriter。

  下面就让我们写一个简单的 HelloWorld

    1. 创建模板文件 hello.html

  ```
<html>
<head>
    <title>模板文件</title>
    <meta charset="utf-8"/>
</head>
<body>
//嵌入动作
{{.}}
</body>
</html>
  ```

    2. 在处理器中触发模板引擎

  ```
func handler(w http.ResponseWriter, r *http.Request) {
//解析模板文件
t, _ := template.ParseFiles("hello.html")
//执行模板
t.Execute(w, "Hello World!")
}
  ```

    3. 浏览器中的结果：`Hello World!`

  #### 解析模板

  ##### func [ParseFiles](https://github.com/golang/go/blob/master/src/html/template/template.go?name=release##305)

  ```
func ParseFiles(filenames ...string) (*Template, error)
  ```

  ParseFiles函数创建一个模板并解析filenames指定的文件里的模板定义。返回的模板的名字是第一个文件的文件名（不含扩展名），内容为解析后的第一个文件的内容。至少要提供一个文件。如果发生错误，会停止解析并返回nil。

  当我们调用 ParseFiles 函数解析模板文件时，Go 会创建一个新的模板，并**将给定的模板文件的名字作为新模板的名字**，如果该函数中传入了多个文件名，那么也只会返回一个模板，**而且以第一个文件的文件名作为模板的名字**，至于其他文件对应的模板则会被放到一个 **map** 中。让我们再来看一下 HelloWorld 中的代码：

  ```
t, _ := template.ParseFiles("hello.html")
  ```

   以上代码相当于调用 New 函数创建一个新模板，然后再调用 template 的ParseFiles 方法：

  ```
t := template.New("hello.html")
t, _ = t.ParseFiles("hello.html")
  ```

  ######## func [New](https://github.com/golang/go/blob/master/src/html/template/template.go?name=release##215)

  ```
func New(name string) *Template
  ```

  创建一个名为name的模板。

  ######## func (*Template) [ParseFiles](https://github.com/golang/go/blob/master/src/html/template/template.go?name=release##312)

  ```
func (t *Template) ParseFiles(filenames ...string) (*Template, error)
  ```

  ParseGlob方法解析filenames指定的文件里的模板定义并将解析结果与t关联。如果发生错误，会停止解析并返回nil，否则返回(t, nil)。至少要提供一个文件。

   我们在解析模板时都没有对错误进行处理，Go 提供了一个 Must 函数专门用来处理这个错误。Must 函数可以包裹起一个函数，被包裹的函数会返回一个指向模板的指针和一个错误，如果错误不是 nil，那么 Must 函数将产生一个 panic。

  ######## func [Must](https://github.com/golang/go/blob/master/src/html/template/template.go?name=release##294)

  ```
func Must(t *Template, err error) *Template
  ```

  Must函数用于包装返回(*Template, error)的函数/方法调用，它会在err非nil时panic，一般用于变量初始化：

  ```
var t = template.Must(template.New("name").Parse("html"))
  ```

  实验 Must 函数之后的代码

  ```
t := template.Must(template.ParseFiles("hello.html"))
  ```

  

  ######## func [ParseGlob](https://github.com/golang/go/blob/master/src/html/template/template.go?name=release##358)

  ```
func ParseGlob(pattern string) (*Template, error)
  ```

  ParseGlob创建一个模板并解析匹配pattern的文件（参见glob规则）里的模板定义。返回的模板的名字是第一个匹配的文件的文件名（不含扩展名），内容为解析后的第一个文件的内容。至少要存在一个匹配的文件。如果发生错误，会停止解析并返回nil。ParseGlob等价于使用匹配pattern的文件的列表为参数调用ParseFiles。

  通过该函数可以通过指定一个规则一次性传入多个模板文件，如：

  ```
t, _ := template.ParseGlob("*.html")
  ```

  #### 执行模板

    1. 通过 Execute 方法

  ######## func (*Template) [Execute](https://github.com/golang/go/blob/master/src/html/template/template.go?name=release##69)

  ```
func (t *Template) Execute(wr io.Writer, data interface{}) error
  ```

  Execute方法将解析好的模板应用到data上，并将输出写入wr。如果执行时出现错误，会停止执行，但有可能已经写入wr部分数据。模板可以安全的并发执行。

    2. 通过 ExecuteTemplate 方法

  ######## func (*Template) [ExecuteTemplate](https://github.com/golang/go/blob/master/src/html/template/template.go?name=release##82)

  ```
func (t *Template) ExecuteTemplate(wr io.Writer, name string, data interface{}) error
  ```

  ExecuteTemplate方法类似Execute，但是使用名为name的t关联的模板产生输出。

  例如：

  ```
t, _ := template.ParseFiles("hello.html", "hello2.html")
  ```

  变量 t 就是一个包含了两个模板的模板集合，第一个模板的名字是hello.html,第二个模板的名字是 hello2.html,如果直接调用 Execute 方法，则只有模板 hello.html 会被执行，如何想要执行模板 hello2.html，则需要调用 ExecuteTemplate 方法

  ```
t.ExecuteTemplate(w, "hello2.html", "我要在 hello2.html 中显示")
  ```

  #### 动作

  Go 模板的动作就是一些嵌入到模板里面的命令，这些命令在模板中需要放到两个大括号里`{{ 动作 }}`，之前我们已经用过一个**很重要的动作**：点`（.）`，它代表了传递给模板的数据。下面我们再介绍几个常用的动作，如果还想了解其他类型的动作，可以参考text/template 库的文档。

  

  ### template基本语法

  *Go模板引擎的模板表达式都包括在 **{{** 和 **}}** 之间。*其中`{{.}}`中的点表示当前对象。

  当我们传入一个结构体对象时，我们可以根据`.`来访问结构体的对应字段。例如：

  ```
// main.go

type UserInfo struct {
    Name   string
    Gender string
    Age    int
}

func sayHello(w http.ResponseWriter, r *http.Request) {
    // 解析指定文件生成模板对象
    tmpl, err := template.ParseFiles("./hello.html")
    if err != nil {
        fmt.Println("create template failed, err:", err)
        return
    }
    // 利用给定数据渲染模板，并将结果写入w
    user := UserInfo{
        Name:   "枯藤",
        Gender: "男",
        Age:    18,
    }
    tmpl.Execute(w, user)
}
  ```

  ```
// hello.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hello</title>

</head>
<body>
    <p>Hello {{.Name}}</p>
    <p>性别 {{.Gender}}</p>
    <p>年龄 {{.Age}}</p>
</body>
</html>
  ```

  同理，当我们传入的变量是map时，也可以在模板文件中通过`.`根据key来取值。

  #### 注释

  ```
{{/* a comment */}}
//注释，执行时会忽略。可以多行。注释不能嵌套，并且必须紧贴分界符始止。
  ```

  #### pipeline

  pipeline是指产生数据的操作。比如`{{.}}`、`{{.Name}}`等。Go的模板语法中支持使用管道符号`|`链接多个命令，用法和unix下的管道类似：`|`前面的命令会将运算结果(或返回值)传递给后一个命令的最后一个位置。

  注意 : 并不是只有使用了`|`才是pipeline。Go的模板语法中，pipeline的概念是传递数据，只要能产生数据的，都是pipeline。

  ####  变量

  Action里可以初始化一个变量来捕获管道的执行结果。初始化语法如下：

  ```
    $variable := pipeline
  ```

  其中`$variable`是变量的名字。声明变量的action不会产生任何输出。

  ### 条件判断

  Go模板语法中的条件判断有以下几种:

  ```go
{{if pipeline}} T1 {{end}}

{{if pipeline}} T1 {{else}} T0 {{end}}

{{if pipeline}} T1 {{else if pipeline}} T0 {{end}}
  ```

  #### range

  Go的模板语法中使用range关键字进行遍历，有以下两种写法，其中pipeline的值必须是数组、切片、字典或者通道。

  ```go
{{range pipeline}} T1 {{end}}
如果pipeline的值其长度为0，不会有任何输出

{{range pipeline}} T1 {{else}} T0 {{end}}
如果pipeline的值其长度为0，则会执行T0。
  ```

  #### with

  ```go
{{with pipeline}} T1 {{end}}
如果pipeline为empty不产生输出，否则将dot设为pipeline的值并执行T1。不修改外面的dot。

{{with pipeline}} T1 {{else}} T0 {{end}}
如果pipeline为empty，不改变dot并执行T0，否则dot设为pipeline的值并执行T1。
  ```

  ### 预定义函数

  执行模板时，函数从两个函数字典中查找：首先是模板函数字典，然后是全局函数字典。*一般不在模板内定义函数，而是使用Funcs方法添加函数到模板里。*

  预定义的全局函数如下：

  ```go
and
    函数返回它的第一个empty参数或者最后一个参数；
    就是说"and x y"等价于"if x then y else x"；所有参数都会执行；
or
    返回第一个非empty参数或者最后一个参数；
    亦即"or x y"等价于"if x then x else y"；所有参数都会执行；
not
    返回它的单个参数的布尔值的否定
len
    返回它的参数的整数类型长度
index
    执行结果为第一个参数以剩下的参数为索引/键指向的值；
    如"index x 1 2 3"返回x[1][2][3]的值；每个被索引的主体必须是数组、切片或者字典。
print
    即fmt.Sprint
printf
    即fmt.Sprintf
println
    即fmt.Sprintln
html
    返回其参数文本表示的HTML逸码等价表示。
urlquery
    返回其参数文本表示的可嵌入URL查询的逸码等价表示。
js
    返回其参数文本表示的JavaScript逸码等价表示。
call
    执行结果是调用第一个参数的返回值，该参数必须是函数类型，其余参数作为调用该函数的参数；
    如"call .X.Y 1 2"等价于go语言里的dot.X.Y(1, 2)；
    其中Y是函数类型的字段或者字典的值，或者其他类似情况；
    call的第一个参数的执行结果必须是函数类型的值（和预定义函数如print明显不同）；
    该函数类型值必须有1到2个返回值，如果有2个则后一个必须是error接口类型；
    如果有2个返回值的方法返回的error非nil，模板执行会中断并返回给调用模板执行者该错误；
  ```

  ### 比较函数

  布尔函数会将任何类型的零值视为假，其余视为真。

  下面是定义为函数的二元比较运算的集合：

  ```
    eq      如果arg1 == arg2则返回真
    ne      如果arg1 != arg2则返回真
    lt      如果arg1 < arg2则返回真
    le      如果arg1 <= arg2则返回真
    gt      如果arg1 > arg2则返回真
    ge      如果arg1 >= arg2则返回真
  ```

  为了简化多参数相等检测，eq（只有eq）可以接受2个或更多个参数，它会将第一个参数和其余参数依次比较，返回下式的结果：

  ```
    {{eq arg1 arg2 arg3}}
  ```

  比较函数只适用于基本类型（或重定义的基本类型，如”type Celsius float32”）。但是，整数和浮点数不能互相比较。

  ### 自定义函数

  Go的模板支持自定义函数。

  ```go
func sayHello(w http.ResponseWriter, r *http.Request) {
    htmlByte, err := ioutil.ReadFile("./hello.html")
    if err != nil {
        fmt.Println("read html failed, err:", err)
        return
    }
    // 自定义一个夸人的模板函数
    kua := func(arg string) (string, error) {
        return arg + "真帅", nil
    }
    // 采用链式操作在Parse之前调用Funcs添加自定义的kua函数
    tmpl, err := template.New("hello").Funcs(template.FuncMap{"kua": kua}).Parse(string(htmlByte))
    if err != nil {
        fmt.Println("create template failed, err:", err)
        return
    }

    user := UserInfo{
        Name:   "枯藤",
        Gender: "男",
        Age:    18,
    }
    // 使用user渲染模板，并将结果写入w
    tmpl.Execute(w, user)
}
  ```

  我们可以在模板文件hello.html中使用我们自定义的kua函数了。`kua` 和`.`之间必须有空格

  ```
    {{kua .Name}}
  ```

  ### 1.1.7. 嵌套template

  我们可以在template中嵌套其他的template。这个template可以是单独的文件，也可以是通过define定义的template。

  举个例子： t.html文件内容如下：

  ```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>tmpl test</title>
</head>
<body>

    <h1>测试嵌套template语法</h1>
    <hr>
    {{template "ul.html"}}
    <hr>
    {{template "ol.html"}}
</body>
</html>

{{ define "ol.html"}}
<h1>这是ol.html</h1>
<ol>
    <li>吃饭</li>
    <li>睡觉</li>
    <li>打豆豆</li>
</ol>
{{end}}
  ```

  ul.html文件内容如下：

  ```go
<ul>
    <li>注释</li>
    <li>日志</li>
    <li>测试</li>
</ul>
  ```

  我们注册一个templDemo路由处理函数.

  ```go
http.HandleFunc("/tmpl", tmplDemo)
  ```

  tmplDemo函数的具体内容如下：

  ```go
func tmplDemo(w http.ResponseWriter, r *http.Request) {
    tmpl, err := template.ParseFiles("./t.html", "./ul.html")
    if err != nil {
        fmt.Println("create template failed, err:", err)
        return
    }
    user := UserInfo{
        Name:   "枯藤",
        Gender: "男",
        Age:    18,
    }
    tmpl.Execute(w, user)
}
  ```

  

  ## 第七章 会话控制

  ####  Cookie

  ######  简介 

  Cookie 实际上就是服务器保存在浏览器上的一段信息。浏览器有了 Cookie 之后，每次向服务器发送请求时都会同时将该信息发送给服务器，服务器收到请求后，就可以根据该信息处理请求。

  ###### type [Cookie](https://github.com/golang/go/blob/master/src/net/http/cookie.go?name=release##23)

  ```go
type Cookie struct {
    Name       string
    Value      string
    Path       string
    Domain     string
    Expires    time.Time
    RawExpires string
    // MaxAge=0表示未设置Max-Age属性
    // MaxAge<0表示立刻删除该cookie，等价于"Max-Age: 0"
    // MaxAge>0表示存在Max-Age属性，单位是秒
    MaxAge   int
    Secure   bool
    HttpOnly bool
    Raw      string
    Unparsed []string // 未解析的“属性-值”对的原始文本
}
  ```

  Cookie代表一个出现在HTTP回复的头域中Set-Cookie头的值里或者HTTP请求的头域中Cookie头的值里的HTTP cookie。

  ###### Cookie 的运行原理 

    1) 第一次向服务器发送请求时在服务器端创建 Cookie
    2) 将在服务器端创建的 Cookie 以响应头的方式发送给浏览器
    3) 以后再发送请求浏览器就会携带着该 Cookie
    4) 服务器得到 Cookie 之后根据 Cookie 的信息来区分不同的用户

  ###### 创建 Cookie 并将它发送给浏览器 

    1) 在服务器创建 Cookie 并将它发送给浏览器

  ```
// 创建处理器函数
func handler(w http.ResponseWriter, r *http.Request) {
	cookie1 := http.Cookie{
		Name:     "user1",
		Value:    "admin",
		HttpOnly: true,
	}

	cookie2 := http.Cookie{
		Name:     "user2",
		Value:    "superAdmin",
		HttpOnly: true,
	}
	//将 Cookie 发送给浏览器,即添加第一个 Cookie
	w.Header().Set("Set-Cookie", cookie1.String())

	w.Header().Add("Set-Cookie", cookie2.String())
}

  ```

    2. 浏览器报文中的内容

  ```
HTTP/1.1 200 OK
Set-Cookie: user1=admin; HttpOnly
Set-Cookie: user2=superAdmin; HttpOnly
  ```

  *以后每次发送请求浏览器都会携带着 Cookie*

    3. 除了 Set 和 Add 方法之外，Go 还提供了一种更快捷的设置 Cookie 的方式，就是通过 net/http 库中的 SetCookie 方法

  ######## func [SetCookie](https://github.com/golang/go/blob/master/src/net/http/cookie.go?name=release##129)

  ```
func SetCookie(w ResponseWriter, cookie *Cookie)
  ```

  SetCookie在w的头域中添加Set-Cookie头，该HTTP头的值为cookie。

    1. 将上面的代码进行修改

  ```
func handler(w http.ResponseWriter, r *http.Request) {
	...
	
	http.SetCookie(w,&cookie1)
	http.SetCookie(w,&cookie2)
}
  ```

  ###### 读取 Cookie 

  由于我们在发送请求时 Cookie 在请求头中，所以我们可以通过 Request 结构中的Header 字段来获取 Cookie。

  ```
func handler(w http.ResponseWriter, r *http.Request) {
	//获取请求头中的 Cookie
	cookies := r.Header["Cookie"]
	fmt.Fprintln(w, cookies)
}
  ```

  浏览器的结果：

  ```
[user1=admin; user2=superAdmin]
  ```

  ###### 设置 Cookie 的有效时间 

  Cookie**默认是会话级别的**，当关闭浏览器之后Cookie将失效，我们可以通过Cookie结构的 MaxAge 字段设置 Cookie 的有效时间。

  ```
func handler(w http.ResponseWriter, r *http.Request) {
	cookie := http.Cookie{
		Name:     "user",
		Value:    "persistAdmin",
		HttpOnly: true,
		MaxAge:   60}
	//将 Cookie 发送给浏览器
	w.Header().Set("Set-Cookie", cookie.String())
}
  ```

  ######  Cookie 的用途 

    1) 广告推荐
    2) 免登录

  #### Session

  ######  简介 

  使用 Cookie 有一个非常大的局限，就是如果 Cookie 很多，则无形的增加了客户端与服务端的数据传输量。而且由于浏览器对 Cookie 数量的限制，注定我们不能在 Cookie 中保存过多的信息，于是 Session 出现。

   *Session 的作用就是**在服务器端保存一些用户的数据**，然后传递给用户一个特殊的 Cookie，这个 Cookie 对应着这个服务器中的一个 Session，通过它就可以获取到保存用户信息的Session，进而就知道是哪个用户再发送请求。*

  ###### Session 的运行原理 

    1) 第一次向服务器发送请求时创建 Session，给它设置一个全球唯一的 ID（可以通过UUID 生成）
    2) 创建一个 Cookie，将 Cookie 的 Value 设置为 Session 的 ID 值，并将 Cookie 发送给浏览器
    3) 以后再发送请求浏览器就会携带着该 Cookie
    4) 服务器获取 Cookie 并根据它的 Value 值找到服务器中对应的 Session，也就知道了请求是那个用户发的

  

  ## 第 8 章 处理静态文件

  对于 HTML 页面中的 css 以及 js 等静态文件，需要使用使用 net/http 包下的以下
  方法来处理

    1) StripPrefix 函数

  ######## func [StripPrefix](https://github.com/golang/go/blob/master/src/net/http/server.go?name=release##1260)

  ```
func StripPrefix(prefix string, h Handler) Handler
  ```

  StripPrefix返回一个处理器，该处理器会将请求的URL.Path字段中给定前缀prefix去除后再交由h处理。StripPrefix会向URL.Path字段中没有给定前缀的请求回复404 page not found。

  Example

  ```go
// To serve a directory on disk (/tmp) under an alternate URL
// path (/tmpfiles/), use StripPrefix to modify the request
// URL's path before the FileServer sees it:
http.Handle("/tmpfiles/", http.StripPrefix("/tmpfiles/", http.FileServer(http.Dir("/tmp"))))
  ```

    2. FileServer 函数

  ###### func [FileServer](https://github.com/golang/go/blob/master/src/net/http/fs.go?name=release##435)

  ```
func FileServer(root FileSystem) Handler
  ```

  FileServer返回一个使用FileSystem接口root提供文件访问服务的HTTP处理器。要使用操作系统的FileSystem接口实现，可使用http.Dir：

  ```
http.Handle("/", http.FileServer(http.Dir("/tmp")))
  ```

  Example

  ```
// Simple static webserver:
log.Fatal(http.ListenAndServe(":8080", http.FileServer(http.Dir("/usr/share/doc"))))
  ```

  ###### type [FileSystem](https://github.com/golang/go/blob/master/src/net/http/fs.go?name=release##50)

  ```
type FileSystem interface {
    Open(name string) (File, error)
}
  ```

  FileSystem接口实现了对一系列命名文件的访问。文件路径的分隔符为'/'，不管主机操作系统的惯例如何。

  ###### type [Dir](https://github.com/golang/go/blob/master/src/net/http/fs.go?name=release##29)

  ```
type Dir string
  ```

  Dir使用限制到指定目录树的本地文件系统实现了http.FileSystem接口。空Dir被视为"."，即代表当前目录。

  ######## func (Dir) [Open](https://github.com/golang/go/blob/master/src/net/http/fs.go?name=release##31)

  ```
func (d Dir) Open(name string) (File, error)
  ```

  例如：

    1. 项目的静态文件的目录结构如下：

  ![image-20220917202346403](https://img.herrluk.icu/typoraPicture/image-20220917202346403-3662827.png)

    2. index.html 模板文件中引入的 css 样式的地址如下：

  ```
<link type="text/css" rel="stylesheet" href="/views/static/css/style.css" >
  ```

  

    3. 对静态文件的处理

  ```
http.Handle("/static/", http.StripPrefix("/static/", http.FileServer(http.Dir("views/static**"))))
  ```

  - `/static/`会匹配 以 `/static/`开头的路径，当浏览器请求 index.html 页面中的style.css 文件时，*static 前缀会被替换为 views/staic，然后去 views/static/css 目录中取查找 style.css 文件*

  # gin 框架

  ## gin 框架中的路由

  ### 路由概述

  路由（Routing）是由一个 URI（或者叫路径）和一个特定的 HTTP 方法（GET、POST 等） 组成的，涉及到应用如何响应客户端对某个网站节点的访问。 

  RESTful API 是目前比较成熟的一套互联网应用程序的 API 设计理论，所以我们设计我们的路 由的时候建议参考 RESTful API 指南。 

  在 RESTful 架构中，每个网址代表一种资源，不同的请求方式表示执行不同的操作： 

|                |                                                |
| :------------: | :--------------------------------------------: |
|  GET(SELECT)   |         从服务器取出资源（一项或多项）         |
|  POST(CREATE)  |              在服务器新建一个资源              |
|  PUT(UPDATE)   | 在服务器更新资源（客户端提供改变后的完整资源） |
| DELECT(DELECT) |                从服务器删除资源                |

  ### 简单的路由配置

  

  ```
func main() {
	// 创建一个默认的路由引擎
	r := gin.Default()
	// 配置路由
	r.GET("/", func(c *gin.Context) {
		c.JSON(200, gin.H{
			// c.JSON：返回 JSON 格式的数据
			"message": "Hello world!",
		})
	})
	// 启动 HTTP 服务，默认在 0.0.0.0:8080 启动服务
	r.Run()
}
  ```

  ##### type [Engine](https://github.com/gin-gonic/gin/blob/v1.8.1/gin.go#L80) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Engine)

  Engine is the framework's instance, it contains the muxer, middleware and configuration settings. *Create an instance of Engine, by using New() or Default()*

  ##### func [Default](https://github.com/gin-gonic/gin/blob/v1.8.1/gin.go#L212) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Default)

  ```
func Default() *Engine
  ```

  Default returns an Engine instance with the Logger and Recovery middleware already attached.

  ##### func [New](https://github.com/gin-gonic/gin/blob/v1.8.1/gin.go#L179) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#New)

  ```
func New() *Engine
  ```

  New returns a new blank Engine instance *without any middleware attached.* 

  ##### type [RouterGroup](https://github.com/gin-gonic/gin/blob/v1.8.1/routergroup.go#L54) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#RouterGroup)

  ```
type RouterGroup struct {
	Handlers HandlersChain
	// contains filtered or unexported fields
}
  ```

  RouterGroup is used internally to configure router, a RouterGroup is associated with a prefix and an array of handlers (middleware).

  ##### func (*RouterGroup) [GET](https://github.com/gin-gonic/gin/blob/v1.8.1/routergroup.go#L115) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#RouterGroup.GET)

  ```
func (group *RouterGroup) GET(relativePath string, handlers ...HandlerFunc) IRoutes
  ```

  GET is a shortcut for router.Handle("GET", path, handle).

  ##### type [HandlerFunc](https://github.com/gin-gonic/gin/blob/v1.8.1/gin.go#L44) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#HandlerFunc)

  ```
type HandlerFunc func(*Context)
  ```

  HandlerFunc defines the handler used by gin middleware as return value.

  ##### type [Context](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L51) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context)

  ```
type Context struct {
	Request *http.Request
	Writer  ResponseWriter

	Params Params

	// Keys is a key/value pair exclusively for the context of each request.
	Keys map[string]any

	// Errors is a list of errors attached to all the handlers/middlewares who used this context.
	Errors errorMsgs

	// Accepted defines a list of manually accepted formats for content negotiation.
	Accepted []string
	// contains filtered or unexported fields
}
  ```

  Context is the most important part of gin. It allows us to pass variables between middleware, manage the flow, validate the JSON of a request and render a JSON response for example.

  ##### func (*Context) [String](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L990) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.String)

  ```
func (c *Context) String(code int, format string, values ...any)
  ```

  String writes the given string into the response body.

  ##### func (*Context) [JSON](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L952) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.JSON)

  ```
func (c *Context) JSON(code int, obj any)
  ```

  JSON serializes the given struct as JSON into the response body. It also sets the Content-Type as "application/json".

  ##### gin.H

  gin.H 就是 `map[string]interface{}` 的缩写， 使用时简单明了

  当用 GET 请求访问一个网址的时候，做什么事情： 

  ```
	r.GET("网址", func(c *gin.Context) {
		c.String(200, "Get")
	})
  ```

  #### 静态文件服务

  当我们渲染的 HTML 文件中引用了静态文件时,我们需要配置静态 web 服务 

  ```
func main() { 
r := gin.Default() 
r.Static("/static", "./static") r.LoadHTMLGlob("templates/**/*") // ... r.Run(":8080") 
}

// html文件：
<link rel="stylesheet" href="/static/css/base.css" />
  ```

  `r.Static("/static", "./static")` 

  前面的/static 表示路由 

  后面的./static 表示路径 

  ##### func (*RouterGroup) [Static](https://github.com/gin-gonic/gin/blob/v1.8.1/routergroup.go#L186) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#RouterGroup.Static)

  ```
func (group *RouterGroup) Static(relativePath, root string) IRoutes
  ```

  Static serves files from the given file system root. Internally a http.FileServer is used, therefore http.NotFound is used instead of the Router's NotFound handler. To use the operating system's file system implementation, use :

  ```
router.Static("/static", "/var/www")
  ```

  ## 渲染模板

### 全部模板放在一个目录里的配置方法

1. 我们首先在项目根目录新建 templates 文件夹，然后在文件夹中新建 index.html 
2. Gin 框架中使用 c.HTML 可以渲染模板，渲染模板前需要使用LoadHTMLGlob()或者 LoadHTMLFiles()方法加载模板 

  ##### func (*Context) [HTML](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L918) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.HTML)

  ```
func (c *Context) HTML(code int, name string, obj any)
  ```

  HTML renders the HTTP template specified by its file name. It also updates the HTTP code and sets the Content-Type as "text/html". See http://golang.org/doc/articles/wiki/

  ##### func (*Engine) [LoadHTMLGlob](https://github.com/gin-gonic/gin/blob/v1.8.1/gin.go#L248) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Engine.LoadHTMLGlob)

  ```
func (engine *Engine) LoadHTMLGlob(pattern string)
  ```

  LoadHTMLGlob loads HTML files identified by glob pattern and associates the result with HTML renderer.  LoadHTMLGlob 加载由glob模式识别的HTML文件，并将结果与HTML渲染器关联起来。

  ```
r.LoadHTMLGlob("templates/*")
//加载 templates 目录下的所有文件
  ```

  main.go：

```
func main() {
	r := gin.Default()
	//加载模板目录
	r.LoadHTMLGlob("templates/*")
	r.GET("/", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index.html", gin.H{
			"title": "首页",
		})
	})
	r.Run()
}
```

### 模板放在不同目录里的配置方法

Gin 框架中如果不同目录下面有同名模板的话我们需要使用下面方法加载模板 

**注意：**定义模板的时候需要通过 define 定义名称 

```
{{ define "admin/index.html" }}
//相当于给模板定义一个名字 define end 成对出现
<!DOCTYPE html>
...
</html> 
{{ end }}
```

对templates/admin/index.html：

`{{ define "admin/index.html" }}`

对templates/default/index.html：

`{{ define "default/index.html" }}`

**注意：**如果模板在多级目录里面的话需要这样配置 `r.LoadHTMLGlob("templates/**/**/*")`  `/**` 表示目录。

### 变量

我们还可以在模板中声明变量，用来保存传入模板的数据或其他语句生成的结果。具体语法如下： 

```
<h4>{{ $obj := .title }}</h4>
<h4>{{ $obj }}</h4>
```

### 移除空格

有时候我们在使用模板语法的时候会不可避免的引入一下空格或者换行符，这样模板最终渲染出来的内容可能就和我们想的不一样，这个时候可以使用`{{-`语法去除模板内容左侧的所有空白符号， 使用`-}}`去除模板内容右侧的所有空白符号。 

例如： 

```
{{- .Name -}}
```

**注意：**-要紧挨{{和}}，同时与模板值之间需要使用空格分隔

  ## 路由详解

  路由（Routing）是由一个 URI（或者叫路径）和一个特定的 HTTP 方法（GET、POST 等） 组成的，涉及到应用如何响应客户端对某个网站节点的访问。 

  ### GET POST 以及获取 Get Post 传值

  #### Get 请求传值 

  GET  /user?uid=20&page=1 

  ```
	router.GET("/user", func(c *gin.Context) { 
		uid := c.Query("uid") 
		page := c.DefaultQuery("page", "0") 
		c.String(200, "uid=%v page=%v", uid, page) 
	})
  ```

  #### 动态路由传值

  域名/user/20

  ```
	r.GET("/user/:uid", func(c *gin.Context) {
		uid := c.Param("uid")
		c.String(200, "userID=%s", uid)
	})
  ```

  ##### func (*Context) [Param](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L389) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.Param)

  ```
func (c *Context) Param(key string) string
  ```

  Param returns the value of the URL param. It is a shortcut for c.Params.ByName(key)

  ```
router.GET("/user/:id", func(c *gin.Context) {
    // a GET request to /user/john
    id := c.Param("id") // id == "john"
})
  ```

#### **Post** **请求传值 获取** **form** **表单数据** 

在 default 文件夹定义一个 add_user.html页面

```
{{ define "default/user.html" }}
...
<body>
//注意这里 action
    <form action="/doAddUser" method="post">
        用户名：<input type="text" name="username" />
        密码：<input type="password" name="password" />
        <input type="submit" value="提交">
    </form>
</body>
</html>
{{ end }}
```

在 main.c中通过 `c.PostForm` 接收表单传过来的数据 

```go
 r.GET("/addUser", func(c *gin.Context) {
		c.HTML(200, "default/add_user.html",gin.H{})
	})
	
	r.POST("/doAddUser", func(c *gin.Context) {
		username := c.PostForm("username")
		password := c.PostForm("password")
		c.JSON(http.StatusOK, gin.H{
			"username": username,
			"password": password,
		})
	})
```

#### 获取 GET POST 传递的数据绑定到结构体

为了能够更方便的获取请求相关参数，提高开发效率，我们可以基于请求的 Content-Type 识别请求数据类型并利用反射机制自动提取请求中 QueryString、form 表单、JSON、XML 等参数到结构体中。 

下面的示例代码演示了.ShouldBind()强大的功能，它能够基于请求自动提取 JSON、form 表单和 QueryString 类型的数据，并把值绑定到指定的结构体对象。

##### Get 传值绑定到结构体 

/?username=zhangsan&password=123456 

```
	type UserInfo struct {
	Username string `json:"username" form:"username"`
	Password string `json:"password" form:"password"`
}

	r.GET("/getUser", func(c *gin.Context) {
		user := &UserInfo{}

		err := c.ShouldBind(&user)
		if err == nil {
			c.JSON(http.StatusOK, user)
			fmt.Println(user)
		} else {
			c.JSON(http.StatusOK, gin.H{
				"err": err.Error(),
			})
		}
	})
```

返回数据 

{"user":"zhangsan","password":"123456"}

##### Post 传值绑定到结构体

```
	r.POST("/getUser2", func(c *gin.Context) {
		user := &UserInfo{}

		err := c.ShouldBind(&user)
		if err == nil {
			c.JSON(http.StatusOK, user)
			fmt.Println(user)
		} else {
			c.JSON(http.StatusOK, gin.H{
				"err": err.Error(),
			})
		}
	})
```

##### func (*Context) [ShouldBind](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L678) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.ShouldBind)

```
func (c *Context) ShouldBind(obj any) error
```

ShouldBind checks the Method and Content-Type to select a binding engine automatically, Depending on the "Content-Type" header different bindings are used, for example:

```
"application/json" --> JSON binding
"application/xml"  --> XML binding
```

It parses the request's body as JSON if Content-Type == "application/json" using JSON or XML as a JSON input. It decodes the json payload into the struct specified as a pointer. Like c.Bind() but this method does not set the response status code to 400 or abort if input is not valid.

### 简单的路由组

```
func main() {
	router := gin.Default()

	// 简单的路由组: v1
	v1 := router.Group("/v1")
	{
		v1.POST("/login", loginEndpoint)
		v1.POST("/submit", submitEndpoint)
		v1.POST("/read", readEndpoint)
	}

	// 简单的路由组: v2
	v2 := router.Group("/v2")
	{
		v2.POST("/login", loginEndpoint)
		v2.POST("/submit", submitEndpoint)
		v2.POST("/read", readEndpoint)
	}

	router.Run(":8080")
}
```

### Gin 路由文件 分组

新建 routes 文件夹，routes 文件下面新建 adminRoutes.go、apiRoutes.go、 defaultRoutes.go 

新建 adminRoutes.go

```
package routes

import (
	"github.com/gin-gonic/gin"
	"net/http"
)
//将 main 中的 r 以参数传入
func AdminRoutesInit(r *gin.Engine) {
	adminRouter := router.Group("/admin")
	{
		adminRouter.GET("/user", func(c *gin.Context) { c.String(http.StatusOK, "用户") })
		adminRouter.GET("/news", func(c *gin.Context) { c.String(http.StatusOK, "news") })
	}
}

```

配置 main.go

```
package main 
import ( "gin_demo/routes" 	//注意要 import 这个
"github.com/gin-gonic/gin")
func main() { 
r := gin.Default() 
routes.AdminRoutesInit(r)//通过 r 注册路由组 routes.ApiRoutesInit(r) routes.DefaultRoutesInit(r) 
r.Run(":8080") 
}
```

## gin 中自定义控制器

### 控制器分组

当我们的项目比较大的时候有必要对我们的控制器进行分组 

新建 controller/admin/UserController.go

```
package admin

import "github.com/gin-gonic/gin"

type UserController struct {}

func (con UserController)Index(c *gin.Context)  {
	c.String(200,"用户列表")
}

```

配置对应的路由 --adminRoutes.go 

其他路由的配置方法类似 

```
package routes

import (
	"github.com/gin-gonic/gin"
	"github.com/herrluk/goWebLearning/controller/admin"
)

func AdminRoutesInit(router *gin.Engine) {
	
	adminRouter := router.Group("/admin")
	{
		adminRouter.GET("/user",  admin.UserController{}.Index)
		//不能加（），现在是注册处理器，加了（）是运行函数
		adminRouter.GET("/user",  admin.UserController{}.XXX)
		
	}
}

```

### 控制器的继承

1、新建 controller/admin/BaseController.go

```
package admin

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

type BaseController struct {
}

func (b BaseController) Success(ctx *gin.Context) {
	ctx.String(http.StatusOK, "成功")
}
func (b BaseController) Error(ctx *gin.Context) {
	ctx.String(http.StatusOK, "失败")
}

```

2、UserController 继承 BaseController 

继承后就可以调用控制器里面的公共方法了

```
package admin

import "github.com/gin-gonic/gin"

type UserController struct {
	BaseController
}

func (con UserController) Index(c *gin.Context) {
	con.Success(c)
}

```

## gin 中间件

Gin 框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如登录认证、权限校验、数据分页、记录日志、耗时统计等。 

**通俗的讲**：中间件就是匹配路由前和匹配路由完成后执行的一系列操作

### 路由中间件 

#### 初识中间件 

Gin 中的中间件必须是一个 gin.HandlerFunc 类型，*配置路由的时候可以传递多个 func 回调函数，最后一个 func 回调函数设置为路由，前面触发的方法都可以称为中间件。*

```
func InitMiddleWare(ctx *gin.Context) {
	fmt.Println("我是一个中间件")
}

r.GET("/", InitMiddleWare, admin.UserController{}.Index)

```

#### ctx.Next()调用该请求的剩余处理程序 

中间件里面加上 ctx.Next()可以让我们在路由匹配完成后执行一些操作。 比如我们统计一个请求的执行时间。

```
func InitMiddleWare(ctx *gin.Context) {
	start := time.Now().UnixNano()
	//调用 Next() 处理该次 GET 请求的剩余处理请求，最后再执行 Next 下面的语句
	ctx.Next()
	end := time.Now().UnixNano()
	fmt.Println(end - start)
}
```

##### func (*Context) [Next](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L170) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.Next)

```
func (c *Context) Next()
```

Next should be used only inside middleware. It executes the pending handlers in the chain inside the calling handler. See example in GitHub.

#### 一个路由配置多个中间件的执行顺序

```
func initMiddlewareOne(ctx *gin.Context) { fmt.Println("initMiddlewareOne--1-执行中中间件") // 调用该请求的剩余处理程序 ctx.Next() fmt.Println("initMiddlewareOne--2-执行中中间件") }func initMiddlewareTwo(ctx *gin.Context) { fmt.Println("initMiddlewareTwo--1-执行中中间件") // 调用该请求的剩余处理程序 ctx.Next() fmt.Println("initMiddlewareTwo--2-执行中中间件") }
```

**控制台内容：** 

initMiddlewareOne--1-执行中中间件 

initMiddlewareTwo--1-执行中中间件 

执行路由里面的程序 

initMiddlewareTwo--2-执行中中间件 

initMiddlewareOne--2-执行中中间件

### 全局中间件

##### func (*Engine) [Use](https://github.com/gin-gonic/gin/blob/v1.8.1/gin.go#L303) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Engine.Use)

```
func (engine *Engine) Use(middleware ...HandlerFunc) IRoutes
```

Use attaches a global middleware to the router. i.e. the middleware attached through Use() will be included in the handlers chain for every single request. Even 404, 405, static files... For example, this is the right place for a logger or error management middleware.

```
func main(){
r := gin.Default() 
r.Use(initMiddleware)
}
```

### 在路由分组中配置中间件

1. 为路由组注册中间件有以下两种写法 

   写法 1： 

   ```
   shopGroup := r.Group("/shop", StatCost()) { 
   shopGroup.GET("/index", func(c *gin.Context) {...}) ... }
   ```

   写法 2： 

   ```
   shopGroup := r.Group("/shop") 
   shopGroup.Use(StatCost()) { 
   shopGroup.GET("/index", func(c *gin.Context) {...}) ... }
   ```

##### func (*RouterGroup) [Group](https://github.com/gin-gonic/gin/blob/v1.8.1/routergroup.go#L71) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#RouterGroup.Group)

```
func (group *RouterGroup) Group(relativePath string, handlers ...HandlerFunc) *RouterGroup
```

Group creates a new router group. You should add all the routes that have common middlewares or the same path prefix. For example, all the routes that use a common middleware for authorization could be grouped.

### 中间件和对应控制器之间共享数据

设置值：

```
ctx.Set("username", "张三")
```

获取值：

```
username, _ := ctx.Get("username")
```

中间件设置：

```
func (con IndexController) Index(c *gin.Context) {

	username, _ := c.Get("username")//空接口类型，需要使用类型断言
	fmt.Println(username)

	//类型断言
	v, ok := username.(string)
	if ok {
		c.String(200, "用户列表--"+v)
	} else {
		c.String(200, "用户列表--获取用户失败")
	}

}
```

控制器获取值：

```
func (c UserController) Index(ctx *gin.Context) { 
username, _ := ctx.Get("username") 
fmt.Println(username) ctx.String(http.StatusOK, "这是用户首页 111") 
}
```

### 中间件注意事项 

##### gin 默认中间件 

gin.Default()默认使用了 Logger 和 Recovery 中间件，其中： 

-  Logger 中间件将日志写入 gin.DefaultWriter，即使配置了 GIN_MODE=release。 
-  Recovery 中间件会 recover 任何 panic。如果有 panic 的话，会写入 500 响应码。 

如果不想使用上面两个默认的中间件，可以使用 gin.New()新建一个没有任何默认中间件的路由。 

##### gin 中间件中使用 goroutine 

当在中间件或 handler 中启动新的 goroutine 时，不能使用原始的上下文（c *gin.Context）， 必须使用其只读副本c.Copy()

## Gin 中自定义 Model 

### 关于 Model 

如果我们的应用非常简单的话，我们可以在 Controller 里面处理常见的业务逻辑。但是如果 我们有一个功能想在多个控制器、或者多个模板里面复用的话，那么我们就可以把公共的功 能单独抽取出来作为一个模块（Model）。 Model 是逐步抽象的过程，一般我们会在 Model 里面封装一些公共的方法让不同 Controller 使用，也可以在 Model 中实现和数据库打交道



## Gin 文件上传

**注意**：需要在上传文件的 form 表单上面需要加入 `enctype="multipart/form-data"`

### 单文件上传

[官方示例](https://gin-gonic.com/zh-cn/docs/examples/upload-file/single-file/)

项目中实现文件上传：

1. 定义模板：

```
{{ define "admin/useradd.html" }}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h2>演示文件上传</h2>
//注意这里 action
    <form action="/admin/user/doUpload" method="post" enctype="multipart/form-data">
        
        用户名:<input type="text" name="username" placeholder="用户名" />
        <br>
        <br>
        头 像<input type="file" name="face" />
        <br>    <br>

        <input type="submit" value="提交">
    </form>
</body>
</html>
{{ end }}
```

2. 定义业务逻辑

```
func (con UserController) DoUpload(c *gin.Context) {
	username := c.PostForm("username")

	file, err := c.FormFile("face")

	// file.Filename 获取文件名称  aaa.jpg   ./static/upload/aaa.jpg
	dst := path.Join("./static/upload", file.Filename)
	if err == nil {
		c.SaveUploadedFile(file, dst)
	}
	// c.String(200, "执行上传")
	c.JSON(http.StatusOK, gin.H{
		"success":  true,
		"username": username,
		"dst":      dst,
	})
}
```

##### package path

```
import "path"
```

path实现了对斜杠分隔的路径的实用操作函数。

##### func [Join](https://github.com/golang/go/blob/master/src/path/path.go?name=release#150)

```
func Join(elem ...string) string
```

Join函数可以将任意数量的路径元素放入一个单一路径里，会根据需要添加斜杠。结果是经过简化的，所有的空字符串元素会被忽略。

Example

```
fmt.Println(path.Join("a", "b", "c"))
```

Output:

```
a/b/c
```

##### func (*Context) [SaveUploadedFile](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L591) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.SaveUploadedFile)

```
func (c *Context) SaveUploadedFile(file *multipart.FileHeader, dst string) error
```

SaveUploadedFile uploads the form file to specific dst.

### 多文件上传-不同名字多个文件



1. 定义模板

```
 <form action="/admin/user/doEdit" method="post" enctype="multipart/form-data">
        
        用户名:<input type="text" name="username" placeholder="用户名" />
        <br>
        <br>
        头 像1:<input type="file" name="face1" />
        <br>
        <br>
        头 像2:<input type="file" name="face2" />
        <br>
        <br>

        <input type="submit" value="提交">
    </form>
```



2. 定义业务逻辑

```
func (con UserController) DoEdit(c *gin.Context) {
	username := c.PostForm("username")

	face1, err1 := c.FormFile("face1")
	dst1 := path.Join("./static/upload", face1.Filename)
	if err1 == nil {

		c.SaveUploadedFile(face1, dst1)
	}

	face2, err2 := c.FormFile("face2")
	dst2 := path.Join("./static/upload", face2.Filename)
	if err2 == nil {
		c.SaveUploadedFile(face2, dst2)
	}

	// c.String(200, "执行上传")
	c.JSON(http.StatusOK, gin.H{
		"success":  true,
		"username": username,
		"dst1":     dst1,
		"dst2":     dst2,
	})
}
```





### 多文件上传-相同名字的多个文件

1. 定义模板：

```
 <h2>演示文件上传</h2>

    <form action="/admin/user/doUpload" method="post" enctype="multipart/form-data">
        
        用户名:<input type="text" name="username" placeholder="用户名" />
        <br>
        <br>
        头 像1：<input type="file" name="face[]" />
        <br> <br>
        头 像2：<input type="file" name="face[]" />
        <br> <br>
        头 像3：<input type="file" name="face[]" />
        <br> <br>

        <input type="submit" value="提交">
    </form>
```



2. 定义业务逻辑

```
func (con UserController) DoUpload(c *gin.Context) {
	username := c.PostForm("username")

	form, _ := c.MultipartForm()
	files := form.File["face[]"]

	for _, file := range files {
		dst := path.Join("./static/upload", file.Filename)
		// 上传文件至指定目录
		c.SaveUploadedFile(file, dst)
	}

	c.JSON(http.StatusOK, gin.H{
		"success":  true,
		"username": username,
	})
}
```

##### func (*Context) [MultipartForm](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L585) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.MultipartForm)

```
func (c *Context) MultipartForm() (*multipart.Form, error)
```

MultipartForm is the parsed multipart form, including file uploads.

## gin 中的 Cookie

### Cookie 介绍 

HTTP 是无状态协议。简单地说，当你浏览了一个页面，然后转到同一个网站的另一个页 面，服务器无法认识到这是同一个浏览器在访问同一个网站。每一次的访问，都是没有任何 关系的。如果我们要实现多个页面之间共享数据的话我们就可以使用 Cookie 或者 Session 实现。

cookie 是存储于访问者计算机的浏览器中。可以让我们用同一个浏览器访问同一个域名的时候共享数据。 

### Cookie 能实现的功能 

1. 保持用户登录状态 

2. 保存用户浏览的历史记录 

3. 猜你喜欢，智能推荐 

4. 电商网站的加入购物车 

### 设置和获取 Cookie 

https://gin-gonic.com/zh-cn/docs/examples/cookie/

##### func (*Context) [SetCookie](https://github.com/gin-gonic/gin/blob/v1.8.1/context.go#L871) [¶](https://pkg.go.dev/github.com/gin-gonic/gin#Context.SetCookie)

```
func (c *Context) SetCookie(name, value string, maxAge int, path, domain string, secure, httpOnly bool)
```

SetCookie adds a Set-Cookie header to the ResponseWriter's headers. The provided cookie must have a valid Name. Invalid cookies may be silently dropped.

- 第一个参数 key 
- 第二个参数 value 
- 第三个参数 过期时间.如果只想设置 Cookie 的保存路径而不想设置存活时间，可以在第三个参数中传递 nil 
- 第四个参数 cookie 的路径 
- 第五个参数 cookie 的路径 Domain 作用域 本地调试配置成 localhost , 正式上线配置成域名 
- 第六个参数是 secure ，当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效 
- 第七个参数 httpOnly，是微软对 COOKIE 做的扩展。如果在 COOKIE 中设置了“httpOnly”属性， 则通过程序（JS 脚本、applet 等）将无法读取到 COOKIE 信息，防止 XSS 攻击产生 

##### 获取 cookie

```
cookie,err:=c.Cookie("name")
```

##### 完整 demo

```
func (con DefaultController) News(c *gin.Context) {
	//获取cookie
	username, _ := c.Cookie("username")
	hobby, _ := c.Cookie("hobby")
	c.String(200, "username=%v----hobby=%v", username, hobby)
}

func (con DefaultController) Shop(c *gin.Context) {
	//获取cookie
	username, _ := c.Cookie("username")
	hobby, _ := c.Cookie("hobby")
	c.String(200, "username=%v----hobby=%v", username, hobby)
}
func (con DefaultController) DeleteCookie(c *gin.Context) {
	//删除cookie
	c.SetCookie("username", "张三", -1, "/", "localhost", false, true)
	c.String(200, "删除成功")
}
```

##### 多个二级域名共享 cookie 

1、分别把 a.itying.com 和 b.itying.com 解析到我们的服务器 

host 设置 a.itying.com 127.0.0.1

2、我们想的是用户在 a.itying.com 中设置 Cookie 信息后在 b.itying.com 中获取刚才设置的 cookie，也就是实现多个二级域名共享 cookie 

这时候的话我们就可以这样设置 cookie 

```
c.SetCookie("usrename", "张三", 3600, "/", ".itying.com", false, true)
```

## gin 中的 session

### Session 简单介绍 

session 是另一种记录客户状态的机制，不同的是 Cookie 保存在客户端浏览器中，而 session 保存在服务器上。 

### Session 的工作流程 

当客户端浏览器第一次访问服务器并发送请求时，服务器端会创建一个 session 对象，生成一个类似于 key,value 的键值对，然后将 value 保存到服务器 将 key(cookie)返回到浏览器(客户)端。浏览器下次访问时会携带 key(cookie)，找到对应的session(value)。

### Gin 中使用 Session 

Gin 官方没有给我们提供 Session 相关的文档，这个时候我们可以使用第三方的 Session 中间件来实现。

https://github.com/gin-contrib/sessions 

## Gin 中使用 GORM 操作 mysql 数据库

### GORM 简单介绍 

GORM 是 Golang 的一个 orm 框架。简单说，ORM 就是通过实例对象的语法，完成关系型 数据库的操作的技术，是"对象-关系映射"（Object/Relational Mapping） 的缩写。使用 ORM 框架可以让我们更方便的操作数据库。

### Gin 中使用 GORM 

1. 安装 

如果使用 go mod 管理项目的话可以忽略此步骤 

```
go get -u gorm.io/gorm 

go get -u gorm.io/driver/mysql 
```

2. Gin 中使用 Gorm 连接数据库 

在 models 下面新建 core.go ，建立数据库链接 

```
package models 

import ( 
"fmt" 
"gorm.io/driver/mysql" 
"gorm.io/gorm" 
) 
var DB *gorm.DB 
var err error 
func init() { dsn := "root:123456@tcp(192.168.0.6:3306)/gin?charset=utf8mb4&parseTime=True&loc=L ocal"
DB, err = gorm.Open(mysql.Open(dsn),&gorm.Config{}) 
if err != nil { fmt.Println(err) } 
}
```

3. 定义操作数据库的模型 

Gorm 官方给我们提供了详细的： 

https://gorm.io/zh_CN/docs/models.html 

虽然在 gorm 中可以指定字段的类型以及自动生成数据表，但是在实际的项目开发中，我们 是先设计数据库表，然后去实现编码的

**在实际项目中定义数据库模型注意以下几点：** 

1. **结构体的名称必须首字母大写** ，并和数据库表名称对应。例如：表名称为 user 结构体名称定义成 User，表名称为 article_cate 结构体名称定义成 ArticleCate 

2. 结构体中的**字段名称首字母必须大写**，并和数据库表中的字段一一对应。例如：下面结构体中的 Id 和数据库中的 id 对应,Username 和数据库中的 username 对应，Age 和数据库中的 age 对应，Email 和数据库中的 email 对应，AddTime 和数据库中的 add_time 字段对应 

3. **默认情况表名是结构体名称的复数形式**。如果我们的结构体名称定义成 User，表示这个模型默认操作的是 users 表。 

4. 我们可以使用结构体中的自定义方法 TableName 改变结构体的默认表名称，如下: 

```
func (User) TableName() string { 
	return "user" 
}
```

表示把 User 结构体默认操作的表改为 user 表 
