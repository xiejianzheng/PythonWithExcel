# 第四章：NumPy基础

可能你还记得，在第一章，我们说过NumPy是Python科学计算中的核心包，它提供了基于数组的计算
和线性代数的支持。
由于NumPy是pandas的支柱，我将会介绍它的基础知识在这一章：在解释了什么是NumPy数组后，
我们将研究什么是矢量化和广播，这两个重要的概念允许你编写简洁的数学代码，你将在pandas
再次找到这两个概念。在这之后，我们将通过学习如何获取和设置数值的值以及解释视图和NumPy
数组副本之间的差异，来了解为什么NumPy为什么会提供称为通用函数的特殊函数，在本章结束
之前。即使我们在本书几乎很少直接用NumPy，了解它的基本也会使得我们在下一章更容易学习
pandas。

## NumPy入门

在本节中，我们将会学习一维和二维NumPy阵列，以及矢量化、广播和普遍函数等技术术语背后
的含义。

### NumPy阵列

要使用嵌套列表进行阵列运算，正如我们在上一章遇到的那样，你必须写某种循环。例如，添加
一个数到一个嵌套的列表中，你可以使用以下嵌套列表推导式（list comprehension）：

In [1]: matrix = [[1,2,3],
		  [4,5,6],
		  [7,8,9]]

In [2]: [[i+i for i in row] for row in matrix]

Out[2]: [[2,3,4], [5,6,7], [8,9,10]]

这不是非常的可读，更重要地是，如果你在一个大阵列执行这个操作，遍历每个元素会变得很慢。
根据你的用例和阵列的大小，使用NumPy而不是Python列表可以使你的计算速度提高几倍到上百倍。
NumPy是通过使用C或Fortran写的代码来实现这种性能的————这些是比Python快多的编译语言。
NumPy阵列是同质数据的N维阵列。同质意着，阵列中的所有元素需要是同一个数据类型。最常见的，
你需要处理的一维、二维浮点数阵列，如图所视：

图 4-1： 一个一维和一个二维NumPy数组。

让我们创建本章中需要使用的一个一维和一个二维阵列。

In[3]: # 第一步，让我们引入NumPy
       import numpy as np

In[4]: # 使用简单列表，构造一维阵列
       array1=np.array([10, 100, 1000.])

In[5]: # 使用嵌套列表，构造二维阵列
       array2=np.array([[1., 2., 3.],
                        [4., 5., 6.]])

**阵列维度**
注意一维、二维阵列的区别是很重要的：
一个一维阵列只有一个轴，因此没有明确的列或行方向。虽然这种行为与VBA中的数组很像，
但如果你来自MATLAB这样的，一维阵列始终具有列或行方向的语言，你可能要习惯这种行为。

即使一个阵列中除了最后一个元素（浮点型）都是整型，NumPy阵列的同质性迫使阵列的数据
类型为float64，这类型可以容纳所有元素。为了了解阵列的数据类型，需要访问dtype属性：

In[6]:  array1.dtype
Out[6]: dtype('float64')

由于dtype返回的是float64，而不是我们在上一章遇到的float，你可能已经猜到，NumPy使用了它
自己的数据类型，这比Python的数据类型更加细粒度。但这通常不是一个问题，在大多数时间，
Python和NumPy中不同数据类型之间的转换是自动进行的。

如果你需要显式地转换一个NumPy到Python的基础数据类型，只需使用相应的构造函数（稍后，
我将详细介绍如何访问阵列中的元素）:

#### 矢量计算及广播

如果你构造一个标量和一个NumPy阵列的和，NumPy将执行元素感应操作，这意味着你不必自己
循环遍历元素。NumPy社区将此，成为矢量化。它允许你编写简洁的代码，真实地展示数学符号。

如果你尝试对一个标量和一个NumPy阵列进行求和操作，NumPy会执行一个元素级的操作，这意味
着你自己不需要去遍历这些元素。NumPy社区称这为矢量化。这让你可以写出简洁的代码，几乎
和数学符号一样。
                                                                   
In[8]:  array2 + 1
Out[8]: array([[2.,3.,4.],
              [5.,6.,7.]])

**标量**
标量指基本的Python类型，像浮点、或字符串。这是为了将它们与具有多个元素（如列表和字典或
一维和二维NumPy阵列）数据结构区分开来。

当你使用两个阵列的时候，同样的原则同样适用。NumPy会进行元素级的操作。

In [9]: array2 * array2
Out[9]: array([[1., 4., 9.],
               [16., 25., 36.]])


如果你使用两个不同形状的阵列进行算术运算，如果可能，NumPy会自动将较小的阵列
穿越大的阵列，以便他们的形状兼容。这就是被称为广播：

在NumPy中当你在两个形状不相同的阵列上执行算术运算时，
小的阵列会自动穿越大的阵列，因此它们的形状会变得兼容。
但是这个是有前提的，就是两个形状要兼容。
我目前知道的是：
1乘1的单位阵列是可以执行广播操作的。
列数相同的单行阵列也是可以执行广播操作的，
行数相同的单列阵列也是可以执行广播操作的，

In [10]: array2 * array1

Out[10]: array([[10.,200.,3000.],
                [40.,500.,6000.]])

要执行矩阵乘法或点积，请是@运算符。
In [11]: array2 @ array2.T # array2.T 是array2.transpose() 
Out[11]: array([[14., 32.],
                [32., 77.]])

不要被我在本节中介绍的术语给吓倒了，如标量、矢量化、广播。
你现在知道阵列会在元素级执行算术操作，但你如何才能对阵列中
的每个元素应用函数呢？这就是泛函数的用处。

### 泛函数（ufunc）

泛函数会作用在NumPy阵列中的每一个元素上。例如，如果你在NumPy阵列上使用math模块上的
 Python的标准平方根函数，你将得到一个错误：
In [12]: import math
In [13]: math.sqrt(array2) # 报错

---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-13-5c37e8f41094> in <module>
----> 1 math.sqrt(array2)  # This will raise en Error
TypeError: only size-1 arrays can be converted to Python scalars

当然，你能使用一个嵌套的循环来获取每一个元素，然后根据结果再次构建NumPy阵列。

In [14]: np.array([[math.sqrt(i) for i in row] for row in array2])

这在NumPy没有提供ufunc泛函数和阵列足够小的情况下，是起作用的。然而，如果NumPy
有一个泛函数，使用它，因为在操作大阵列时会更快，除了更容易键入和键入之外：

In [15]: np.sqrt(array2)

一些NumPy的泛函数，像sum, 还可以作为阵列的方法使用：
  如果你需要统计每列的和，可以执行下列操作：

  In [16]: array2.sum(axis=0)
  Out[16]: array([5., 7., 9.])

参数轴（axis）= 0，代表沿着行的轴；axis=1，代表沿着列的轴。
如图4-1所示。不使用axis参数可以对整个阵列求和。

  In [17]: array2.sum()
  Out[17]: 21.0

在本书中，你会遇到更多的NumPy泛函数，因为他们可以和pandas的数据帧（DataFrame）联合使用。
到目前为止，我们一直在使用整个阵列，下一节，向你展示如何操控阵列的各个部分。并且介绍一些
有用的阵列构造函数。

## 创建和操作阵列
我会开始这节，通过获取或设置一个阵列的特定元素，在介绍一些有用的阵列构造函数之前，
包括如何构造假随机数，你可以用作蒙特卡罗模拟。我将结束本节，通过解释阵列的视图和副本之间
的区别。

### 获取和设置阵列元素
在上一章中，我向你展示了如何对列表进行索引和切片访问特定元素。
当你使用嵌套列表（例如第一章的矩阵）时，你可以使用链式索引：矩阵[0][0] 会获取到第一行的
第一个元素。但是对于NumPy阵列，你在一对方括号中提供索引和切片参数。
格式如下：

   numpy_阵列[行选择区，列选择区]

对于一维阵列，这简化为numpy_阵列[选择区]。当你选择单个元素，你会得到一个标量。否则，你会
得到一个一维或二维阵列。记住，切片表示法由一个开始索引（包含）、一个结束索引（排除）和两者之间的
一个冒号组成。如果不填起始索引和结束索引，就会剩下一个冒号，代表二维阵列中的所有行或列。

array1[2]: 返回一个一维阵列中第2列的标量值。

array2[0,0]: 返回二维阵列中第0行，第0列的标量值。

array2[:, 1:]: 返回一个3行2列的子阵列。3行是源阵列的所有行，2列，是原来从索引1开始的所有列。

array2[:, 1]: 返回一个3行1列的子阵列2。1列是原来矩阵的索引为1的列。

array2[1, :2]: 返回一个1行2列的子阵列。

### 有用的阵列构造函数

NumPy提供了几种阵列的构造方法，这些方法也有助于构造pandas数据帧（DataFrame），就像我们将会在第五章看到的那样。
一个最简单创建阵列的方法是使用arange函数。这代表阵列范围（array range）和前一章遇到区域函数range很像。不同在与
arange函数返回一个NumPy阵列，通过将其与reshape重塑函数结合允许我们快速生成所需维度的阵列。

In [23]: np.arange(2 * 5).reshape(2, 5) # 2 行，5列
Out[23]: array([[0,1,2,3,4], [5,6,7,8,9]])

另一个常见的需求是，例如Monte Carlo蒙特卡洛模拟，是生成正态分布伪随机数。NumPy使这个变得容易：

In [24]: np.random.randn(2, 3) # rows, 3 columns

numpy.random.randn函数是用于生成n维随机阵列的函数。
其他值得探讨的有用的构造函数是np.ones和np.zero。它们
分别用于创建值为1，值为0的相同的矩阵，以及np.eye用于
创建一个身份矩阵。在下一章，我们会再次遇到这些构造函数，
但现在，让我们先了解一下视图和NumPy阵列副本的区别。

### 视图和副本

当你在NumPy阵列上执行切片操作时，NumPy阵列返回的是一个视图。这意味着你是在一个原始阵列的一个子集上操作，
而没有复制数据。在一个视图上设置一个值，也会改变原数组。

In [25]: array2
Out[25]: array2([1., 2., 3.],
		[4., 5., 6.]])
In [26]: subset=array2[:, :2]
         subset

Out[26]: array([1., 2.],
	       [4., 5.]])

In [27]: subset[0,0] = 1000
	       
In [29]: array2
Out[29]: array([1000., 2., 3.],
	       [4., 5., 6.]])

如果那不是你想要的，你需要把In[26]改为如下：

subset=array2[:,:2].copy()

在一个副本上工作，会让原始的阵列保持不变。

## 结论
在这一章，我展示了你，如何使用NumPy阵列和矢量化、广播等表达式背后的逻辑。
撇开这些技术术语，使用阵列应该会感到非常直觉化，因为它们非常遵循数学符号。
虽然NumPy是一个功能强大得难以置信的库，但在使用它进行数据分析时，有两个主要问题：

* 整个NumPy阵列需要是同一个类型。这意味着，如果阵列中混合了文本和数字，就不能
执行本章中的算术运算。因为一旦包含文本，阵列的数据类型将为对象，这将不允许
数学操作。

* 使用NumPy阵列进行数据分析时，会使你很难知道每一行，或每一列所指的内容。
因为你通常使用位置来选择列。如：array2[:, 1]

pandas通过在NumPy阵列上提供更智能的数据结构解决了这些问题。他们是什么和
他们是如何工作的是下一章的主题。


