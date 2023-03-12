# 基础

## 快排

![image-20230114144141431](https://img.herrluk.icu/typoraPicture/2023-01-14-14:41:41.png)

基于分治思想

1. 确定分界点：`q[l]`、`q[(l+r)/2]`、`q[r]` 或 随机
2. 调整区间：`q[num]<= x`在左侧，`q[num]>= x` 在右侧
3. 递归处理左右两段

代码：

```c++
void quick_sort(int q[], int l, int r) {
  if (l >= r) return;

  int i = l - 1, j = r + 1, x = q[(l + r) >> 1];
  while (i < j) {
    do i++; while (q[i] < x);
    do j--; while (q[j] > x);
    if (i < j) swap(q[i], q[j]);
  }

  quick_sort(q, l, j);
  quick_sort(q, j + 1, r);
}
```

## 归并排序

![image-20230114154949584](https://img.herrluk.icu/typoraPicture/2023-01-14-15:49:49.png)

1. 确定分界点：`mid=(l+r)/2`
2. 递归排序 left、right
3. 归并——合二为一

代码：

```c++
void merge_sort(int q[], int l, int r) {
  if (l >= r) return;

  int mid = l + r >> 1;

  merge_sort(q, l, mid), merge_sort(q, mid + 1, r);

  int k = 0, i = l, j = mid + 1;
  while (i <= mid && j <= r)
    if (q[i] <= q[j]) tmp[k++] = q[i++];
    else tmp[k++] = q[j++];
  while (i <= mid) tmp[k++] = q[i++];
  while (j <= r) tmp[k++] = q[j++];

  for (i = l, j = 0; i <= r; i++, j++) q[i] = tmp[j];
}
```

## 二分

### 整数二分



![image-20230115202630645](https://img.herrluk.icu/typoraPicture/2023-01-15-20:26:30.png)



> 二分的本质：
>
> **假设区间里可以找到某个性质，把区间划分为一半满足一半不满足，那么我们可以用二分找到性质的边界点。**

情况 1：（上图的红色部分）

给出某时刻的 mid，然后每次判断是否满足性质，满足的话 `l` 在右半区间。否则在左半区间。

情况 2：（上图的绿色部分）

给出某时刻的 mid，然后每次判断是否满足性质，满足的话 `l` 在右半区间。否则在左半区间。

二分模板一共有两个，分别适用于不同情况。

> 算法思路：假设目标值在闭区间[l, r]中， 每次将区间长度缩小一半，当l = r时，我们就找到了目标值。
>
> **有单调性一定可以二分，没有单调性也可能可以二分！**

版本1
当我们将区间`[l, r]`划分成`[l, mid]`和`[mid + 1, r]`时，其更新操作是`r = mid`或者`l = mid + 1`，计算*mid时不需要加 1。*

C++ 代码模板：

```c++
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;
        else l = mid + 1;
    }
    return l;
}
```



版本2
当我们将区间`[l, r]`划分成`[l, mid - 1]`和`[mid, r]`时，其更新操作是`r = mid - 1`或者`l = mid`，此时为了防止死循环（**当 `r=l+1`且判定为 true 时，mid会一直等于`l`**），计算mid时需要加1。

C++ 代码模板：

```c++
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```



> **即 更新方式是`mid=l` 需要+1，否则`mid=r` 不需要+1**

#### AcWing 789. 数的范围

```c++
#include <iostream>

using namespace std;

const int N = 100010;

int n, m;
int q[N];

int main()
{
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i ++ ) scanf("%d", &q[i]);

    while (m -- )
    {
        int x;
        scanf("%d", &x);

        int l = 0, r = n - 1;
        while (l < r)
        {
            int mid = l + r >> 1;
            if (q[mid] >= x) r = mid;
            else l = mid + 1;
        }

        if (q[l] != x) cout << "-1 -1" << endl;
        else
        {
            cout << l << ' ';

            int l = 0, r = n - 1;
            while (l < r)
            {
                int mid = l + r + 1 >> 1;
                if (q[mid] <= x) l = mid;
                else r = mid - 1;
            }

            cout << l << endl;
        }
    }

    return 0;
}

```

### 浮点数二分

浮点数没有边界问题！

#### AcWing 790. 数的三次方根

```
#include <iostream>

using namespace std;

int main()
{
    double x;
    cin >> x;

    double l = -100, r = 100;
    while (r - l > 1e-8)
    {
        double mid = (l + r) / 2;
        if (mid * mid * mid >= x) r = mid;
        else l = mid;
    }

    printf("%.6lf\n", l);
  
```

## 高精度

### 高精度加法

#### AcWing 791. 高精度加法

```
#include <iostream>
#include <vector>

using namespace std;

vector<int> add(vector<int> &A, vector<int> &B)
{
    if (A.size() < B.size()) return add(B, A);

    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); i ++ )
    {
        t += A[i];
        if (i < B.size()) t += B[i];
        C.push_back(t % 10);
        t /= 10;
    }

    if (t) C.push_back(t);
    return C;
}

int main()
{
    string a, b;
    vector<int> A, B;
    cin >> a >> b;
    for (int i = a.size() - 1; i >= 0; i -- ) A.push_back(a[i] - '0');
    for (int i = b.size() - 1; i >= 0; i -- ) B.push_back(b[i] - '0');

    auto C = add(A, B);

    for (int i = C.size() - 1; i >= 0; i -- ) cout << C[i];
    cout << endl;

    return 0;
}
```

# 第二章 数据结构

## 单链表

**用数组模拟链表！使用`struct`容易超时！**

单链表：常用于邻接表，用来存储图和树。

双链表：优化某些问题。

![image-20230127170932265](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\算法\acwing 算法基础课.assets\image-20230127170932265.png)

e[N]：表示结点i的值

ne[N]：表示结点i的next指针的下标，空节点用-1表示

**模板**

```c++
int head;   // 头结点的下标
int idx;    // 当前使用到的结点下标(0,1,2,3)按顺序开始
int e[N],ne[N];

void init(){
    head=-1;
    idx=0;
}

// 将 x 插入到头结点 算法题中80%都是插入到头结点的位置
void add_to_head(int x){
    e[idx]=x;   //新结点的下标
    ne[idx]=head;   //将新结点的next设置为head
    head=idx++; //把当前的idx当做head，并且idx++

}

// 将x插入到下标是k的点后面
void add(int k,int x){
    e[idx]=x,ne[idx]=ne[k],ne[k]=idx++;
}

// 将下标是k的点后面的点删掉
void remove(int k){
    ne[k]=ne[ne[k]];
}
```

## 双链表

![image-20230128141702958](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\算法\acwing 算法基础课.assets\image-20230128141702958.png)

偷懒直接让下标为0的点作为head，1作为tail

```c++
// e[]表示节点的值，l[]表示节点的左指针，r[]表示节点的右指针，idx表示当前用到了哪个节点
int e[N], l[N], r[N], idx;

// 初始化
void init()
{
    //0是head，1是tail
    r[0] = 1, l[1] = 0;
    idx = 2;
}

// 在节点a的右边插入一个数x
void insert(int a, int x)
{
    e[idx] = x;
    l[idx] = a, r[idx] = r[a];
    l[r[a]] = idx, r[a] = idx ++ ;
}

// 删除节点a
void remove(int a)
{
    l[r[a]] = l[a];
    r[l[a]] = r[a];
}

```

## 栈和队列

**数组模拟栈和队列**！

##### 栈

```c++
// tt表示栈顶
int stk[N], tt = 0;

// 向栈顶插入一个数
stk[ ++ tt] = x;

// 从栈顶弹出一个数
tt -- ;

// 栈顶的值
stk[tt];

// 判断栈是否为空
if (tt > 0)
{

}
```

##### 单调栈

**常见模型：找出每个数左边离它最近的比它大/小的数** 	

```c++
//模板
int tt = 0;
for (int i = 1; i <= n; i ++ )
{
    while (tt && check(stk[tt], i)) tt -- ;
    stk[ ++ tt] = i;
}

```



```c++
//题目：acwing830
#include <iostream>
using namespace std;
const int N = 100010;
int stk[N], tt;

int main()
{
    int n;
    cin >> n;
    while (n -- )
    {
        int x;
        scanf("%d", &x);
        while (tt && stk[tt] >= x) tt -- ;//如果栈顶元素大于当前待入栈元素，则出栈
        if (!tt) printf("-1 ");//如果栈空，则没有比该元素小的值。
        else printf("%d ", stk[tt]);//栈顶元素就是左侧第一个比它小的元素。
        stk[ ++ tt] = x;
    }
    return 0;
}

```



##### 普通队列

```c++
// hh 表示队头，tt表示队尾
int q[N], hh = 0, tt = -1;

// 向队尾插入一个数
q[ ++ tt] = x;

// 从队头弹出一个数
hh ++ ;

// 队头的值
q[hh]; 

// 判断队列是否为空
if (hh <= tt)
{
	//不空
}
```

##### 循环队列

```c++
// hh 表示队头，tt表示队尾的后一个位置
int q[N], hh = 0, tt = 0;

// 向队尾插入一个数
q[tt ++ ] = x;
if (tt == N) tt = 0;

// 从队头弹出一个数
hh ++ ;
if (hh == N) hh = 0;

// 队头的值
q[hh];

// 判断队列是否为空
if (hh != tt)
{

}
```

##### 单调队列

常见模型：**找出滑动窗口中的最大值**/最小值

```c++
// 模板
int hh = 0, tt = -1;
for (int i = 0; i < n; i ++ )
{
    while (hh <= tt && check_out(q[hh])) hh ++ ;  // 判断队头是否滑出窗口
    while (hh <= tt && check(q[tt], i)) tt -- ;
    q[ ++ tt] = i;
}
```



```c++
// AcWing 154. 滑动窗口
#include <iostream>
using namespace std;
const int N = 1000010;
// q队列存储下标 a存储元素
int a[N], q[N], hh, tt = -1;

int main() {
  int n, k;
  cin >> n >> k;
  for (int i = 0; i < n; ++i) {
    scanf("%d", &a[i]);
    // 维持滑动窗口的大小
    // 当前滑动窗口的大小(i - q[hh] + 1)>我们设定的
    // 滑动窗口的大小(k),队列弹出队列头元素以维持滑动窗口的大小
    // 因为窗口只移动一位，所以用if不用while
    if (hh <= tt && k < i - q[hh] + 1) ++hh;  // 若队首出窗口，hh加1

    // 构造单调递增队列
    // 当队列不为空(hh <= tt) 且 当队列队尾元素>=当前元素(a[i])时,那么队尾元素
    // 就一定不是当前窗口最小值,删去队尾元素,加入当前元素(q[ ++ tt] = i)
    while (hh <= tt && a[i] <= a[q[tt]]) --tt;  // 若队尾不单调，tt减1
    q[++tt] = i;                                // 下标加到队尾
    // 题目是从第k个数开始输出，不足K个不输出
    if (i + 1 >= k) printf("%d ", a[q[hh]]);
  }
  cout << endl;
  hh = 0;
  tt = -1;  // 重置！
  for (int i = 0; i < n; ++i) {
    if (i - k + 1 > q[hh]) ++hh;
    while (hh <= tt && a[i] >= a[q[tt]]) --tt;
    q[++tt] = i;
    if (i + 1 >= k) printf("%d ", a[q[hh]]);
  }
  return 0;
}

```

> **思路：**
>
> 最小值和最大值分开来做，两个for循环完全类似，都做以下四步：
>
> 1. 解决队首已经出窗口的问题;
>
> 2. 解决队尾与当前元素a[i]不满足单调性的问题;
> 3. 将当前元素下标加入队尾;
> 4. 如果满足条件则输出结果;
>
> 解题思路（**以最大值为例**）：
>
> 由于我们需要求出的是滑动窗口的最大值。
>
> 如果当前的滑动窗口中有两个下标 i 和 j ，其中i在j的左侧（i<j），并且i对应的元素不大于j对应的元素（nums[i]≤nums[j]），则：
>
> 当滑动窗口向右移动时，只要 i 还在窗口中，那么 j 一定也还在窗口中。这是由于 i 在 j 的左侧所保证的。
>
> 因此，由于 nums[j] 的存在，nums[i] 一定不会是滑动窗口中的最大值了，我们可以将nums[i]永久地移除。
>
> 因此我们可以使用一个队列存储所有还没有被移除的下标。在队列中，这些下标按照从小到大的顺序被存储，并且它们在数组nums中对应的值是严格单调递减的。
>
> 当滑动窗口向右移动时，我们需要把一个新的元素放入队列中。
>
> 为了保持队列的性质，我们会不断地将新的元素与队尾的元素相比较，如果新元素大于等于队尾元素，那么队尾的元素就可以被永久地移除，我们将其弹出队列。我们需要不断地进行此项操作，直到队列为空或者新的元素小于队尾的元素。
>
> 由于队列中下标对应的元素是严格单调递减的，因此此时队首下标对应的元素就是滑动窗口中的最大值。
>
> 窗口向右移动的时候。因此我们还需要不断从队首弹出元素保证队列中的所有元素都是窗口中的，因此当队头元素在窗口的左边的时候，弹出队头。
>
> 
>
> **需要注意的细节：**
>
> 1. 上面四个步骤中一定要先3后4，因为有可能输出的正是新加入的那个元素;
> 2. 队列中存的是原数组的下标，取值时要再套一层，a[q[]];
> 3. 算最大值前注意将hh和tt重置;
> 4. 此题用cout会超时，只能用printf;
> 5. hh从0开始，数组下标也要从0开始。

## KMP

> **前缀：包含首字母，不包含最后一个字母的所有子串**
>
> **后缀：包含尾字母，不包含首字母的所有子串**
>
> **我们求的是最长相等的前缀和后缀！**
>
> **前缀和后缀都是从左往右的！**

比如模式串：aabaaf

| 前缀  | 后缀  |      |
| ----- | ----- | ---- |
| a     | f     |      |
| aa    | af    |      |
| aab   | aaf   |      |
| aaba  | baaf  |      |
| aabaa | abaaf |      |
|       |       |      |

所有子串的最大相等前后缀：

| 子串   | 相等的前后缀 |
| ------ | ------------ |
| a      | 0            |
| aa     | 1            |
| aab    | 0            |
| aaba   | 1            |
| aabaa  | 2            |
| aabaaf | 0            |

**前缀和后缀是否相等都是从左往右的！**aabaa的前缀有：`a,aa,aab,aaba` 后缀有`a,aa,baa,abaa`所以为2

> 思路：
>
> 1. 初始化
> 2. 前后缀相同的情况
> 3. 前后缀相同的情况
> 4. 求出next
>
> 注意：
>
> 用 `i` 指向后缀末尾，`j` 指向前缀末尾，初始化时，j=0，指向前缀末尾，i=1，指向后缀末尾
>
> 
>
> **next数组就是一个前缀表（prefix table）。**
>
> 前缀表有什么作用呢？
>
> **前缀表是用来回退的，它记录了模式串与主串(文本串)不匹配的时候，模式串应该从哪里开始重新匹配。**
>
> 首先要知道前缀表的任务是当前位置匹配失败，找到之前已经匹配上的位置，再重新匹配，此也意味着在某个字符失配时，前缀表会告诉你下一步匹配中，模式串应该跳到哪个位置。
>
> 那么什么是前缀表：**记录下标i之前（包括i）的字符串中，有多大长度的相同前缀后缀。**
>
> 求解next**数组前要懂的以下几个概念**：
>
> next数组定义：当主串与模式串的某一位字符不匹配时，模式串要回退的位置
>
> `next[j]`：其值=第 `j` 位字符前面 `j-1` 位字符组成的子串的前后缀重合字符数+1

前缀表（不减一）的C++代码实现：

```c++
class Solution {
public:
    void getNext (int* next, const string& s){
        next[0] = 0;
        int j = 0;
        for(int i = 1;i < s.size(); i++){
            while(j > 0 && s[i] != s[j]) {
                j = next[j - 1];
            }
            if(s[i] == s[j]) {
                j++;
            }
            next[i] = j;
        }
    }
    bool repeatedSubstringPattern (string s) {
        if (s.size() == 0) {
            return false;
        }
        int next[s.size()];
        getNext(next, s);
        int len = s.size();
        if (next[len - 1] != 0 && len % (len - (next[len - 1] )) == 0) {
            return true;
        }
        return false;
    }
};
```

前缀表统一减一的实现方式：

```C++
class Solution {
public:
    void getNext (int* next, const string& s){
        next[0] = -1;
        int j = -1;
        for(int i = 1;i < s.size(); i++){
            while(j >= 0 && s[i] != s[j + 1]) {
                j = next[j];
            }
            if(s[i] == s[j + 1]) {
                j++;
            }
            next[i] = j;
        }
    }
    bool repeatedSubstringPattern (string s) {
        if (s.size() == 0) {
            return false;
        }
        int next[s.size()];
        getNext(next, s);
        int len = s.size();
        if (next[len - 1] != -1 && len % (len - (next[len - 1] + 1)) == 0) {
            return true;
        }
        return false;
    }
};
```

## Trie树 

Trie树，即字典树，又称单词查找树或键树，是一种树形结构，是一种哈希树的变种。典型应用是用于统计和排序大量的字符串（但不仅限于字符串），所以经常被搜索引擎系统用于文本词频统计。它的优点是：利用字符串的公共前缀来减少查询时间，最大限度地减少无谓的字符串比较。

**前缀树的3个基本性质：**

1. 根节点不包含字符，除根节点外每一个节点都只包含一个字符。
2. 从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串。
3. 每个节点的所有子节点包含的字符都不相同。





```c++
int son[N][26], cnt[N], idx;
// 0号点既是根节点，又是空节点
// son[][]存储树中每个节点的子节点
// cnt[]存储以每个节点结尾的单词数量

// 插入一个字符串
void insert(string str) {	
    int p = 0;
    for (int i = 0; str[i]; ++i) {	//str[i]=='\0'说明遍历完成
        int u = str[i] - 'a';
        if (!son[p][u]) son[p][u] = ++idx;
        p = son[p][u];
    }
    cnt[p]++;
}

// 查询字符串出现的次数
int query(string str) {
    int p = 0;
    for (int i = 0; str[i]; ++i) {
        int u = str[i] - 'a';
        if (!son[p][u]) return 0;
        p = son[p][u];
    }
    return cnt[p];
}
```

