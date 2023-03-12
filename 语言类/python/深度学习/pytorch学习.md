# 1. 深度学习的介绍



## 深度学习的概念

**深度学习**（英语：deep learning）是机器学习的分支，是一种以人工神经网络为架构，对数据进行特征学习的算法。

在生物神经网络中，每个神经元与其他神经元相连，当它"兴奋"时，就会向相连的神经元发送化学物质，从而改变这些神经元内的电位；**如果某神经元的电位超过了一个"阈值"，那么它就会被激活，即“兴奋”起来，向具他神经元发送化学物质。**

1943年，McCulloch和Pitts将上述情形抽象为上图所示的简单模型，这就是一直沿用至今的M-P神经元模型。把许多这样的神经元按一定的层次结构连接起来，就得到了神经网络。

## 机器学习和深度学习的区别

### 区别1 ：特征提取

![](D:/BaiduNetdiskWorkspace/学习资料/编程学习笔记/语言类/python/深度学习/images/1.1/区别.png)

从**特征提取**的角度出发：

1. 机器学习需要有人工的特征提取的过程

2. 深度学习没有复杂的人工特征提取的过程，特征提取的过程可以通过深度神经网络自动完成

   

### 区别2：数据量

![](D:/BaiduNetdiskWorkspace/学习资料/编程学习笔记/语言类/python/深度学习/images/1.1/数据量.png)

从**数据量**的角度出发：

1. 深度学习需要大量的训练数据集，会有更高的效果
2. 深度学习训练深度神经网络需要大量的算力，因为其中有更多的参数



## 深度学习的应用场景

1. 图像识别

   1. 物体识别
   2. 场景识别
   3. 人脸检测跟踪
   4. 人脸身份认证

2. 自然语言处理技术

   1. 机器翻译
   2. 文本识别
   3. 聊天对话

3. 语音技术

   1. 语音识别

   ## 常见的深度学习框架 

   目前企业中常见的深度学习框架有很多，TensorFlow, Caffe2, Keras, Theano, PyTorch, Chainer, DyNet, MXNet, and CNTK等等

   其中tensorflow和Kears是google出品的，使用者很多，但是其语法晦涩而且和python的语法不尽相同，对于入门玩家而言上手难度较高。

   所以在之后的课程中我们会使用facebook出的PyTorch，PyTorch的使用和python的语法相同，整个操作类似Numpy的操作，并且 PyTorch使用的是动态计算，会让代码的调试变的更加简单

   

# 神经网络的介绍

## 人工神经网络的概念

**人工神经网络**（英语：Artificial Neural Network，ANN），简称**神经网络**（Neural Network，NN）或**类神经网络**，是一种模仿生物神经网络（动物的中枢神经系统，特别是大脑）的结构和功能的数学模型，用于对函数进行估计或近似。

和其他机器学习方法一样，神经网络已经被用于解决各种各样的问题，例如机器视觉和语音识别。这些问题都是很难被传统基于规则的编程所解决的。



## 神经元的概念

在生物神经网络中，每个神经元与其他神经元相连，当它“兴奋”时，就会向相连的神经元发送化学物质，从而改变这些神经元内的电位；如果某神经元的电位超过了一个“阈值”，那么它就会被激活，即“兴奋”起来，向其他神经元发送化学物质。

1943 年，McCulloch 和 Pitts 将上述情形抽象为上图所示的简单模型，这就是一直沿用至今的 **M-P 神经元模型**。把许多这样的神经元按一定的层次结构连接起来，就得到了神经网络。

一个简单的神经元如下图所示，

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\神经元.png)

其中：

1. $ a_1,a_2\dots a_n $ 为各个输入的分量
2. $ w_1,w_2 \cdots w_n $ 为各个输入分量对应的权重参数
3. $b$ 为偏置
4. $f$ 为**激活函数**，常见的激活函数有tanh，sigmoid，relu
5. $t$ 为神经元的输出

使用数学公式表示就是：
$$
t = f(W^TA+b)
$$
可见，**一个神经元的功能是求得输入向量与权向量的内积后，经一个非线性传递函数得到一个标量结果**。



## 单层神经网络

是最基本的神经元网络形式，由有限个神经元构成，所有神经元的输入向量都是同一个向量。由于每一个神经元都会产生一个标量结果，所以单层神经元的输出是一个向量，向量的维数等于神经元的数目。

示意图如下：

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\单层神经网络.png)

##  感知机

感知机由**两层神经网**络组成，**输入层**接收外界输入信号后传递给**输出层（输出+1正例，-1反例）**，输出层是 M-P 神经元

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\感知机.png)

其中从$w_0,w_1\cdots w_n$都表示权重

**感知机的作用：**

把一个n维向量空间用一个超平面分割成两部分，给定一个输入向量，超平面可以判断出这个向量位于超平面的哪一边，得到输入时正类或者是反类，**对应到2维空间就是一条直线把一个平面分为两个部分**。

## 多层神经网络

多层神经网络就是由单层神经网络进行叠加之后得到的，所以就形成了**层**的概念，常见的多层神经网络有如下结构：

- 输入层（Input layer），众多神经元（Neuron）接受大量输入消息。输入的消息称为输入向量。
- 输出层（Output layer），消息在神经元链接中传输、分析、权衡，形成输出结果。输出的消息称为输出向量。
- 隐藏层（Hidden layer），简称“隐层”，是输入层和输出层之间众多神经元和链接组成的各个层面。隐层可以有一层或多层。隐层的节点（神经元）数目不定，但数目越多神经网络的非线性越显著，从而神经网络的强健性（robustness）更显著。

示意图如下：

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\多层神经网络.png)

**概念：全连接层**

全连接层：当前一层和前一层每个神经元相互链接，我们称当前这一层为全连接层。

思考：假设第N-1层有m个神经元，第N层有n个神经元，当第N层是全连接层的时候，则N-1和N层之间有1，这些参数可以如何表示？

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\全连接层.png)

从上图可以看出，所谓的全连接层就是在前一层的输出的基础上进行一次$Y=Wx+b$的变化(不考虑激活函数的情况下就是一次线性变化，所谓线性变化就是平移(+b)和缩放的组合(*w))

## 激活函数

在前面的神经元的介绍过程中我们提到了激活函数，那么他到底是干什么的呢？

假设我们有这样一组数据，三角形和四边形，需要把他们分为两类

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\激活函数1.png)

通过不带激活函数的感知机模型我们可以划出一条线, 把平面分割开

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\激活函数2.png)

假设我们确定了参数w和b之后，那么带入需要预测的数据，如果y>0,我们认为这个点在直线的右边，也就是正类（三角形），否则是在左边（四边形）

但是可以看出，三角形和四边形是没有办法通过直线分开的，那么这个时候该怎么办？

可以考虑使用多层神经网络来进行尝试，比如**在前面的感知机模型中再增加一层**

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\激活函数3.png)



对上图中的等式进行合并，我们可以得到：
$$
y = (w_{1-11}w_{2-1}+\cdots)x_1+(w_{1-21}w_{2-1}+\cdots)x_2 + (w_{2-1}+\cdots)b_{1-1}
$$
上式括号中的都为w参数，和公式$y = w_1x_1 + w_2x_2 +b$完全相同，依然只能够绘制出直线

所以可以发现，即使是多层神经网络，相比于前面的感知机，没有任何的改进。

但是如果此时，我们在前面感知机的基础上加上**非线性的激活函数**之后，输出的结果就不在是一条直线

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\激活函数4.png)

如上图，右边是sigmoid函数，对感知机的结果，通过sigmoid函数进行处理

如果给定合适的参数w和b，就可以得到合适的曲线，能够完成对最开始问题的非线性分割

所以激活函数很重要的一个**作用**就是**增加模型的非线性分割能力**

常见的激活函数有：

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\激活函数5.jpg)

看图可知：

- sigmoid 只会输出正数，以及靠近0的输出变化率最大
- tanh和sigmoid不同的是，tanh输出可以是负数
- Relu是输入只能大于0,如果你输入含有负数，Relu就不适合，如果你的输入是图片格式，Relu就挺常用的，因为图片的像素值作为输入时取值为[0,255]。

激活函数的作用除了前面说的**增加模型的非线性分割能力**外，还有

- **提高模型鲁棒性**
- **缓解梯度消失问题**
- **加速模型收敛等**

这些好处，大家后续会慢慢体会到，这里先知道就行



## 神经网络示例

一个男孩想要找一个女朋友，于是实现了一个**女友判定机**，随着年龄的增长，他的判定机也一直在变化

14岁的时候：

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\神经网络例子1.png)

无数次碰壁之后，男孩意识到追到女孩的可能性和颜值一样重要，于是修改了判定机：

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\神经网络例子2.png)

在15岁的时候终于找到呢女朋友，但是一顿时间后他发现有各种难以忍受的习惯，最终决定分手。一段空窗期中，他发现找女朋友很复杂，需要更多的条件才能够帮助他找到女朋友，于是在25岁的时候，他再次修改了判定机：

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\神经网络例子3.png)

在更新了女友判定机之后，问题又来了，很多指标不能够很好的量化，如何颜值，什么样的叫做颜值高，什么样的叫做性格好等等，为了解决这个问题，他又更新了判定机，最终得到**超级女友判定机**

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\神经网络例子4.png)



上述的超级女友判定机其实就是神经网络，它能够接受基础的输入，通过隐藏层的线性的和非线性的变化最终的到输出

通过上面例子，希望大家能够理解深度学习的**思想**：

**输出的最原始、最基本的数据，通过模型来进行特征工程，进行更加高级特征的学习，然后通过传入的数据来确定合适的参数，让模型去更好的拟合数据。**

**这个过程可以理解为盲人摸象，多个人一起摸，把摸到的结果乘上合适的权重，进行合适的变化，让他和目标值趋近一致。整个过程只需要输入基础的数据，程序自动寻找合适的参数。**

# Pytorch的入门使用

## 1. 张量Tensor

张量是一个统称，其中包含很多类型：

1. 0阶张量：标量、常数，0-D Tensor
2. 1阶张量：向量，1-D Tensor
3. 2阶张量：矩阵，2-D Tensor
4. 3阶张量
5. ...
6. N阶张量


## 2. Pytorch中创建张量

1. 使用python中的列表或者序列创建tensor

   ```python
   torch.tensor([[1., -1.], [1., -1.]])
   tensor([[ 1.0000, -1.0000],
           [ 1.0000, -1.0000]])
   ```

2. 使用numpy中的数组创建tensor

   ```python
   torch.tensor(np.array([[1, 2, 3], [4, 5, 6]]))
   tensor([[ 1,  2,  3],
           [ 4,  5,  6]])
   ```

3. 使用torch的api创建tensor

   1. `torch.empty(3,4)`创建3行4列的空的tensor，会用无用数据进行填充

   2. `torch.ones([3,4])` 创建3行4列的**全为1**的tensor，最好加上 `[]`

   3. `torch.zeros([3,4])`创建3行4列的**全为0**的tensor

   4. `torch.rand([3,4])` 创建3行4列的**随机值**的tensor，随机值的区间是`[0, 1)`

   5. `torch.arange(12)`：创建以0开始的前12个整数
   
      ```python
      torch.rand(2, 3)
      
      tensor([[ 0.8237,  0.5781,  0.6879],
      [ 0.3816,  0.7249,  0.0998]])
      ```
   
   6. `torch.randint(low=0,high=10,size=[3,4])` 创建3行4列的**随机整数**的tensor，随机值的区间是`[low, high)`
   
      ```python
      torch.randint(3, 10, (2, 2))
      
      tensor([[4, 5],
      	[6, 7]])
      ```
   
   7. `torch.randn([3,4])` 创建3行4列的**随机数**的tensor，随机值的分布式均值为0，方差为1

## 3. Pytorch中tensor的常用方法

1. 获取tensor中的数据(当tensor中只有一个元素可用)：`tensor.item()`

   ```python
   a=torch.tensor(np.arange(1))
   a
   
   tensor([0], dtype=torch.int32)
   
   a.item()
   0
   ```

2. 转化为numpy数组

   ```python
   z.numpy()
   
   array([[-2.5871205],
          [ 7.3690367],
          [-2.4918075]], dtype=float32)
   ```

3. 访问形状：通过张量的shape属性来访问张量（沿每个轴的⻓度）的形状。`x.shape`

4. 获取形状：`tensor.size()` 使用`size(dim=‘1’)` 或 `size(1)` 获取第1维的大小

   如果只想知道张量中元素的总数，即形状的所有元素乘积，可以检查它的大小（size）

   ```python
   x
   
   tensor([[    1,     2],
           [    3,     4],
           [    5,    10]], dtype=torch.int32)
   
   x.size()
   
   torch.Size([3, 2])
   ```

5. 形状改变：`tensor.view((3,4))`。类似numpy中的reshape，是一种浅拷贝，仅仅是形状发生改变

   > Returns a new tensor with the same data as the `self` tensor but of a different `shape`.

   ```python
   x.view(2,3)
   
   tensor([[    1,     2,     3],
           [    4,     5,    10]], dtype=torch.int32)
   ```

   如果某一维是-1，则会从另一维推断

   ```
   z = x.view(-1, 8)  # the size -1 is inferred from other dimensions
   >>> z.size()
   torch.Size([2, 8])
   ```

6. 要想改变⼀个张量的形状⽽不改变元素数量和元素值，可以调用reshape函数。

   > 我们不需要通过⼿动指定每个维度来改变形状。也就是说，如果我们的⽬标形状是（⾼度,宽度），那么在知道宽度后，⾼度会被⾃动计算得出，不必我们⾃⼰做除法。在上⾯的例⼦中，为了获得⼀个3⾏的矩阵，我们⼿动指定了它有3⾏和4列。幸运的是，我们可以**通过-1来调⽤此⾃动计算出维度的功能**。即我们可以⽤`x.reshape(-1,4)`或x.reshape(3,-1)来取代x.reshape(3,4)。

   ```
   X = x.reshape(3, 4)
   
   tensor([[ 0,  1,  2,  3],
           [ 4,  5,  6,  7],
           [ 8,  9, 10, 11]])
   ```

   

7. 获取阶数：`tensor.dim()`

   ```python
   x.dim()
   2
   ```

   

8. 获取最大值：`tensor.max()`

   ```python
   In [78]: x.max()
   Out[78]: tensor(10, dtype=torch.int32)
   ```

   

9. 转置：`tensor.t()` 二维用t 高维用transpose

   ```python
    x.t()
   
   tensor([[    1,     3,     5],
           [    2,     4, 	  10]], dtype=torch.int32)
   ```

10. `tensor[1,3]`  获取tensor中第一行第三列的值

11. `tensor[1,3]=100` 对tensor中第一行第三列的位置进行赋值100

12. transpose()

    `Tensor.transpose(dim0, dim1) → Tensor` See torch.transpose()

    `torch.transpose(input, dim0, dim1) → Tensor`
    Returns a tensor that is a transposed version of input. The given dimensions dim0 and dim1 are swapped.

    ```
    >>> x = torch.randn(2, 3)
    >>> x
    tensor([[ 1.0028, -0.9893,  0.5809],
            [-0.1669,  0.7299,  0.4942]])
    >>> torch.transpose(x, 0, 1)
    tensor([[ 1.0028, -0.1669],
            [-0.9893,  0.7299],
            [ 0.5809,  0.4942]])
    ```

13. permute()

    `torch.permute(input, dims) → Tensor`
    Returns a view of the original tensor input with its dimensions permuted.

    > Parameters:
    > input (Tensor) – the input tensor.
    >
    > dims (tuple of python:int) – The desired ordering of dimensions
    >

    Example

    ```
    >>> x = torch.randn(2, 3, 5)
    >>> x.size()
    torch.Size([2, 3, 5])
    >>> torch.permute(x, (2, 0, 1)).size()
    torch.Size([5, 2, 3])
    ```

    `Tensor.permute(*dims) → Tensor`
    See torch.permute()

14. tensor的切片

   ```python
In [101]: x
Out[101]:
tensor([[1.6437, 1.9439, 1.5393],
        [1.3491, 1.9575, 1.0552],
        [1.5106, 1.0123, 1.0961],
        [1.4382, 1.5939, 1.5012],
        [1.5267, 1.4858, 1.4007]])

In [102]: x[:,1]
Out[102]: tensor([1.9439, 1.9575, 1.0123, 1.5939, 1.4858])
   ```

11. torch与numpy转化

    ```
    a = torch.ones(5)
    b = a.numpy()
    b
    
    
    a = np.ones(5)
    b = torch.from_numpy(a)
    b
    >> tensor([1., 1., 1., 1., 1.], dtype=torch.float64)
    ```

## 4. tensor的数据类型

tensor中的数据类型非常多，常见类型如下：

![](D:\BaiduNetdiskWorkspace\学习资料\编程学习笔记\语言类\python\深度学习\pytorch学习.assets\tensor的数据类型.png)

上图中的Tensor types表示这种type的tensor是其实例



1. 获取tensor的数据类型:`tensor.dtype`

   ```python
   In [80]: x.dtype
   Out[80]: torch.int32
   ```

2. 创建数据的时候指定类型

   ```python
   In [88]: torch.ones([2,3],dtype=torch.float32)
   Out[88]:
   tensor([[9.1167e+18, 0.0000e+00, 7.8796e+15],
           [8.3097e-43, 0.0000e+00, -0.0000e+00]])
   ```

   

3. 类型的修改

   ```python
   In [17]: a
   Out[17]: tensor([1, 2], dtype=torch.int32)
   
   In [18]: a.type(torch.float)
   Out[18]: tensor([1., 2.])
   
   In [19]: a.double()
   Out[19]: tensor([1., 2.], dtype=torch.float64)
   ```

   



## 5. tensor的其他操作

1. tensor和tensor相加

   ```python
   In [94]: x = x.new_ones(5, 3, dtype=torch.float)
   
   In [95]: y = torch.rand(5, 3)
   
   In [96]: x+y
   Out[96]:
   tensor([[1.6437, 1.9439, 1.5393],
           [1.3491, 1.9575, 1.0552],
           [1.5106, 1.0123, 1.0961],
           [1.4382, 1.5939, 1.5012],
           [1.5267, 1.4858, 1.4007]])
   In [98]: torch.add(x,y)
   Out[98]:
   tensor([[1.6437, 1.9439, 1.5393],
           [1.3491, 1.9575, 1.0552],
           [1.5106, 1.0123, 1.0961],
           [1.4382, 1.5939, 1.5012],
           [1.5267, 1.4858, 1.4007]])
   In [99]: x.add(y)
   Out[99]:
   tensor([[1.6437, 1.9439, 1.5393],
           [1.3491, 1.9575, 1.0552],
           [1.5106, 1.0123, 1.0961],
           [1.4382, 1.5939, 1.5012],
           [1.5267, 1.4858, 1.4007]])
   In [100]: x.add_(y)  #带下划线的方法会对x进行就地修改
   Out[100]:
   tensor([[1.6437, 1.9439, 1.5393],
           [1.3491, 1.9575, 1.0552],
           [1.5106, 1.0123, 1.0961],
           [1.4382, 1.5939, 1.5012],
           [1.5267, 1.4858, 1.4007]])
   
   In [101]: x #x发生改变
   Out[101]:
   tensor([[1.6437, 1.9439, 1.5393],
           [1.3491, 1.9575, 1.0552],
           [1.5106, 1.0123, 1.0961],
           [1.4382, 1.5939, 1.5012],
           [1.5267, 1.4858, 1.4007]])
   ```

   注意：带下划线的方法（比如:`add_`)会对tensor进行就地修改

2. tensor和数字操作

   ```python
   In [97]: x +10
   Out[97]:
   tensor([[11., 11., 11.],
           [11., 11., 11.],
           [11., 11., 11.],
           [11., 11., 11.],
           [11., 11., 11.]])
   ```

3. CUDA中的tensor

   CUDA（Compute Unified Device Architecture），是NVIDIA推出的运算平台。 CUDA™是一种由NVIDIA推出的通用并行计算架构，该架构使GPU能够解决复杂的计算问题。

   `torch.cuda`这个模块增加了对CUDA tensor的支持，能够在cpu和gpu上使用相同的方法操作tensor

   通过`.to`方法能够把一个tensor转移到另外一个设备(比如从CPU转到GPU)

   ```python
   #device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
   if torch.cuda.is_available():
       device = torch.device("cuda")          # cuda device对象
       y = torch.ones_like(x, device=device)  # 创建一个在cuda上的tensor
       x = x.to(device)                       # 使用方法把x转为cuda 的tensor
       z = x + y
       print(z)
       print(z.to("cpu", torch.double))       # .to方法也能够同时设置类型
       
   >>tensor([1.9806], device='cuda:0')
   >>tensor([1.9806], dtype=torch.float64)
   ```

   

通过前面的学习，可以发现torch的各种操作几乎和numpy一样