# 基础知识

## 基本输入输出

我们如何读取用户的键盘（控制台）输入呢？从键盘和标准输入 `os.Stdin` 读取输入，最简单的办法是使用 `fmt` 包提供的 Scan 和 Sscan 开头的函数。请看以下程序：

```go
// 从控制台读取输入:
package main
import "fmt"

var (
   firstName, lastName, s string
   i int
   f float32
   input = "56.12 / 5212 / Go"
   format = "%f / %d / %s"
)

func main() {
   fmt.Println("Please enter your full name: ")
   fmt.Scanln(&firstName, &lastName)
   // fmt.Scanf("%s %s", &firstName, &lastName)
   fmt.Printf("Hi %s %s!\n", firstName, lastName) // Hi Chris Naegels
   fmt.Sscanf(input, format, &f, &i, &s)
   fmt.Println("From the string we read: ", f, i, s)
    // 输出结果: From the string we read: 56.12 5212 Go
}
```

`Scanln` 扫描来自标准输入的文本，将空格分隔的值依次存放到后续的参数内，直到碰到换行。

`Scanf` 与其类似，除了 `Scanf` 的第一个参数用作格式字符串，用来决定如何读取。

`Sscan` 和以 `Sscan` 开头的函数则是从字符串读取，除此之外，与 `Scanf` 相同。如果这些函数读取到的结果与您预想的不同，您可以检查成功读入数据的个数和返回的错误。

您也可以使用 `bufio` 包提供的缓冲读取（buffered reader）来读取数据，正如以下例子所示：

```go
package main
import (
    "fmt"
    "bufio"
    "os"
)

var inputReader *bufio.Reader
var input string
var err error

func main() {
    inputReader = bufio.NewReader(os.Stdin)
    fmt.Println("Please enter some input: ")
    input, err = inputReader.ReadString('\n')
    if err == nil {
        fmt.Printf("The input was: %s\n", input)
    }
}
```

`inputReader` 是一个指向 `bufio.Reader` 的指针。`inputReader := bufio.NewReader(os.Stdin)` 这行代码，将会创建一个读取器，并将其与标准输入绑定。

`bufio.NewReader()` 构造函数的签名为：`func NewReader(rd io.Reader) *Reader`

该函数的实参可以是满足 `io.Reader` 接口的任意对象（任意包含有适当的 `Read()` 方法的对象，请参考[章节 11.8](https://go.learnku.com/docs/the-way-to-go/118-second-examples-reading-and-writing/98)），函数返回一个新的带缓冲的 `io.Reader` 对象，它将从指定读取器（例如 `os.Stdin`）读取内容。

返回的读取器对象提供一个方法 `ReadString(delim byte)`，该方法从输入中读取内容，直到碰到 `delim` 指定的字符，然后将读取到的内容连同 `delim` 字符一起放到缓冲区。

`ReadString` 返回读取到的字符串，如果碰到错误则返回 `nil`。如果它一直读到文件结束，则返回读取到的字符串和 `io.EOF`。如果读取过程中没有碰到 `delim` 字符，将返回错误 `err != nil`。

在上面的例子中，我们会读取键盘输入，直到回车键（\n）被按下。

屏幕是标准输出 `os.Stdout`；`os.Stderr` 用于显示错误信息，大多数情况下等同于 `os.Stdout`。

一般情况下，我们会省略变量声明，而使用 `:=`，例如：

## 列表

在Go语言中，列表使用 `container/list` 包来实现，内部的实现原理是双链表，列表能够高效地进行任意位置的元素插入和删除操作。

### 初始化列表

list 的初始化有两种方法：分别是使用 New() 函数和 var 关键字声明，两种方法的初始化效果都是一致的。

1) 通过 container/list 包的 New() 函数初始化 list

`变量名 := list.New()`

2) 通过 var 关键字声明初始化 list

`var 变量名 list.List`

列表与切片和 map 不同的是，列表并没有具体元素类型的限制，因此，列表的元素可以是任意类型，这既带来了便利，也引来一些问题，例如给列表中放入了一个 interface{} 类型的值，取出值后，如果要将 interface{} 转换为其他类型将会发生宕机。

### 在列表中插入元素

双链表支持从队列前方或后方插入元素，分别对应的方法是 `PushFront` 和 `PushBack`。

#### 提示

这两个方法都会返回一个 `*list.Element` 结构，如果在以后的使用中需要删除插入的元素，则只能通过 `*list.Element` 配合 `Remove()` 方法进行删除，这种方法可以让删除更加效率化，同时也是双链表特性之一。

下面代码展示如何给 list 添加元素：

```go
l := list.New()

l.PushBack("fist")
l.PushFront(67)
```

代码说明如下：

- 第 1 行，创建一个列表实例。
- 第 3 行，将 fist 字符串插入到列表的尾部，此时列表是空的，插入后只有一个元素。
- 第 4 行，将数值 67 放入列表，此时，列表中已经存在 fist 元素，67 这个元素将被放在 fist 的前面。


列表插入元素的方法如下表所示。



| 方  法                                                | 功  能                                            |
| ----------------------------------------------------- | ------------------------------------------------- |
| InsertAfter(v interface {}, mark * Element) * Element | 在 mark 点之后插入元素，mark 点由其他插入函数提供 |
| InsertBefore(v interface {}, mark * Element) *Element | 在 mark 点之前插入元素，mark 点由其他插入函数提供 |
| PushBackList(other *List)                             | 添加 other 列表元素到尾部                         |
| PushFrontList(other *List)                            | 添加 other 列表元素到头部                         |

### 从列表中删除元素

列表插入函数的返回值会提供一个 `*list.Element` 结构，这个结构记录着列表元素的值以及与其他节点之间的关系等信息，从列表中删除元素时，需要用到这个结构进行快速删除。

列表操作元素：

```go
package mainimport "container/list"
func main() {    

  l := list.New()    
 // 尾部添加    
	l.PushBack("canon")    
// 头部添加    
	l.PushFront(67)    
  // 尾部添加后保存元素句柄    
	element := l.PushBack("fist")    
  // 在fist之后添加high    
	l.InsertAfter("high", element)    
  // 在fist之前添加noon    
	l.InsertBefore("noon", element)    
  // 使用    
	l.Remove(element)}
```

代码说明如下：
第 6 行，创建列表实例。
第 9 行，将字符串 canon 插入到列表的尾部。
第 12 行，将数值 67 添加到列表的头部。
第 15 行，将字符串 fist 插入到列表的尾部，并将这个元素的内部结构保存到 element 变量中。
第 18 行，使用 element 变量，在 element 的位置后面插入 high 字符串。
第 21 行，使用 element 变量，在 element 的位置前面插入 noon 字符串。
第 24 行，移除 element 变量对应的元素。

下表中展示了每次操作后列表的实际元素情况。



| 操作内容                        | 列表元素                    |
| ------------------------------- | --------------------------- |
| l.PushBack("canon")             | canon                       |
| l.PushFront(67)                 | 67, canon                   |
| element := l.PushBack("fist")   | 67, canon, fist             |
| l.InsertAfter("high", element)  | 67, canon, fist, high       |
| l.InsertBefore("noon", element) | 67, canon, noon, fist, high |
| l.Remove(element)               | 67, canon, noon, high       |

### 遍历列表——访问列表的每一个元素

遍历双链表需要配合 Front() 函数获取头元素，遍历时只要元素不为空就可以继续进行，每一次遍历都会调用元素的 Next() 函数，代码如下所示。

```
l := list.New()
// 尾部添加
l.PushBack("canon")
// 头部添加
l.PushFront(67)
for i := l.Front(); i != nil; i = i.Next() {    
fmt.Println(i.Value)
}
```

代码输出如下：

67
canon

代码说明如下：

- 第 1 行，创建一个列表实例。
- 第 4 行，将 canon 放入列表尾部。
- 第 7 行，在队列头部放入 67。
- 第 9 行，使用 for 语句进行遍历，其中 i:=l.Front() 表示初始赋值，只会在一开始执行一次，每次循环会进行一次 i != nil 语句判断，如果返回 false，表示退出循环，反之则会执行 i = i.Next()。
- 第 10 行，使用遍历返回的 *list.Element 的 Value 成员取得放入列表时的原值。

## strings包

##### func [TrimSpace](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#613)

```
func TrimSpace(s string) string
```

返回将s前后端所有空白（unicode.IsSpace指定）都去掉的字符串。



## 常用库

### 切片

go 通过切片模拟栈和队列

栈

```
// 创建栈

stack:=make([]int,0)

// push压入

stack=append(stack,10)

// pop弹出

v:=stack[len(stack)-1]

stack=stack[:len(stack)-1]

// 检查栈空

len(stack)==0
```

队列

```
// 创建队列

queue:=make([]int,0)

// enqueue入队

queue=append(queue,10)

// dequeue出队

v:=queue[0]

queue=queue[1:]

// 长度0为空

len(queue)==0
```

注意点

- 参数传递，只能修改，不能新增或者删除原始数据
- 默认 s=s[0:len(s)]，取下限不取上限，数学表示为：[)

### 字典

基本用法

```
// 创建

m:=make(map[string]int)

// 设置kv

m["hello"]=1

// 删除k

delete(m,"hello")

// 遍历

for k,v:=range m{
  println(k,v)
}


```

注意点

- map 键需要可比较，不能为 slice、map、function
- map 值都有默认值，可以直接操作默认值，如：m[age]++ 值由 0 变为 1
- 比较两个 map 需要遍历，其中的 kv 是否相同，因为有默认值关系，所以需要检查 val 和 ok 两个值

### 标准库

sort

```
// int排序

sort.Ints([]int{})

// 字符串排序

sort.Strings([]string{})

// 自定义排序

sort.Slice(s,func(i,j int)bool{return s[i]<s[j]})
```

math

```
// int32 最大最小值

math.MaxInt32 // 实际值：1<<31-1

math.MinInt32 // 实际值：-1<<31

// int64 最大最小值（int默认是int64）

math.MaxInt64

math.MinInt64
```

copy

/

```
/ 删除a[i]，可以用 copy 将i+1到末尾的值覆盖到i,然后末尾-1

copy(a[i:],a[i+1:])

a=a[:len(a)-1]



// make创建长度，则通过索引赋值

a:=make([]int,n)

a[n]=x

// make长度为0，则通过append()赋值

a:=make([]int,0)

a=append(a,x)
```



### 常用技巧

类型转换

```
// byte转数字

s="12345"  // s[0] 类型是byte

num:=int(s[0]-'0') // 1

str:=string(s[0]) // "1"

b:=byte(num+'0') // '1'

fmt.Printf("%d%s%c\n", num, str, b) // 111
// 字符串转数字

num,_:=strconv.Atoi()

str:=strconv.Itoa()
```





### 刷题注意点

- leetcode 中，全局变量不要当做返回值，否则刷题检查器会报错



# 基础数据结构

## 数组

数组本质上是一段连续的存储区域，可以通过计算地址偏移快速访问里面的任意元素（即"随机访问"）。因为要保持连续，所以数组的扩容往往需要通过先申请新空间再拷贝的方法实现，比较奢侈。

```go
// type any = interface{}
// any is an alias for interface{} and is equivalent to interface{} in all ways.
// Package list implements a doubly linked list.
func InsertTo[T any](list []T,pos int,val T)[]T{
	if pos<0||pos>len(list){
		panic("illegal pos")
	}
	list=append(list, val)
	for i := len(list)-1; i > pos; i-- {
		list[i]=list[i-1]
		
	}
	list[pos]=val
	return list
}

func EraseForm[T any](list []T,pos int,keepOrder bool)[] T{
	if pos<0||pos>=len(list){
		panic("illegal pos")	
	}
	last :=len(list)-1
	if keepOrder{
		for i := pos; i < last; i++ {
			list[i]=list[i+1]
		}
	}else {
		list[pos]=list[last]
	}
	return list[:last]
}

```

对于无序数组，插入和删除都是很方便的（O(1)时间），但对于有序数组则需要一番腾挪（O(N)时间）。

### 二分查找

有序数组在查找上有特殊优势，可以在O(logN)的时间内完成：

看看就好，现在没有 constraints了

```go
func Search[T constraints.Ordered](list []T, key T) int {
    for a, b := 0, len(list); a < b; {
        m := (a+b) / 2
        switch {
            case key > list[m]: a = m + 1
            case key < list[m]: b = m
            default: return m           
        }
    }                           //寻找key的位置
    return -1                   //没有则返回-1
}
```

### 环状队列

[![img](http://typorapictureload.oss-cn-shanghai.aliyuncs.com/uPic/CyclicQueue.png)](https://github.com/PeterRK/DSGO/blob/master/book/images/CyclicQueue.png)

可以基于数组实现队列，这种队列呈环状，实际可容纳元素比底层的数组少一个。

```go
type queue[T any] struct {
    r, w  int       //读写游标
    space []T       //底层数组
}

func (q *queue[T]) init(size int) {
    if size < 7 {
        size = 7
    }
    q.space = make([]T, size+1)
    q.r, q.w = 0, 0
}

func (q *queue[T]) IsEmpty() bool {
    return q.r == q.w
}

func (q *queue[T]) IsFull() bool {
    return (q.w+1)%len(q.space) == q.r
}
```



## 栈

```
// Stack Linked-List
// description: based on `geeksforgeeks` description Stack is a linear data structure which follows a particular order in which the operations are performed.
//	The order may be LIFO(Last In First Out) or FILO(First In Last Out).
// details:
// 	Stack Data Structure : https://www.geeksforgeeks.org/stack-data-structure-introduction-program/
// 	Stack (abstract data type) : https://en.wikipedia.org/wiki/Stack_(abstract_data_type)
// author [Milad](https://github.com/miraddo)
// see stacklinkedlistwithlist.go, stackarray.go, stack_test.go

package stack

// Node structure
type Node struct {
	Val  any
	Next *Node
}

// Stack has jost top of node and with length
type Stack struct {
	top    *Node
	length int
}

// push add value to last index
func (ll *Stack) push(n any) {
	newStack := &Node{} // new node

	newStack.Val = n
	newStack.Next = ll.top

	ll.top = newStack
	ll.length++
}

// pop remove last item as first output
func (ll *Stack) pop() any {
	result := ll.top.Val
	if ll.top.Next == nil {
		ll.top = nil
	} else {
		ll.top.Val, ll.top.Next = ll.top.Next.Val, ll.top.Next.Next
	}

	ll.length--
	return result
}

// isEmpty to check our array is empty or not
func (ll *Stack) isEmpty() bool {
	return ll.length == 0
}

// len use to return length of our stack
func (ll *Stack) len() int {
	return ll.length
}

// peak return last input value
func (ll *Stack) peak() any {
	return ll.top.Val
}

// show all value as an interface array
func (ll *Stack) show() (in []any) {
	current := ll.top

	for current != nil {
		in = append(in, current.Val)
		current = current.Next
	}
	return
}
```



## 树



### 二叉树遍历

```
type TreeNode struct {
	val         int
	Left, Right *TreeNode
}
```



#### 前序遍历

*递归*

```
// preorder traversal
func preorderTraversal(root *TreeNode) {
	if root == nil {
		return
	}
	
	//visit the root
	//and then visit the left and right nodes
	fmt.Println(root.val)
	
	preorderTraversal(root.Left)
	preorderTraversal(root.Right)
}
```

*非递归*

```
func preorderTraversal1(root *TreeNode) []int {
	if root == nil {
		return nil
	}
	result := make([]int, 0)
	stack := make([]*TreeNode, 0)
	
	for root != nil || len(stack) != 0 {
		for root != nil {
			// Preorder traversal, so save the result first
			result = append(result, root.val)
			stack = append(stack, root)
			root = root.Left
		}
		
		// pop
		node := stack[len(stack)-1]
		stack = stack[:len(stack)-1]
		root = node.Right
	}
	return result
}
```

#### 中序遍历

*中序非递归*

```
// 思路：通过stack 保存已经访问的元素，用于原路返回
func inorderTraversal(root *TreeNode) []int {
	result := make([]int, 0)
	if root == nil {
		return result
	}
	stack := make([]*TreeNode, 0)
	for len(stack) > 0 || root != nil {
		for root != nil {
			stack = append(stack, root)
			root = root.Left // 一直向左
		}
		// 弹出
		val := stack[len(stack)-1]
		stack = stack[:len(stack)-1]
		result = append(result, val.Val)
		root = val.Right
	}
	return result
}

```

#### 后序遍历

*后序非递归*

```
func postorderTraversal(root *TreeNode) []int {
	// 通过lastVisit标识右子节点是否已经弹出
	if root == nil {
		return nil
	}
	result := make([]int, 0)
	stack := make([]*TreeNode, 0)
	var lastVisit *TreeNode
	for root != nil || len(stack) != 0 {
		for root != nil {
			stack = append(stack, root)
			root = root.Left
		}
		// 这里先看看，先不弹出
		node := stack[len(stack)-1]
		// 根节点必须在右节点弹出之后，再弹出
		if node.Right == nil || node.Right == lastVisit {
			stack = stack[:len(stack)-1] // pop
			result = append(result, node.Val)
			// 标记当前这个节点已经弹出过
			lastVisit = node
		} else {
			root = node.Right
		}
	}
	return result
}
```

注意点

- 核心就是：根节点必须在右节点弹出之后，再弹出



#### 遍历模板

```
// 使用指针避免每次新建切片
func traversal(root *TreeNode, result *[]int) {
	if root == nil {
		return
	}
	traversal(root.Left, result)
	traversal(root.Right, result)
	*result = append(*result, root.Val)
}

func postorderTraversal(root *TreeNode) []int {
	result := make([]int, 0)
	traversal(root, &result)
	return result
}
```



使用立即函数：

```
func PreorderTraversal(root *TreeNode) (res []int) {
    var traversal func(node *TreeNode)
    traversal = func(node *TreeNode) {
	if node == nil {
            return
	}
	res = append(res,node.Val)
	traversal(node.Left)
	traversal(node.Right)
    }
    traversal(root)
    return res
}

```



### 搜索

#### DFS 深度优先搜索

```
type TreeNode struct {
	Val   int
	Left  *TreeNode
	Right *TreeNode
}

func preorderTraversal(root *TreeNode) []int {
	result := make([]int, 0)
	dfs(root, &result)
	return result
}

// V1：深度遍历，结果指针作为参数传入到函数内部
func dfs(root *TreeNode, result *[]int) {
	if root == nil {
		return
	}
	*result = append(*result, root.Val)
	dfs(root.Left, result)
	dfs(root.Right, result)
}

```

##### 从下向上（分治法）

```
package main

type TreeNode struct {
	Val         int
	Left, Right *TreeNode
}

// postorderTraversal
func preorderTraversal(root *TreeNode) []int {
	result := divideAndConquer(root)
	return result
}

func divideAndConquer(root *TreeNode) []int {
	// 新建切片存结果
	// Create a new slice to save the result
	result := make([]int, 0)
	
	// return conditions (null & leaf)
	if root == nil {
		return result
	}
	
	//分治 (divide)
	left := divideAndConquer(root.Left)
	right := divideAndConquer(root.Right)
	
	// 合并结果
	//merge the results
	result = append(result, root.Val)
	result = append(result, left...)
	result = append(result, right...)
	return result
}

```



<u>DFS 深度搜索（从上到下） 和分治法区别：前者一般将最终结果通过指针参数传入，后者一般递归返回结果最后合并</u>

#### BFS 层次遍历



```
func levelOrder(root *TreeNode) [][]int {
    // 通过上一层的长度确定下一层的元素
    result := make([][]int, 0)
    if root == nil {
        return result
    }
    queue := make([]*TreeNode, 0)
    queue = append(queue, root)
    for len(queue) > 0 {
        list := make([]int, 0)
        // 为什么要取length？
        // 记录当前层有多少元素（遍历当前层，再添加下一层）
        l := len(queue)
        for i := 0; i < l; i++ {
            // 出队列
            level := queue[0]
            queue = queue[1:]
            list = append(list, level.Val)
            if level.Left != nil {
                queue = append(queue, level.Left)
            }
            if level.Right != nil {
                queue = append(queue, level.Right)
            }
        }
        result = append(result, list)
    }
    return result
}
```

分治法应用

先分别处理局部，再合并结果

适用场景

- 快速排序
- 归并排序
- 二叉树相关问题

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

```
func traversal(root *TreeNode) ResultType  {
    // nil or leaf
    if root == nil {
        // do something and return
    }

    // Divide
    ResultType left = traversal(root.Left)
    ResultType right = traversal(root.Right)

    // Conquer
    ResultType result = Merge from left and right

    return result
}
```



### 基础习题

[Maxmum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

##### 思路

使用递归，如果根结点是空深度是0,如果根结点是叶子结点深度是1。

如果不是叶子结点肯定有子树，就返回子树里面的最大深度+1

注意：为什么要判断叶子结点停止递归而不只判定根结点为空停止递归呢？

因为判定叶子结点停止就会在叶子结点处停止递归，而判定空结点再停止

则需要递归到叶子结点的子树(都是空)才停止，就多算了很多空结点的深度，浪费内存。

```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func maxDepth(root *TreeNode) int {
	if root == nil{
		return 0
	}
	if root.Left == nil && root.Right == nil{
		return 1
	}
	leftDepth := maxDepth(root.Left)
	rightDepth := maxDepth(root.Right)
	if leftDepth > rightDepth{
		return 1 + leftDepth
	}else{
		return 1 + rightDepth
	}

}
```



# 常用方法

## 递归

主要是对递归不成体系，没有方法论，每次写递归算法 ，都是靠玄学来写代码，代码能不能编过都靠运气。

本篇将介绍前后中序的递归写法，一些同学可能会感觉很简单，其实不然，我们要通过简单题目把方法论确定下来，有了方法论，后面才能应付复杂的递归。

**这里帮助大家确定下来递归算法的三个要素。**每次写递归，都按照这三要素来写，可以保证大家写出正确的递归算法！

1. **确定递归函数的参数和返回值：**
   确定*哪些参数是递归的过程中需要处理的，那么就在递归函数里加上这个参数*， 并且还要明确每次递归的返回值是什么进而确定递归函数的返回类型。

2. **确定终止条件：**
   写完了递归算法, 运行的时候，经常会遇到栈溢出的错误，就是*没写终止条件或者终止条件写的不对*，操作系统也是用一个栈的结构来保存每一层递归的信息，如果递归没有终止，操作系统的内存栈必然就会溢出。

3. **确定单层递归的逻辑：**
   *确定每一层递归需要处理的信息*。在这里也就会重复调用自己来实现递归的过程。

好了，我们确认了递归的三要素，接下来就来练练手：

以下以前序遍历为例：

1. **确定递归函数的参数和返回值**：因为要打印出前序遍历节点的数值，所以参数里需要传入vector在放节点的数值，除了这一点就不需要在处理什么数据了也不需要有返回值，所以递归函数返回类型就是void，代码如下：

```
void traversal(TreeNode* cur, vector<int>& vec)
```



2. **确定终止条件：**在递归的过程中，如何算是递归结束了呢，当然是当前遍历的节点是空了，那么本层递归就要要结束了，所以如果当前遍历的这个节点是空，就直接return，代码如下：

```
if (cur == NULL) return;
```

3. **确定单层递归的逻辑**：前序遍历是中左右的循序，所以在单层递归的逻辑，是要先取中节点的数值，代码如下：

```
vec.push_back(cur->val);    // 中
traversal(cur->left, vec);  // 左
traversal(cur->right, vec); // 右
```

单层递归的逻辑就是按照中左右的顺序来处理的，这样二叉树的前序遍历，基本就写完了，在看一下完整代码：

前序遍历：

```
class Solution {
public:
    void traversal(TreeNode* cur, vector<int>& vec) {
        if (cur == NULL) return;
        vec.push_back(cur->val);    // 中
        traversal(cur->left, vec);  // 左
        traversal(cur->right, vec); // 右
    }
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        traversal(root, result);
        return result;
    }
};
```

Go：

```
func PreorderTraversal(root *TreeNode) (res []int) {
    var traversal func(node *TreeNode)
    traversal = func(node *TreeNode) {
	if node == nil {
            return
	}
	res = append(res,node.Val)
	traversal(node.Left)
	traversal(node.Right)
    }
    traversal(root)
    return res
}

```

那么前序遍历写出来之后，中序和后序遍历就不难理解了，代码如下：

中序遍历：


    void traversal(TreeNode* cur, vector<int>& vec) {
        if (cur == NULL) return;
        traversal(cur->left, vec);  // 左
        vec.push_back(cur->val);    // 中
        traversal(cur->right, vec); // 右
    }
    
    func InorderTraversal(root *TreeNode) (res []int) {
        var traversal func(node *TreeNode)
        traversal = func(node *TreeNode) {
    	if node == nil {
    	    return
    	}
    	traversal(node.Left)
    	res = append(res,node.Val)
    	traversal(node.Right)
        }
        traversal(root)
        return res
    }
    
后序遍历：


    void traversal(TreeNode* cur, vector<int>& vec) {
        if (cur == NULL) return;
        traversal(cur->left, vec);  // 左
        traversal(cur->right, vec); // 右
        vec.push_back(cur->val);    // 中
    }
    
    func PostorderTraversal(root *TreeNode) (res []int) {
        var traversal func(node *TreeNode)
        traversal = func(node *TreeNode) {
    	if node == nil {
    	    return
    	}
    	traversal(node.Left)
    	traversal(node.Right)
            res = append(res,node.Val)
        }
        traversal(root)
        return res
    }
此时大家可以做一做leetcode上三道题目，分别是：

- 144.二叉树的前序遍历
- 145.二叉树的后序遍历
- 94.二叉树的中序遍历

可能有同学感觉前后中序遍历的递归太简单了，要打迭代法（非递归），别急，我们明天打迭代法，打个通透！

### 迭代法

为什么可以用迭代法（非递归的方式）来实现二叉树的前后中序遍历呢？

**递归的实现就是：每一次递归调用都会把函数的局部变量、参数值和返回地址等压入调用栈中，**然后递归返回的时候，从栈顶弹出上一次递归的各项参数，所以这就是递归为什么可以返回上一层位置的原因。

此时大家应该知道我们用栈也可以是实现二叉树的前后中序遍历了。

#### 前序遍历（迭代法）

我们先看一下前序遍历。

前序遍历是中左右，每次先处理的是中间节点，那么先将跟节点放入栈中，然后将右孩子加入栈，再加入左孩子。

*为什么要先加入右孩子，再加入左孩子呢？ 因为这样出栈的时候才是中左右的顺序。*

动画如下：

![二叉树前序遍历（迭代法）.gif](http://typorapictureload.oss-cn-shanghai.aliyuncs.com/uPic/1600934720-bMXWmu-%E4%BA%8C%E5%8F%89%E6%A0%91%E5%89%8D%E5%BA%8F%E9%81%8D%E5%8E%86%EF%BC%88%E8%BF%AD%E4%BB%A3%E6%B3%95%EF%BC%89.gif)

不难写出如下代码: （*注意代码中空节点不入栈*）

```
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if (root == NULL) return result;
        st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();                       // 中
            st.pop();
            result.push_back(node->val);
            if (node->right) st.push(node->right);           // 右（空节点不入栈）
            if (node->left) st.push(node->left);             // 左（空节点不入栈）
        }
        return result;
    }
};
```

统一写法：

```
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        if (root != NULL) st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();
            if (node != NULL) {
                st.pop();
                if (node->right) st.push(node->right);  // 右
                if (node->left) st.push(node->left);    // 左
                st.push(node);                          // 中
                st.push(NULL);
            } else {
                st.pop();
                node = st.top();
                st.pop();
                result.push_back(node->val);
            }
        }
        return result;
    }
};

```

Go语言版：

```
//迭代法前序遍历
/**
 type Element struct {
    // 元素保管的值
    Value interface{}
    // 内含隐藏或非导出字段
}

func (l *List) Back() *Element
前序遍历：中左右
压栈顺序：右左中
 **/
func preorderTraversal1(root *TreeNode) []int {
	if root == nil {
		return nil
	}
	stack := list.New()
	
	stack.PushBack(root.Right)
	stack.PushBack(root.Left)
	res := make([]int, 0)
	res = append(res, root.Val)
	
	for stack.Len() > 0 {
		e := stack.Back()
		stack.Remove(e)
		node := e.Value.(*TreeNode) //e是Element类型，其值为e.Value.由于Value为接口，所以要断言
		if node == nil {
			continue
		}
		res = append(res, node.Val)
		stack.PushBack(node.Right)
		stack.PushBack(node.Left)
	}
	return res
}
```

统一写法：

```
/**
 type Element struct {
    // 元素保管的值
    Value interface{}
    // 内含隐藏或非导出字段
}

func (l *List) Back() *Element 
前序遍历：中左右
压栈顺序：右左中
 **/
func preorderTraversal(root *TreeNode) []int {
	if root == nil {
		return nil
	}
	var stack = list.New()//栈
    res:=[]int{}//结果集
    stack.PushBack(root)
    var node *TreeNode
    for stack.Len()>0{
        e := stack.Back()
        stack.Remove(e)//弹出元素
        if e.Value==nil{// 如果为空，则表明是需要处理中间节点
            e=stack.Back()//弹出元素（即中间节点）
            stack.Remove(e)//删除中间节点
            node=e.Value.(*TreeNode)
            res=append(res,node.Val)//将中间节点加入到结果集中
            continue//继续弹出栈中下一个节点
        }
        node = e.Value.(*TreeNode)
        //压栈顺序：右左中
        if node.Right!=nil{
            stack.PushBack(node.Right)
        }
        if node.Left!=nil{
            stack.PushBack(node.Left)
        }
        stack.PushBack(node)//中间节点压栈后再压入nil作为中间节点的标志符
        stack.PushBack(nil)
    }
    return res

}
```

此时会发现貌似使用迭代法写出前序遍历并不难，确实不难。

此时是不是想改一点前序遍历代码顺序就把中序遍历搞出来了？

其实还真不行！

但接下来，再用迭代法写中序遍历的时候，会发现套路又不一样了，目前的前序遍历的逻辑无法直接应用到中序遍历上。

#### 中序遍历（迭代法）

为了解释清楚，我说明一下 刚刚在迭代的过程中，其实我们有两个操作：

1. **处理：将元素放进result数组中**

2. **访问：遍历节点**

分析一下为什么刚刚写的前序遍历的代码，不能和中序遍历通用呢，因为前序遍历的顺序是中左右，先访问的元素是中间节点，要处理的元素也是中间节点，所以刚刚才能写出相对简洁的代码，**因为要访问的元素和要处理的元素顺序是一致的，都是中间节点。**

那么再看看中序遍历，中序遍历是左中右，先访问的是二叉树顶部的节点，然后一层一层向下访问，直到到达树左面的最底部，再开始处理节点（也就是在把节点的数值放进result数组中），这就造成了处理顺序和访问顺序是不一致的。

**那么在使用迭代法写中序遍历，就需要借用指针的遍历来帮助访问节点，栈则用来处理节点上的元素。**

中序遍历，可以写出如下代码：

```
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        TreeNode* cur = root;
        while (cur != NULL || !st.empty()) {
            if (cur != NULL) { // 指针来访问节点，访问到最底层
                st.push(cur); // 将访问的节点放进栈
                cur = cur->left;                // 左
            } else {
                cur = st.top(); // 从栈里弹出的数据，就是要处理的数据（放进result数组里的数据）
                st.pop();
                result.push_back(cur->val);     // 中
                cur = cur->right;               // 右
            }
        }
        return result;
    }
};
```

统一写法：

```
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        if (root != NULL) st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();
            if (node != NULL) {
                st.pop(); // 将该节点弹出，避免重复操作，下面再将右中左节点添加到栈中
                if (node->right) st.push(node->right);  // 添加右节点（空节点不入栈）

                st.push(node);                          // 添加中节点
                st.push(NULL); // 中节点访问过，但是还没有处理，加入空节点做为标记。

                if (node->left) st.push(node->left);    // 添加左节点（空节点不入栈）
            } else { // 只有遇到空节点的时候，才将下一个节点放进结果集
                st.pop();           // 将空节点弹出
                node = st.top();    // 重新取出栈中元素
                st.pop();
                result.push_back(node->val); // 加入到结果集
            }
        }
        return result;
    }
};

```

Go 语言版

```
//迭代法中序遍历
func inorderTraversal(root *TreeNode) []int {
    rootRes:=[]int{}
    if root==nil{
       return nil
    }
    stack:=list.New()
    node:=root
    //先将所有左节点找到，加入栈中
    for node!=nil{
        stack.PushBack(node)
        node=node.Left
    }
    //其次对栈中的每个节点先弹出加入到结果集中，再找到该节点的右节点的所有左节点加入栈中
    for stack.Len()>0{
        e:=stack.Back()
        node:=e.Value.(*TreeNode)
        stack.Remove(e)
        //找到该节点的右节点，再搜索他的所有左节点加入栈中
        rootRes=append(rootRes,node.Val)
        node=node.Right
        for node!=nil{
            stack.PushBack(node)
            node=node.Left
        }
    }
    return rootRes
}

```

统一写法：

```
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
 //中序遍历：左中右
 //压栈顺序：右中左
func inorderTraversal(root *TreeNode) []int {
    if root==nil{
       return nil
    }
    stack:=list.New()//栈
    res:=[]int{}//结果集
    stack.PushBack(root)
    var node *TreeNode
    for stack.Len()>0{
        e := stack.Back()
        stack.Remove(e)
        if e.Value==nil{// 如果为空，则表明是需要处理中间节点
            e=stack.Back()//弹出元素（即中间节点）
            stack.Remove(e)//删除中间节点
            node=e.Value.(*TreeNode)
            res=append(res,node.Val)//将中间节点加入到结果集中
            continue//继续弹出栈中下一个节点
        }
        node = e.Value.(*TreeNode)
        //压栈顺序：右中左
        if node.Right!=nil{
            stack.PushBack(node.Right)
        }
        stack.PushBack(node)//中间节点压栈后再压入nil作为中间节点的标志符
        stack.PushBack(nil)
        if node.Left!=nil{
            stack.PushBack(node.Left)
        }
    }
    return res
}

```



#### 后序遍历（迭代法）

再来看后序遍历，先序遍历是中左右，后续遍历是左右中，那么我们只需要调整一下先序遍历的代码顺序，就变成中右左的遍历顺序，然后在反转result数组，输出的结果顺序就是左右中了，如下图：



所以后序遍历只需要前序遍历的代码稍作修改就可以了，代码如下：

```
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> result;
        if (root == NULL) return result;
        st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();
            st.pop();
            result.push_back(node->val);
            if (node->left) st.push(node->left); // 相对于前序遍历，这更改一下入栈顺序 （空节点不入栈）
            if (node->right) st.push(node->right); // 空节点不入栈
        }
        reverse(result.begin(), result.end()); // 将结果反转之后就是左右中的顺序了
        return result;
    }
};
```

统一写法：

```
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        if (root != NULL) st.push(root);
        while (!st.empty()) {
            TreeNode* node = st.top();
            if (node != NULL) {
                st.pop();
                st.push(node);                          // 中
                st.push(NULL);

                if (node->right) st.push(node->right);  // 右
                if (node->left) st.push(node->left);    // 左

            } else {
                st.pop();
                node = st.top();
                st.pop();
                result.push_back(node->val);
            }
        }
        return result;
    }
};

```



Go 语言版

```
//迭代法后序遍历
//后续遍历：左右中
//压栈顺序：中右左(按照前序遍历思路)，再反转结果数组
func postorderTraversal(root *TreeNode) []int {
	if root == nil {
		return nil
	}
	var stack = list.New()
    stack.PushBack(root.Left)
    stack.PushBack(root.Right)
    res:=[]int{}
    res=append(res,root.Val)
    for stack.Len()>0 {
        e:=stack.Back()
        stack.Remove(e)
        node := e.Value.(*TreeNode)//e是Element类型，其值为e.Value.由于Value为接口，所以要断言
        if node==nil{
            continue
        }
        res=append(res,node.Val)
        stack.PushBack(node.Left)
        stack.PushBack(node.Right)
    }
    for i:=0;i<len(res)/2;i++{
        res[i],res[len(res)-i-1] = res[len(res)-i-1],res[i]
    }
    return res
}
```

统一写法：

```
//后续遍历：左右中
//压栈顺序：中右左
func postorderTraversal(root *TreeNode) []int {
	if root == nil {
		return nil
	}
	var stack = list.New()//栈
    res:=[]int{}//结果集
    stack.PushBack(root)
    var node *TreeNode
    for stack.Len()>0{
        e := stack.Back()
        stack.Remove(e)
        if e.Value==nil{// 如果为空，则表明是需要处理中间节点
            e=stack.Back()//弹出元素（即中间节点）
            stack.Remove(e)//删除中间节点
            node=e.Value.(*TreeNode)
            res=append(res,node.Val)//将中间节点加入到结果集中
            continue//继续弹出栈中下一个节点
        }
        node = e.Value.(*TreeNode)
        //压栈顺序：中右左
        stack.PushBack(node)//中间节点压栈后再压入nil作为中间节点的标志符
        stack.PushBack(nil)
        if node.Right!=nil{
            stack.PushBack(node.Right)
        }
        if node.Left!=nil{
            stack.PushBack(node.Left)
        }
    }
    return res
}
```

此时我们实现了前后中遍历的三种迭代法，是不是发现迭代法实现的先中后序，其实风格也不是那么统一，除了先序和后序，有关联，中序完全就是另一个风格了，一会用栈遍历，一会又用指针来遍历。

二叉树前中后迭代方式统一写法
迭代法实现的先中后序，其实风格也不是那么统一，除了先序和后序，有关联，中序完全就是另一个风格了，一会用栈遍历，一会又用指针来遍历。

实践过的同学，也会发现使用迭代法实现先中后序遍历，很难写出统一的代码，不像是递归法，实现了其中的一种遍历方式，其他两种只要稍稍改一下节点顺序就可以了。

其实针对三种遍历方式，使用迭代法是可以写出统一风格的代码！

重头戏来了，接下来介绍一下统一写法。

我们以中序遍历为例，无法同时解决访问节点（遍历节点）和处理节点（将元素放进结果集）不一致的情况。

那我们就将访问的节点放入栈中，把要处理的节点也放入栈中但是要做标记。

如何标记呢，就是要处理的节点放入栈之后，紧接着放入一个空指针作为标记。 这种方法也可以叫做标记法。

迭代法中序遍历
中序遍历代码如下：（详细注释）

    class Solution {
    public:
        vector<int> inorderTraversal(TreeNode* root) {
            vector<int> result;
            stack<TreeNode*> st;
            if (root != NULL) st.push(root);
            while (!st.empty()) {
                TreeNode* node = st.top();
                if (node != NULL) {
                    st.pop(); // 将该节点弹出，避免重复操作，下面再将右中左节点添加到栈中
                    if (node->right) st.push(node->right);  // 添加右节点（空节点不入栈）
    st.push(node);                          // 添加中节点
                st.push(NULL); // 中节点访问过，但是还没有处理，加入空节点做为标记。
    
                if (node->left) st.push(node->left);    // 添加左节点（空节点不入栈）
            } else { // 只有遇到空节点的时候，才将下一个节点放进结果集
                st.pop();           // 将空节点弹出
                node = st.top();    // 重新取出栈中元素
                st.pop();
                result.push_back(node->val); // 加入到结果集
            }
        }
        return result;
    }
