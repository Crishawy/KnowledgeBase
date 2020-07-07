

**Abstract：**

本文研究单图像超分辨（SISR）问题，现有的大多数模型忽略了深层CNN网络中间层的相互关系，本文提出了一种二阶注意力网络（SAN），使用二阶统计量协方差池化（covirance pooling）代替一阶统计量池化（global average pooling）并使用注意力机制（attention mechanism）进而获取通道间的相互关系特征（channel-wise correlation）。

 

**I. Introduction**

SISR是一种Image-to-Image的问题，分别提出SISR的三种方法，分别是内插法、基于模型的方法和基于学习的方法。

\1. 内插法是简单有效的，但应用受限。

\2. 基于模型的方法更为灵活，但往往会有高计算复杂的优化过程、以及无法处理异常的图片数据（泛化能力不强）。

\3. 基于学习的方法，如CNN近年来是非常有效的，但CNN的中间层表示能力不强，如何增加CNN的表征的能力，从而提取更为有效的特征是核心问题。

**Contribution**

本文提出了一种基于二阶注意力机制来学习更好的特征以及进行特征相关性学习，并提出了一种非局部增强的残差组（NLRG）结构来进一步获取长距离的空间上下文信息，最后通过堆叠局部残差注意网络来更好的完成SISR任务。

 

**II. Related Work**

​    SR是一个Image-to-image 回归问题，通过增加网络的深度，并使用残差学习方法，可以显著的改善SR的性能。同时，NLRN和RCAN等方法关注于图像在空间和通道上的特征相关性，获取了很好的效果。

 

**III. SAN**

\1. 网络架构

 

SAN分别由四个部分组成：浅层特征提取模块（shallow feature extraction）、非局部增强残差组模块（NLRG，提取深层特征）、提升模块（upscale module）和重构模块（reconstruction part），输入的LR图像首先通过一个单层的convolution layer提取浅层特征，然后通过一个非局部加强的残差组提取深层特征，接着通过upscale moduel 提取高层特征，最后引入一个单层的convolutional layer进行重构。

 

 

 

 

整个pipeline的公示如上图，公示（1）中将低分辨的图像  通过一层卷积层  提取浅层特征  ，随后将  输入到NLRG模块来提取深层特征  ，接着通过一个提升模块  得到提升后的特征  ，提升模块可以使用转置卷积[3]或ESPCN，最后通过一个单层的卷积层将提升特征  映射到SR图像。

 

\2. 损失函数

follow previous works，我们使用L1损失函数，具体而言：

 

可以使用随机梯度下降方法来训练整个网络。

 

\3. 非局部增强残差组（NLRG）

​    NLRG模块由共源残差组模块（SSRG）和区域级的非局部模块（RL-NL）组成（间上图）。SSRG由G个局部源残差注意力组（LSRAG）共源跳层连接（SSC），每一个LSRAG包含M个局部跳层连接的共源残差块和一个二阶通道注意力模块（SOCA）来提取特征的内部相关依赖性；RL-NL用于提取LR的structure cues和HR的self-similarities in HR nature scenes。

​    **LSRAG****：**使用SSC连接G个残差组的原因：不仅能加快深层网络的训练，也能绕过LR图像中的低频信息，第g个LSRAG信息流：

 

第g个LSRAG中的第m个残差块的信息流：

 

为了关注信息更丰富的features，LSRAG使用了局部源跳层连接产生输出：

 

​    **RL-NL****：**传统的非局部神经网络（non-local netural network，NL）用于整个图像长距离的特征，以用于高层任务（high-level，图像识别、检测等），然而却局限于计算负担（尤其是当feature size很大时）。经验表明，区域的非局部操作更适合用于低层任务（low-level，图像恢复，超分辨等），本文将feature map分割为k×k的区域进行RL-NL操作。

 

\4. **LSRAG****中的SOCA**

SOCA包含两个部分：covariance normalization 和 channel attention，其中CN是核心的步骤，文献19表明是提取discriminative representations的核心，本文也通过一种门控机制添加了channel attention从而更好的捕获特征的内在依赖性。

**covariance normalization:** 对于一个H×W×C的特征图谱 F，其中H和W分别为高度和宽度，C为特征图谱的个数。将F重构为s×C的特征图谱X，s = WH，因此，样本协方差矩阵可以计算为：

 

其中  ，I和1是s×s的单位矩阵和1矩阵。可以证明协方差矩阵  （C×C）是对称半正定的，因此，可以使用特征值分解（EIG）：

 

其中，U是正交矩阵，  ，特征值在对角线以非递增的顺序排列。最后协方差正则可以表示为以下式子：

 

其中α是正则化系数，根据研究表明，  可以work well。

**channel attention:** 正则化后的协方差矩阵包含了通道方向的特征相关性，我们使用全局协方差池化来获取这种通道特征。  ，则第c个通道特征可以计算为：

 

其中  为全局协方差池化函数。为了充分利用全局池化的特征内部依赖性，本文使用了门控机制，使用简单的sigmoid函数进行调控：

 

 

其中  和f为ReLu和sigmoid激活函数，  为第c个通道的调控系数（选择系数），  为第c个通道的特征图谱。此外，由于原始的特征值分解无法在GPU平台上并行运算，本文使用牛顿-舒尔茨迭代方法实现并行化，从而加速运算。

 

**IV.** **实验**

**1.** **实验设置**

​    Follow previous work，本文使用DIV2K数据集作为训练集，测试机我们选取5个标准的数据集：Set5、Set14、BSD100、Urban100、Manga109，本文采用两种退化模型BI和BD：

​    BI：最经典的bicubic的降采样。

​    BD: 先把图像模糊化，再将它降采样。

​    在训练中，我们通过旋转和翻折等操作增强训练，使用adam优化器训练网络。

​    **评价指标：**在YCbCr图片数据格式中Y的分量的PSNR和SSMI，YCbCr相对于RGB颜色系统使用Y：亮度，Cb：蓝色色度，Cr：红色色度表示图片数据，在计算机处理图片时应用更为广泛，由于图片的质量仅和Y分量有关，因此只需要关注Y分量即可。

​    **PSNR****：峰值信噪比**

 

​    PSNR计算的是单通道灰度图像，对于RGB图像，则有两种方式计算，1）计算每个通道的PSNR取平均，2）将RGB转为YcbCr格式，仅在Y通道计算PSNR

​    **SSIM****：结构相似性**

 

BI 

\2. Ablation Study

 

\3. BI Results

 

SAN恢复的细节优于其他模型，SAN与RCAN有着相似的表现，主要因为两者都使用了channel-wise的特征，而RCAN使用的是一阶统计量，SAN使用的是二阶统计量。此外，SAN在Urban100数据集中可能有比较差的表现，因为这些数据集的主要特征是一个边缘、棱角的一阶特征，而SAN提取的是二阶特征。

 

4. BD Results

 

 

V. 思考

​    本文研究的是单图片超分辨问题（SISR），是一个Image-to-Image Regression问题，核心问题在于如何从原始的LR图像中提取到核心特征，从而将LR尽量恢复成HR。本文提出了一种基于二阶统计量的注意力网络（SAN），使用协方差池化代替一阶统计量池化来提取LR中每个channel的内部依赖特征，使用非局部残差组 + 共源跳层连接 来堆叠网络，学习深层特征。

​    问题：

​    \1. 本文从原始图像LR中提取低频信息用于重建，是否可以结合高频信息，或者有无更好的方法可以从LR中提取核心特征？

​    \2. 本文是否能从可解释性方面解释二阶统计量优于一阶统计量的原因？或者结合两个统计量会更好？