# 1\. 定义

### 维基定义

#### 数组

**数组**（英语：Array），是由**相同类型**的元素（element）的集合所组成的资料结构，分配一块连续的内存来存储。利用元素的索引（index）可以计算出该元素对应的储存地址。

*   没有规定数组维度-->可以是任意的，eg：一维，二维，多维etc

#### 矩阵

数学上，一个*m*×*n*的**矩阵**是一个由*m*行*n*列元素排列成的矩形阵列。

*   规定了矩阵维度：m x n--->二维的

### numpy定义

#### numpy.ndarray

An array object represents a multidimensional, homogeneous array of fixed-size items. An associated data-type object describes the format of each element in the array .
一个表示多维，元素类型相同的确定维度的数组对象。有一个描述数组中元素类型的数据类型对象。

#### numpy.matrix

Returns a matrix from an array-like object, or from a string of data. A matrix is a specialized 2-D array that retains its 2-D nature through operations. It has certain special operators, such as * (matrix multiplication) and ** (matrix power).
矩阵是一个特定的2维的数组对象。有特定的操作，如：*乘法，**矩阵乘方

# 2\. 乘法运算类型及其定义

*   运算符号*
*   np.multiply（a,b）
*   np.dot(a, b)
*   np.matmul(a, b)

1.  运算符号*
    符号乘法*，运算符重载；
    (1) a,b 为1-D数组；**逐元素相乘element-wise**

```
a=np.arange(4)#a=[0,1,2,3]
b=np.arange(4)+1 #b=[1,2,3,4]
c=a*b # c= [0,2,6,12]

```

（2）a,b为2-D数组；element-wise运算

```
a=np.arange(6).reshape(2,3)#a=[[0,1,2],[3,4,5]]
b=np.arange(6).reshape(3,2)#b=[[0,1],[2,3],[4,5]]
c=a*b#wrong 运行出错；不能进行广播
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operands could not be broadcast together with shapes (2,3) (3,2)

```

（3）a,b 为2-D矩阵；矩阵运算

```
a=np.mat(np.arange(6).reshape(2,3))#a=[[0,1,2],[3,4,5]]
b=np.mat((np.arage(6)+1).reshape(3,2))#b=[[0,1],[2,3],[4,5]]
c=a*b #c=[[13, 16],[40, 52]]

```

运算*：
如果输入为ndarray类型，进行逐元素乘法运算；维度不等时，进行[广播](https://link.jianshu.com?t=http%3A%2F%2Fscipy.github.io%2Fold-wiki%2Fpages%2FEricsBroadcastingDoc)；
如果输入为matrix类型，进行矩阵乘法运算。

2.  np.multiply(a, b)
    Multiply arguments element-wise.逐元素相乘
    参数：a,b: 数组类型
    输出：ndarray 数组；a,b 逐元素相乘的结果；如果a和b都是标量，返回一个标量。

（1）标量：一个数，eg:6; 6.0; 666

```
a=1; b=2
c = np.multiply(a,b)
#c = 2 输出结果c为2

```

（2）a,b 1-D数组:

```
a=np.arange(4)#a=[0,1,2,3]
b=np.arange(4)+1 #b=[1,2,3,4]
c=np.multiply(a,b)#c=[0,2,6,12]

```

（3）a,b 2-D 数组：能广播，逐元素相乘；不能广播，报错。

```
a=np.arange(6).reshape(2,3)#a=[[0,1,2],[3,4,5]]
b=(np.arage(6)+1).reshape(3,2)#b=[[0,1],[2,3],[4,5]]
c=np.multiply(a,b)#wrong
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operands could not be broadcast together with shapes (2,3) (3,2)

```

（4）a，b 矩阵：报错；本质上，进行逐元素element-wise运算

```
a=np.mat(np.arange(6).reshape(2,3))#a=[[0,1,2],[3,4,5]]
b=np.mat((np.arage(6)+1).reshape(3,2))#b=[[0,1],[2,3],[4,5]]
c=np.multiply(a,b)#wrong
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operands could not be broadcast together with shapes (2,3) (3,2)

```

可以进行广播运算，正常运行（element-wise）

```
a=np.mat(np.arange(6).reshape(2,3))#a=[[0,1,2],[3,4,5]]
b=np.mat((np.arage(3)+1))#b=[[1,2,3]]
c=np.multiply(a,b)#c=matrix([[0,2,6],[3,8,15]])

```

3.  np.dot(a,b)
    Dot product of two arrays.数组a和数组b的点积。
    **点积**是两个向量上的函数并返回一个标量的二元运算；是内积的一种特殊形式。
    根据数组a和数组b的维度不同，运算过程也不相同：

（1）a, b为一维数组：返回a和b的点积（就是一个数）：

```
a=np.arange(2) #a=[0,1]
b=np.arange(2)+2 #b=[2,3]
c=np.dot(a,b) #c=3 ；just a number

```

（2）a, b为二维数组：a和b做矩阵乘法运算：

```
a=np.arrange(6).reshape(2,3) #a=[[0,1,2],[3,4,5]]
b=np.arange(6).reshape(3,2) #b=[[0,1],[2,3],[4,5]]
c=np.dot(a,b) # c= [[10, 13], [28,40]]

```

仍然可以看做是点积运算，矩阵a中的行向量和矩阵b中列向量乘积，得到一个数，记录下来（这个过程是点积运算，10=0*0+1*2+2*4）。
（3）a,b 为矩阵形式:和二维数组相同，进行矩阵运算。

```
a=np.mat(np.arrange(6).reshape(2,3)) #a=[[0,1,2],[3,4,5]]
b=np.mat(np.arange(6).reshape(3,2) )#b=[[0,1],[2,3],[4,5]]
c=np.dot(a,b) # c= [[10, 13], [28,40]]

```

4.  np.matmul(a, b)
    两个数组的矩阵积。
    运算根据参数的维度大小，有不同的计算方法：

*   参数a，b都为二维矩阵2-D，进行矩阵运算；
*   第一个参数为1维1-D，用1填充参数a，然后进行矩阵运算，之后，删除添加的1；
*   第二个参数为1维1-D，用1填充参数b，进行矩阵运算，之后，删除添加的1；
*   如果参数a，b的维度大于2，将其视为存在于最后两个索引的矩阵的栈，并进行相应广播；

（1）a,b为2-D数组：

```
a=np.arrange(6).reshape(2,3) #a=[[0,1,2],[3,4,5]]
b=np.arange(6).reshape(3,2) #b=[[0,1],[2,3],[4,5]]
c=np.matmul(a,b) # c= [[10,13],[28,40]]

```

（2）a,b为matrix类型;和2-D数组相同

```
a=np.mat(np.arrange(6).reshape(2,3)) #a=[[0,1,2],[3,4,5]]
b=np.mat(np.arange(6).reshape(3,2))#b=[[0,1],[2,3],[4,5]]
c=np.matmul(a,b) # c= [[10,13],[28,40]]
```

转自作者：七八音
链接：https://www.jianshu.com/p/428c3f0a6f35