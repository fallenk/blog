---
title: CNN学习
date: 2018-05-09 21:58:41
tags: [AI, 深度学习]
category: 人工智能
---
# CNN学习快速学习
## 背景知识 
在前面的文章中，我们介绍了[全连接神经网络](https://www.zybuluo.com/hanbingtao/note/485480)，以及它的训练和使用。我们用它来识别了手写数字，然而，这种结构的网络对于图像识别任务来说并不是很合适。本文将要介绍一种更适合图像、语音识别任务的神经网络结构——卷积神经网络`(Convolutional Neural Network, CNN)`。说卷积神经网络是最重要的一种神经网络也不为过，它在最近几年大放异彩，几乎所有图像、语音识别领域的重要突破都是卷积神经网络取得的，比如谷歌的GoogleNet、微软的ResNet等，打败李世石的AlphaGo也用到了这种网络。本文将详细介绍卷积神经网络以及它的训练算法，以及动手实现一个简单的卷积神经网络。
<!--more-->
## 一个新的激活函数--ReLU
最近几年卷积神经网络中，激活函数往往不选择sigmoid或tanh函数，而是选择relu函数。Relu函数的定义是：
![](https://img-blog.csdn.net/20180113174854023)
Relu函数图像如下图所示:
![](http://upload-images.jianshu.io/upload_images/2256672-0ac9923bebd3c9dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

Relu函数作为激活函数，有下面几大优势：

- **速度快** 和`sigmoid函数`需要计算指数和倒数相比，`relu函数`其实就是一个`max(0,x)`，计算代价小很多。
- **减轻梯度消失问题** 回忆一下计算梯度的公式![](https://img-blog.csdn.net/20180113174953099)其中，![](https://img-blog.csdn.net/20180115081038308)是sigmoid函数的导数。在使用反向传播算法进行梯度计算时，每经过一层sigmoid神经元，梯度就要乘上一个![](https://img-blog.csdn.net/20180115081038308)。从下图可以看出，![](https://img-blog.csdn.net/20180115081038308)函数最大值是1/4。因此，乘一个![](https://img-blog.csdn.net/20180115081038308)会导致梯度越来越小，这对于深层网络的训练是个很大的问题。而relu函数的导数是1，不会导致梯度变小。当然，激活函数仅仅是导致梯度减小的一个因素，但无论如何在这方面relu的表现强于sigmoid。使用relu激活函数可以让你训练更深的网络。![](http://upload-images.jianshu.io/upload_images/2256672-ad98d6b22f1a66ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/360)
- **稀疏性** 通过对大脑的研究发现，大脑在工作的时候只有大约5%的神经元是激活的，而采用sigmoid激活函数的人工神经网络，其激活率大约是50%。有论文声称人工神经网络在15%-30%的激活率时是比较理想的。因为relu函数在输入小于0时是完全不激活的，因此可以获得一个更低的激活率。

## 全连接网络 VS 卷积网络
全连接神经网络之所以不太适合图像识别任务，主要有以下几个方面的问题：

- **参数数量太多** 考虑一个输入1000*1000像素的图片(一百万像素，现在已经不能算大图了)，输入层有1000*1000=100万节点。假设第一个隐藏层有100个节点(这个数量并不多)，那么仅这一层就有(1000*1000+1)*100=1亿参数，这实在是太多了！我们看到图像只扩大一点，参数数量就会多很多，因此它的扩展性很差。
- **没有利用像素之间的位置信息** 对于图像识别任务来说，每个像素和其周围像素的联系是比较紧密的，和离得很远的像素的联系可能就很小了。如果一个神经元和上一层所有神经元相连，那么就相当于对于一个像素来说，把图像的所有像素都等同看待，这不符合前面的假设。当我们完成每个连接权重的学习之后，最终可能会发现，有大量的权重，它们的值都是很小的(也就是这些连接其实无关紧要)。努力学习大量并不重要的权重，这样的学习必将是非常低效的。
- **网络层数限制** 我们知道网络层数越多其表达能力越强，但是通过梯度下降方法训练深度全连接神经网络很困难，因为全连接神经网络的梯度很难传递超过3层。因此，我们不可能得到一个很深的全连接神经网络，也就限制了它的能力。

那么，卷积神经网络又是怎样解决这个问题的呢？主要有三个思路：

- **局部连接** 这个是最容易想到的，每个神经元不再和上一层的所有神经元相连，而只和一小部分神经元相连。这样就减少了很多参数。
- **权值共享** 一组连接可以共享同一个权重，而不是每个连接有一个不同的权重，这样又减少了很多参数。
- **下采样** 可以使用Pooling来减少每层的样本数，进一步减少参数数量，同时还可以提升模型的鲁棒性。

对于图像识别任务来说，卷积神经网络通过尽可能保留重要的参数，去掉大量不重要的参数，来达到更好的学习效果。

接下来，我们将详述卷积神经网络到底是何方神圣。

## 卷积神经网络是啥
首先，我们先获取一个感性认识，下图是一个卷积神经网络的示意图：![](http://upload-images.jianshu.io/upload_images/2256672-a36210f89c7164a7.png)

### 网络架构
如上图所示，一个卷积神经网络由**若干卷积层**、**Pooling层**、**全连接层**组成。你可以构建各种不同的卷积神经网络，它的常用架构模式为：
`INPUT -> [[CONV]*N -> POOL?]*M -> [FC]*K`

也就是N个卷积层叠加，然后(可选)叠加一个Pooling层，重复这个结构M次，最后叠加K个全连接层。

对于上图展示的卷积神经网络：
`INPUT -> CONV -> POOL -> CONV -> POOL -> FC -> FC`
按照上面描述：
`INPUT -> [[CONV]*1 -> POOL]*2 -> [FC]*2` 即`N=1, M=2, K=2`

### 三维的层结构
从图1我们可以发现卷积神经网络的层结构和全连接神经网络的层结构有很大不同。全连接神经网络每层的神经元是按照一维排列的，也就是排成一条线的样子；而卷积神经网络每层的神经元是按照三维排列的，也就是排成一个长方体的样子，有宽度、高度和深度。

对于图1展示的神经网络，我们看到**输入层的宽度和高度**对应于输入图像的宽度和高度，而它的深度为1。接着，第一个卷积层对这幅图像**进行了卷积操作**(后面我们会讲如何计算卷积)，**得到了三个Feature Map**。这里的"3"可能是让很多初学者迷惑的地方，实际上，就是这个卷积层包含三个Filter，也就是三套参数，每个Filter都可以把原始输入图像卷积得到一个Feature Map，三个Filter就可以得到三个Feature Map。至于一个卷积层可以有多少个Filter，那是可以自由设定的。也就是说，卷积层的Filter个数也是一个超参数。我们可以**把Feature Map可以看做是通过卷积变换提取到的图像特征**，三个Filter就对原始图像提取出三组不同的特征，也就是得到了三个Feature Map，也称做三个`通道(channel)`。

继续观察图1，在第一个卷积层之后，Pooling层对三个Feature Map做了下采样(后面我们会讲如何计算下采样)，得到了三个更小的Feature Map。接着，是第二个**卷积层**，它有5个Filter。每个Fitler都把前面下采样之后的3个**Feature Map卷积在一起，得到一个新的Feature Map。这样，5个Filter就得到了5个Feature Map。接着，是第二个Pooling，继续对5个Feature Map进行下采样**，得到了5个更小的Feature Map。

图1所示网络的最后两层是全连接层。第一个全连接层的每个神经元，和上一层5个Feature Map中的每个神经元相连，第二个全连接层(也就是输出层)的每个神经元，则和第一个全连接层的每个神经元相连，这样得到了整个网络的输出。

至此，我们对卷积神经网络有了最基本的感性认识。接下来，我们将介绍卷积神经网络中各种层的计算和训练。

## 卷积神经网络输出值的计算
### 卷积层输出值的计算
我们用一个简单的例子来讲述如何计算卷积，然后，我们抽象出卷积层的一些重要概念和计算方法。

假设有一个`5*5`的图像，使用一个`3*3`的filter进行卷积，想得到一个`3*3`的F`eature Map`，如下所示：
![](http://upload-images.jianshu.io/upload_images/2256672-548b82ccd7977294.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

为了清楚的描述卷积计算过程，我们首先对图像的每个像素进行编号，![](https://img-blog.csdn.net/20180115081320003)用表示图像的第i行第i列元素；对filter的每个权重进行编号，![](https://img-blog.csdn.net/20180115081402212)用表示第m行第n列权重，![](https://img-blog.csdn.net/20180115081425922)用表示filter的偏置项；![](https://img-blog.csdn.net/20180115081453443)对Feature Map的每个元素进行编号，用表示Feature Map的第i行第j列元素；用f表示激活函数(这个例子选择relu函数作为激活函数)。然后，使用下列公式计算卷积：
![](https://img-blog.csdn.net/20180115081551153)
例如，对于Feature Map左上角元素![](https://img-blog.csdn.net/20180115081629010)来说，其卷积计算方法为：
![](https://img-blog.csdn.net/20180115081659514)
计算结果如下图所示:
![](http://upload-images.jianshu.io/upload_images/2256672-318017ad134effc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
接下来，Feature Map的元素![](https://img-blog.csdn.net/20180115081754383)的卷积计算方法为：![](https://img-blog.csdn.net/20180115081826274)
计算结果如下图所示：
![](http://upload-images.jianshu.io/upload_images/2256672-b05427072f4c548d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
可以依次计算出Feature Map中所有元素的值。下面的动画显示了整个Feature Map的计算过程：![](http://upload-images.jianshu.io/upload_images/2256672-19110dee0c54c0b2.gif)
上面的计算过程中，步幅(stride)为1。步幅可以设为大于1的数。例如，当步幅为2时，Feature Map计算如下：
![](http://upload-images.jianshu.io/upload_images/2256672-7f362ea9350761d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
![](http://upload-images.jianshu.io/upload_images/2256672-7f362ea9350761d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
![](http://upload-images.jianshu.io/upload_images/2256672-f5fa1e904cb0287e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
![](http://upload-images.jianshu.io/upload_images/2256672-7919cabd375b4cfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
我们注意到，当步幅设置为2的时候，Feature Map就变成2*2了。这说明图像大小、步幅和卷积后的Feature Map大小是有关系的。事实上，它们满足下面的关系：
![](https://img-blog.csdn.net/20180115081908059)
在上面两个公式中，![](https://img-blog.csdn.net/20180115081942388)是卷积后Feature Map的宽度；![](https://img-blog.csdn.net/20180115082001142)是卷积前图像的宽度；F是filter的宽度；P是Zero Padding数量，Zero Padding是指在原始图像周围补几圈0，如果P的值是1，那么就补1圈0；S是步幅；![](https://img-blog.csdn.net/20180115082049200)是卷积后Feature Map的高度；![](https://img-blog.csdn.net/20180115082111660)是卷积前图像的宽度。式2和式3本质上是一样的。
以前面的例子来说，图像宽度![](https://img-blog.csdn.net/20180115082001142)=5，filter宽度F=3，Zero Padding的值P=0，步幅S=2，则
![](https://img-blog.csdn.net/20180115082227146)
说明Feature Map宽度是2。同样，我们也可以计算出Feature Map高度也是2。
前面我们已经讲了深度为1的卷积层的计算方法，如果深度大于1怎么计算呢？其实也是类似的。如果卷积前的图像深度为D，那么相应的filter的深度也必须为D。我们扩展一下式1，得到了深度大于1的卷积计算公式：
![](https://img-blog.csdn.net/20180115082300868)
在式4中，D是深度；F是filter的大小(宽度或高度，两者相同)；![](https://img-blog.csdn.net/20180115082341336)表示filter的第d层第m行第n列权重；![](https://img-blog.csdn.net/20180115082420974)表示图像的第d层第i行第j列像素；其它的符号含义和式1是相同的，不再赘述。
我们前面还曾提到，每个卷积层可以有多个filter。每个filter和原始图像进行卷积后，都可以得到一个Feature Map。因此，卷积后Feature Map的深度(个数)和卷积层的filter个数是相同的。

下面的动画显示了包含两个filter的卷积层的计算。我们可以看到`7*7*3`输入，经过两个`3*3*3filter`的卷积(步幅为2)，得到了`3*3*2`的输出。另外我们也会看到下图的`Zero padding`是1，也就是在输入元素的周围补了一圈0。`Zero padding`对于图像边缘部分的特征提取是很有帮助的。
![](http://upload-images.jianshu.io/upload_images/2256672-958f31b01695b085.gif)以上就是卷积层的计算方法。这里面体现了**局部连接**和**权值共享**：每层神经元只和上一层部分神经元相连(卷积计算规则)，且filter的权值对于上一层所有神经元都是一样的。对于包含两个`3*3*3`的fitler的卷积层来说，其参数数量仅有`(3*3*3+1)*2=56`个，且参数数量与上一层神经元个数无关。与**全连接神经网络**相比，其参数数量大大减少了。

### 用卷积公式来表达卷积层计算(暂时跳过)
式4的表达很是繁冗，最好能简化一下。就像利用矩阵可以简化表达全连接神经网络的计算一样，我们利用卷积公式可以简化卷积神经网络的表达。
下面我们介绍二维卷积公式。
设矩阵A，B，其行、列数分别为![](https://img-blog.csdn.net/20180115082547691)、![](https://img-blog.csdn.net/20180115082558146)、![](https://img-blog.csdn.net/20180115082608199)、![](https://img-blog.csdn.net/20180115082618338)，则二维卷积公式如下：
![](https://img-blog.csdn.net/20180115082642970)
且使得满足条件![](https://img-blog.csdn.net/20180115082721477)
我们可以把上式写成![](https://img-blog.csdn.net/20180115082743120)
如果我们按照式5来计算卷积，我们可以发现矩阵A实际上是filter，而矩阵B是待卷积的输入，位置关系也有所不同：
![](http://upload-images.jianshu.io/upload_images/2256672-8d30f15073885d7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
从上图可以看到，A左上角的值![](https://img-blog.csdn.net/20180115082809330)与B对应区块中右下角![](https://img-blog.csdn.net/20180115082833025)的值相乘，而不是与左上角![](https://img-blog.csdn.net/20180115082900300)的相乘。因此，数学中的卷积和卷积神经网络中的『卷积』还是有区别的，为了避免混淆，我们把**卷积神经网络**中的『卷积』操作叫做互相关(cross-correlation)操作。
卷积和互相关操作是可以转化的。首先，我们把矩阵A翻转180度，然后再交换A和B的位置（即把B放在左边而把A放在右边。卷积满足交换率，这个操作不会导致结果变化），那么卷积就变成了互相关。
如果我们不去考虑两者这么一点点的区别，我们可以把式5代入到式4：
![](https://img-blog.csdn.net/20180115082934277)
其中，是卷积层输出的feature map。同式4相比，式6就简单多了。然而，这种简洁写法只适合步长为1的情况。
### Pooling层输出值的计算
Pooling层主要的作用是下采样，通过去掉Feature Map中不重要的样本，进一步减少参数数量。Pooling的方法很多，最常用的是Max Pooling。Max Pooling实际上就是在n*n的样本中取最大值，作为采样后的样本值。下图是2*2 max pooling：
![](http://upload-images.jianshu.io/upload_images/2256672-03bfc7683ad2e3ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
除了Max Pooing之外，常用的还有Mean Pooling——取各样本的平均值。
对于深度为D的Feature Map，各层独立做Pooling，因此Pooling后的深度仍然为D。
###  全连接层
[零基础入门深度学习(3) - 神经网络和反向传播算法](http://blog.csdn.net/luanpeng825485697/article/details/79009223)

## 卷积神经网络的训练
和**全连接神经网络**相比，卷积神经网络的训练要复杂一些。但训练的原理是一样的：

1. 利用链式求导计算损失函数对每个权重的偏导数（梯度），
2. 然后根据梯度下降公式更新权重。
3. 训练算法依然是反向传播算法。

### 神经网络和反向传播算法
整个算法分为三个步骤：

1. 前向计算每个神经元![](https://img-blog.csdn.net/20180115083129939)的输出值（j表示网络的第j个神经元，以下同）；
2. 反向计算每个神经元的误差项![](https://img-blog.csdn.net/20180115083208546)，在有的文献中也叫做敏感度(sensitivity)。它实际上是网络的损失函数对神经元加权输入![](https://img-blog.csdn.net/20180115083326036)的偏导数，即:![](https://img-blog.csdn.net/20180115083434404)
3. 计算每个神经元连接权重的梯度（表示从神经元i连接到神经元j的权重），公式为，其中，表示神经元i的输出。

最后，根据梯度下降法则更新每个权重即可。
对于卷积神经网络，由于涉及到**局部连接、下采样**的等操作，影响到了第二步**误差项![](https://img-blog.csdn.net/20180115083849542)的具体计算方法**，而**权值共享影响了第三步权重![](https://img-blog.csdn.net/20180115083949912)的梯度的计算方法**。接下来，我们分别介绍卷积层和Pooling层的训练算法。

### 卷积层的训练
对于卷积层，我们先来看看上面的第二步，即如何将误差项![](https://img-blog.csdn.net/20180115083849542)传递到上一层；然后再来看看第三步，即如何计算filter每个权值![](https://img-blog.csdn.net/20180115083949912)的梯度。
#### 卷积层误差项的传递
##### 最简单情况下误差项的传递
我们先来考虑步长为1、输入的深度为1、filter个数为1的最简单的情况。
假设输入的大小为`3*3`，filter大小为`2*2`，按步长为1卷积，我们将得到`2*2`的feature map。如下图所示：
![](http://upload-images.jianshu.io/upload_images/2256672-52295dad2641037f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
在上图中，为了描述方便，我们为每个元素都进行了编号。用![](https://img-blog.csdn.net/20180115084140183)表示第![](https://img-blog.csdn.net/20180115084218924)层第i行第j列的误差项；用![](https://img-blog.csdn.net/20180115084246597)表示filter第m行第n列权重，用![](https://img-blog.csdn.net/20180115084308647)表示filter的偏置项；用![](https://img-blog.csdn.net/20180115084354638)表示第![](https://img-blog.csdn.net/20180115084218924)层第i行第j列神经元的输出；用![](https://img-blog.csdn.net/20180115084427681)表示第![](https://img-blog.csdn.net/20180115084218924)行神经元的加权输入；用![](https://img-blog.csdn.net/20180115084518548)表示第l层第i行第j列的误差项；用![](https://img-blog.csdn.net/20180115084615779)表示第![](https://img-blog.csdn.net/20180115084218924)层的激活函数。它们之间的关系如下：
![](https://img-blog.csdn.net/20180115084640172)
上式中，![](https://img-blog.csdn.net/20180115084720056)、![](https://img-blog.csdn.net/20180115084748345)、![](https://img-blog.csdn.net/20180115084818865)都是数组，![](https://img-blog.csdn.net/20180115084748345)是![](https://img-blog.csdn.net/20180115084835449)由组成的数组，conv表示卷积操作。
在这里，我们假设第![](https://img-blog.csdn.net/20180115084543714)中的每个![](https://img-blog.csdn.net/20180115084955559)值都已经算好，我们要做的是计算第![](https://img-blog.csdn.net/20180115084218924)层每个神经元的误差项![](https://img-blog.csdn.net/20180115085015737)。
根据链式求导法则：
![](https://img-blog.csdn.net/20180115085035952)
![](https://img-blog.csdn.net/20180115085144383)
![](https://img-blog.csdn.net/20180115085220743)
![](https://img-blog.csdn.net/20180115085257382)
从上面三个例子，我们发挥一下想象力，不难发现，计算![](https://img-blog.csdn.net/20180115085352749)，相当于把第l层的sensitive map周围补一圈0，在与180度翻转后的filter进行cross-correlation，就能得到想要结果，如下图所示：
![](http://upload-images.jianshu.io/upload_images/2256672-2fb37b0a3ff0e1f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
因为卷积相当于将filter旋转180度的cross-correlation，因此上图的计算可以用卷积公式完美的表达：
![](https://img-blog.csdn.net/20180115085414018)
上式中的![](https://img-blog.csdn.net/20180115085432922)表示第![](https://img-blog.csdn.net/20180115085443988)层的filter的权重数组。也可以把上式的卷积展开，写成求和的形式：
![](https://img-blog.csdn.net/20180115085503736)
现在，我们再求第二项![](https://img-blog.csdn.net/20180115085532360)。因为![](https://img-blog.csdn.net/20180115085606788)
所以这一项极其简单，仅求激活函数![](https://img-blog.csdn.net/20180115085625646)的导数就行了。
![](https://img-blog.csdn.net/20180115085643812)
将第一项和第二项组合起来，我们得到最终的公式：
![](https://img-blog.csdn.net/20180115085709269)
也可以将式7写成卷积的形式：![](https://img-blog.csdn.net/20180115085733597)
其中，符号表![](https://img-blog.csdn.net/20180115085812725)示element-wise product，即将矩阵中每个对应元素相乘。注意式8中的![](https://img-blog.csdn.net/20180115085828281)、![](https://img-blog.csdn.net/20180115085840820)、![](https://img-blog.csdn.net/20180115085914035)都是矩阵。
以上就是步长为1、输入的深度为1、filter个数为1的最简单的情况，卷积层误差项传递的算法。下面我们来推导一下步长为S的情况.

#### 卷积步长为S时的误差传递
我们先来看看步长为S与步长为1的差别。
![](http://upload-images.jianshu.io/upload_images/2256672-754f37eb7603e99f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
如上图，上面是步长为1时的卷积结果，下面是步长为2时的卷积结果。我们可以看出，因为步长为2，得到的feature map跳过了步长为1时相应的部分。因此，当我们反向计算误差项时，我们可以对步长为S的sensitivity map相应的位置进行补0，将其『还原』成步长为1时的sensitivity map，再用式8进行求解。
#### 输入层深度为D时的误差传递
当输入深度为D时，filter的深度也必须为D，![](https://img-blog.csdn.net/20180115085942571)层的![](https://img-blog.csdn.net/20180115090033391)通道只与filter的通道![](https://img-blog.csdn.net/20180115090033391)的权重进行计算。因此，反向计算误差项时，我们可以使用式8，用filter的第![](https://img-blog.csdn.net/20180115090033391)通道权重对第![](https://img-blog.csdn.net/20180115090057475)层sensitivity map进行卷积，得到第![](https://img-blog.csdn.net/20180115090106537)层![](https://img-blog.csdn.net/20180115090033391)通道的sensitivity map。如下图所示：
![](http://upload-images.jianshu.io/upload_images/2256672-af2da9701a03dc3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
#### filter数量为N时的误差传递
filter数量为N时，输出层的深度也为N，第i个filter卷积产生输出层的第i个feature map。由于第![](https://img-blog.csdn.net/20180115090134108)层每个加权输入![](https://img-blog.csdn.net/20180115090247722)都同时影响了第![](https://img-blog.csdn.net/20180115090310191)层所有feature map的输出值，因此，反向计算误差项时，需要使用全导数公式。也就是，我们先使用第d个filter对第![](https://img-blog.csdn.net/20180115090310191)层相应的第d个sensitivity map进行卷积，得到一组N个![](https://img-blog.csdn.net/20180115090134108)层的偏sensitivity map。依次用每个filter做这种卷积，就得到D组偏sensitivity map。最后在各组之间将N个偏sensitivity map 按元素相加，得到最终的N个![](https://img-blog.csdn.net/20180115090134108)层的sensitivity map：
![](https://img-blog.csdn.net/20180115090402786)
以上就是卷积层误差项传递的算法，如果读者还有所困惑，可以参考后面的代码实现来理解。

#### 卷积层filter权重梯度的计算
我们要在得到第层sensitivity map的情况下，计算filter的权重的梯度，由于卷积层是权重共享的，因此梯度的计算稍有不同。
![](http://upload-images.jianshu.io/upload_images/2256672-afe6d3a863b7cbcc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
如上图所示，![](https://img-blog.csdn.net/20180115090523967)是第![](https://img-blog.csdn.net/20180115090537795)层的输出，![](https://img-blog.csdn.net/20180115090801070)是第l层filter的权重，是第l层的sensitivity map。我们的任务是计算![](https://img-blog.csdn.net/20180115090625906)的梯度，即![](https://img-blog.csdn.net/20180115090911701)。
为了计算偏导数，我们需要考察权重![](https://img-blog.csdn.net/20180115090625906)对![](https://img-blog.csdn.net/20180115090933516)的影响。权重项![](https://img-blog.csdn.net/20180115090625906)通过影响![](https://img-blog.csdn.net/20180115091038992)的值，进而影响![](https://img-blog.csdn.net/20180115090933516)。我们仍然通过几个具体的例子来看权重![](https://img-blog.csdn.net/20180115090625906)项对![](https://img-blog.csdn.net/20180115091038992)的影响，然后再从中总结出规律。
![](https://img-blog.csdn.net/20180115091118338)
从上面的公式看出，由于权值共享，权值![](https://img-blog.csdn.net/20180115091159303)对所有![](https://img-blog.csdn.net/20180115091213472)的都有影响。![](https://img-blog.csdn.net/20180115091225598)是每一个的![](https://img-blog.csdn.net/20180115091213472)函数，而每一个![](https://img-blog.csdn.net/20180115091213472)又是的![](https://img-blog.csdn.net/20180115091159303)函数，根据全导数公式，计算![](https://img-blog.csdn.net/20180115091343304)就是要把每个偏导数都加起来：![](https://img-blog.csdn.net/20180115091405511)
例2，计算![](https://img-blog.csdn.net/20180115091428661)：
通过查看![](https://img-blog.csdn.net/20180115091454609)与![](https://img-blog.csdn.net/20180115091213472)的关系，我们很容易得到：
![](https://img-blog.csdn.net/20180115091518934)
实际上，每个权重项都是类似的，我们不一一举例了。现在，是我们再次发挥想象力的时候，我们发现计算![](https://img-blog.csdn.net/20180115090911701)规律是：![](https://img-blog.csdn.net/20180115091600148)
也就是用sensitivity map作为卷积核，在input上进行cross-correlation，如下图所示：
![](http://upload-images.jianshu.io/upload_images/2256672-aeba8c8666a22e72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
最后，我们来看一看偏置项![](https://img-blog.csdn.net/20180115091648804)的梯度。通过查看前面的公式，我们很容易发现：![](https://img-blog.csdn.net/20180115091709205)
也就是偏置项的梯度就是sensitivity map所有误差项之和。
对于步长为S的卷积层，处理方法与传递**误差项**是一样的，首先将sensitivity map『还原』成步长为1时的sensitivity map，再用上面的方法进行计算。
获得了所有的梯度之后，就是根据梯度下降算法来更新每个权重。这在前面的文章中已经反复写过，这里就不再重复了。

至此，我们已经解决了卷积层的训练问题，接下来我们看一看Pooling层的训练。
### Pooling层的训练
无论`max pooling`还是`mean pooling`，都没有需要学习的参数。因此，在卷积神经网络的训练中，Pooling层需要做的仅仅是将误差项传递到上一层，而没有梯度的计算。
#### Max Pooling误差项的传递
如下图，假设第![](https://img-blog.csdn.net/20180115091731785)层大小为`4*4`，`pooling filter`大小为`2*2`，步长为2，这样，`max pooling`之后，第l层大小为`2*2`。假设第l层的![](https://img-blog.csdn.net/20180115091826316)值都已经计算完毕，我们现在的任务是计算第![](https://img-blog.csdn.net/20180115091731785)层的![](https://img-blog.csdn.net/20180115091826316)值。
![](http://upload-images.jianshu.io/upload_images/2256672-a30c883f19db53b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
我们用![](https://img-blog.csdn.net/20180115092007360)表示第l-1层的加权输入；用![](https://img-blog.csdn.net/20180115092036622)表示第l层的加权输入。我们先来考察一个具体的例子，然后再总结一般性的规律。对于max pooling：
![](https://img-blog.csdn.net/20180115092107090)
也就是说，只有区块中最大![](https://img-blog.csdn.net/20180115092007360)的才会对![](https://img-blog.csdn.net/20180115092036622)的值产生影响。我们假设最大的值是![](https://img-blog.csdn.net/20180115092036622)，则上式相当于：
![](https://img-blog.csdn.net/20180115092210308)
那么，我们不难求得下面几个偏导数：
![](https://img-blog.csdn.net/20180115092233932)
因此：![](https://img-blog.csdn.net/20180115092252830)而：![](https://img-blog.csdn.net/20180115092322505)
现在，我们发现了规律：对于max pooling，下一层的误差项的值会原封不动的传递到上一层对应区块中的最大值所对应的神经元，而其他神经元的误差项的值都是0。如下图所示(假设![](https://img-blog.csdn.net/20180115092425761)、![](https://img-blog.csdn.net/20180115092433497)、![](https://img-blog.csdn.net/20180115092440786)、![](https://img-blog.csdn.net/20180115092449384)为所在区块中的最大输出值)：
![](http://upload-images.jianshu.io/upload_images/2256672-af77e98c09fad84c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
#### Mean Pooling误差项的传递
我们还是用前面屡试不爽的套路，先研究一个特殊的情形，再扩展为一般规律。
![](http://upload-images.jianshu.io/upload_images/2256672-a30c883f19db53b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
同理我们发现了规律：对于mean pooling，下一层的误差项的值会平均分配到上一层对应区块中的所有神经元。如下图所示：![](http://upload-images.jianshu.io/upload_images/2256672-c3a6772cb07b416a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
上面这个算法可以表达为高大上的克罗内克积(Kronecker product)的形式，有兴趣的读者可以研究一下。
![](https://img-blog.csdn.net/20180115093011283)
其中，n是pooling层filter的大小![](https://img-blog.csdn.net/20180115093045140)，![](https://img-blog.csdn.net/20180115093100178)、都是矩阵。

至此，我们已经把卷积层、Pooling层的训练算法介绍完毕，加上上一篇文章讲的全连接层训练算法，您应该已经具备了编写卷积神经网络代码所需要的知识。为了加深对知识的理解，接下来，我们将展示如何实现一个简单的卷积神经网络。
## 卷积神经网络的实现
现在，我们亲自动手实现一个卷积神经网络，以便巩固我们所学的知识。

首先，我们要改变一下代码的架构，『层』成为了我们最核心的组件。这是因为卷积神经网络有不同的层，而每种层的算法都在对应的类中实现。

这次，我们用到了在python中编写算法经常会用到的numpy包。为了使用numpy，我们需要先将numpy导入：

``` Python
import numpy as np  

```
### 卷积层的实现
#### 卷积层初始化
我们用ConvLayer类来实现一个卷积层。下面的代码是初始化一个卷积层，可以在构造函数中设置卷积层的超参数。

```Python
# 用ConvLayer类来实现一个卷积层。  
class ConvLayer(object):  
    # 下面的代码是初始化一个卷积层，可以在构造函数中设置卷积层的超参数  
    def __init__(self, input_width, input_height,  
                 channel_number, filter_width,  
                 filter_height, filter_number,  
                 zero_padding, stride, activator,  
                 learning_rate):  
        self.input_width = input_width   #  输入宽度  
        self.input_height = input_height  # 输入高度  
        self.channel_number = channel_number  # 通道数=输入的深度=过滤器的深度  
        self.filter_width = filter_width  # 过滤器的宽度  
        self.filter_height = filter_height  # 过滤器的高度  
        self.filter_number = filter_number  # 过滤器的数量。  
        self.zero_padding = zero_padding  # 补0圈数  
        self.stride = stride # 步幅  
        self.output_width = int(ConvLayer.calculate_output_size(self.input_width, filter_width, zero_padding,stride))  # 计算输出宽度  
        self.output_height = int(ConvLayer.calculate_output_size(self.input_height, filter_height, zero_padding,stride))  # 计算输出高度  
        self.output_array = np.zeros((self.filter_number,self.output_height, self.output_width)) # 创建输出三维数组。每个过滤器都产生一个二维数组的输出  
        self.filters = []   # 卷积层的每个过滤器  
        for i in range(filter_number):  
            self.filters.append(Filter(filter_width,filter_height, self.channel_number))  
        self.activator = activator   # 使用rule激活器  
        self.learning_rate = learning_rate  # 学习速率  
```
calculate_output_size函数用来确定卷积层输出的大小，其实现如下：

```Python
# 确定卷积层输出的大小  
@staticmethod  
def calculate_output_size(input_size,filter_size, zero_padding, stride):  
    return (input_size - filter_size + 2 * zero_padding) / stride + 1 
```
Filter类保存了卷积层的参数以及梯度，并且实现了用梯度下降算法来更新参数。

``` Python
# Filter类保存了卷积层的参数以及梯度，并且实现了用梯度下降算法来更新参数。  
class Filter(object):  
    def __init__(self, width, height, depth):  
        self.weights = np.random.uniform(-1e-4, 1e-4,(depth, height, width))  # 随机初始化卷基层权重一个很小的值，  
        self.bias = 0  # 初始化偏量为0  
        self.weights_grad = np.zeros(self.weights.shape)   # 初始化权重梯度  
        self.bias_grad = 0  # 初始化偏量梯度  
  
    def __repr__(self):  
        return 'filter weights:\n%s\nbias:\n%s' % (repr(self.weights), repr(self.bias))  
  
    # 读取权重  
    def get_weights(self):  
        return self.weights  
  
    # 读取偏量  
    def get_bias(self):  
        return self.bias  
  
    # 更新权重和偏量  
    def update(self, learning_rate):  
        self.weights -= learning_rate * self.weights_grad  
        self.bias -= learning_rate * self.bias_grad
```
我们对参数的初始化采用了常用的策略，即：权重随机初始化为一个很小的值，而偏置项初始化为0。

ReluActivator类实现了relu激活函数，其中，forward方法实现了前向计算，而backward方法则是计算导数。比如，relu函数的实现如下:

```Python
# rule激活器  
class ReluActivator(object):  
    def forward(self, weighted_input):  # 前向计算，计算输出  
        return max(0, weighted_input)  
    def backward(self, output):  # 后向计算，计算导数  
        return 1 if output > 0 else 0 
```
IdentityActivator类实现f(x)=x激活函数，其中，forward方法实现了前向计算，而backward方法则是计算导数。

```Python
# IdentityActivator激活器.f(x)=x  
class IdentityActivator(object):  
    def forward(self, weighted_input):  # 前向计算，计算输出  
        return weighted_input  
    def backward(self, output):  # 后向计算，计算导数  
        return 1  
```
#### 卷积层前向计算的实现
ConvLayer类的forward方法实现了卷积层的前向计算（即计算根据输入来计算卷积层

```Python
# 计算卷积层的输出。输出结果保存在self.output_array  
    def forward(self, input_array):  
        self.input_array = input_array  # 多个通道的图片，每个通道为一个二维图片  
        self.padded_input_array = padding(input_array,self.zero_padding)  # 先将输入补足0  
        for i in range(self.filter_number):  #每个过滤器都产生一个二维数组的输出  
            filter = self.filters[i]  
            conv(self.padded_input_array,filter.get_weights(), self.output_array[i],self.stride, filter.get_bias())  
        # element_wise_op函数实现了对numpy数组进行按元素操作，并将返回值写回到数组中  
        element_wise_op(self.output_array,self.activator.forward)  
```
上面的代码里面包含了几个工具函数。element_wise_op函数实现了对numpy数组进行按元素操作，并将返回值写回到数组中，代码如下：

```Python 
# 对numpy数组进行逐个元素的操作。op为函数。element_wise_op函数实现了对numpy数组进行按元素操作，并将返回值写回到数组中  
def element_wise_op(array, op):  
    for i in np.nditer(array,op_flags=['readwrite']):  
        i[...] = op(i)   # 将元素i传入op函数，返回值，再修改i  
```
conv函数实现了2维和3维数组的卷积，代码如下：

```Python
# 计算一个过滤器的卷积运算，输出一个二维数据。每个通道的输入是图片，但是可能不是一个通道，所以这里自动适配输入为2D和3D的情况。  
def conv(input_array,kernel_array,output_array,stride, bias):  
    output_width = output_array.shape[1]   # 获取输出的宽度。一个过滤器产生的输出一定是一个通道  
    output_height = output_array.shape[0] # 获取输出的高度  
    kernel_width = kernel_array.shape[-1]  # 过滤器的宽度。有可能有多个通道。多通道时shape=[深度、高度、宽度]，单通道时shape=[高度、宽度]  
    kernel_height = kernel_array.shape[-2] # 过滤器的高度。有可能有多个通道。多通道时shape=[深度、高度、宽度]，单通道时shape=[高度、宽度]  
    for i in range(output_height):  
        for j in range(output_width):  
            juanjiqu = get_patch(input_array, i, j, kernel_width,kernel_height, stride)   # 获取输入的卷积区。（单通道或多通道）  
            # 这里是对每个通道的两个矩阵对应元素相乘求和，再将每个通道的和值求和  
            kernel_values= (np.multiply(juanjiqu,kernel_array)).sum() # 卷积区与过滤器卷积运算。1，一个通道内，卷积区矩阵与过滤器矩阵对应点相乘后，求和值。2、将每个通道的和值再求和。  
            output_array[i][j] = kernel_values + bias  #将卷积结果加上偏量  
```
padding函数实现了zero padding操作：

```
# 为数组增加Zero padding。zp步长，自动适配输入为2D和3D的情况  
def padding(input_array, zp):  
    if zp == 0: # 如果不补0  
        return input_array  
    else:  
        if input_array.ndim == 3:   # 如果输入有多个通道  
            input_width = input_array.shape[2]  # 获取输入的宽度  
            input_height = input_array.shape[1]  # 获取输入的宽度  
            input_depth = input_array.shape[0]  # 获取输入的深度  
            padded_array = np.zeros((input_depth,input_height + 2 * zp,input_width + 2 * zp))  # 先定义一个补0后大小的全0矩阵  
            padded_array[:,zp: zp + input_height,zp: zp + input_width] = input_array # 每个通道上，将中间部分替换成输入，这样就变成了原矩阵周围补0 的形式  
            return padded_array  
        elif input_array.ndim == 2:  # 如果输入只有一个通道  
            input_width = input_array.shape[1] # 获取输入的宽度  
            input_height = input_array.shape[0] # 虎丘输入的高度  
            padded_array = np.zeros((input_height + 2 * zp,input_width + 2 * zp))  # 先定义一个补0后大小的全0矩阵  
            padded_array[zp: zp + input_height,zp: zp + input_width] = input_array  # 将中间部分替换成输入，这样就变成了原矩阵周围补0 的形式  
            return padded_array  
```
#### 卷积层反向传播算法的实现
现在，是介绍卷积层核心算法的时候了。我们知道反向传播算法需要完成几个任务：

1. 将误差项传递到上一层。
2. 计算每个参数的梯度。
3. 更新参数。
以下代码都是在ConvLayer类中实现。我们先来看看将误差项传递到上一层的代码实现。

```
# 将误差项传递到上一层。sensitivity_array: 本层的误差。activator: 上一层的激活函数  
    def bp_sensitivity_map(self, sensitivity_array,activator):   # 公式9  
        # 根据卷积步长，对原始sensitivity map进行补0扩展，扩展成如果步长为1的输出误差形状。再用公式8求解  
        expanded_error_array = self.expand_sensitivity_map(sensitivity_array)  
        # print(sensitivity_array)  
        # full卷积，对sensitivitiy map进行zero padding  
        # 虽然原始输入的zero padding单元也会获得残差，但这个残差不需要继续向上传递，因此就不计算了  
        expanded_width = expanded_error_array.shape[2]   # 误差的宽度  
        zp = int((self.input_width + self.filter_width - 1 - expanded_width) / 2)   # 计算步长  
        padded_array = padding(expanded_error_array, zp)  #补0操作  
        # 初始化delta_array，用于保存传递到上一层的sensitivity map  
        self.delta_array = self.create_delta_array()  
        # 对于具有多个filter的卷积层来说，最终传递到上一层的sensitivity map相当于所有的filter的sensitivity map之和  
        for i in range(self.filter_number):   # 遍历每一个过滤器。每个过滤器都产生多通道的误差，多个多通道的误差叠加  
            filter = self.filters[i]  
            # 将滤波器每个通道的权重权重翻转180度。  
            flipped_weights=[]  
            for oneweight in filter.get_weights():  # 这一个滤波器下的每个通道都进行180翻转  
                flipped_weights.append(np.rot90(oneweight, 2))  
            flipped_weights = np.array(flipped_weights)  
            # 计算与一个filter对应的delta_array  
            delta_array = self.create_delta_array()  
            for d in range(delta_array.shape[0]):   # 计算每个通道上的误差，存储在delta_array的对应通道上  
                # print('大小：\n',flipped_weights[d])  
                conv(padded_array[i], flipped_weights[d],delta_array[d], 1, 0)  
            self.delta_array += delta_array   # 将每个滤波器每个通道产生的误差叠加  
  
        # 将计算结果与激活函数的偏导数做element-wise乘法操作  
        derivative_array = np.array(self.input_array)  # 复制一个矩阵，因为下面的会改变元素的值，所以深复制了一个矩阵  
        element_wise_op(derivative_array,activator.backward)  # 逐个元素求偏导数。  
        self.delta_array *= derivative_array  # 误差乘以偏导数。得到上一层的误差  
```
expand_sensitivity_map方法就是将步长为S的sensitivity map『还原』为步长为1的sensitivity map，代码如下

```Python
# 对步长为S的sensitivitymap相应的位置进行补0，将其『还原』成步长为1时的sensitivitymap，再用式8进行求解  
    def expand_sensitivity_map(self, sensitivity_array):  
        depth = sensitivity_array.shape[0]   # 获取误差项的深度  
        # 确定扩展后sensitivity map的大小，即计算stride为1时sensitivity map的大小  
        expanded_width = (self.input_width - self.filter_width + 2 * self.zero_padding + 1)  
        expanded_height = (self.input_height - self.filter_height + 2 * self.zero_padding + 1)  
        # 构建新的sensitivity_map  
        expand_array = np.zeros((depth, expanded_height, expanded_width))  
        # 从原始sensitivity map拷贝误差值，每有拷贝的位置，就是要填充的0  
        for i in range(self.output_height):  
            for j in range(self.output_width):  
                i_pos = i * self.stride  
                j_pos = j * self.stride  
                expand_array[:, i_pos, j_pos] = sensitivity_array[:, i, j]  
        return expand_array  
```
create_delta_array是创建用来保存传递到上一层的sensitivity map的数组

```
# 创建用来保存传递到上一层的sensitivity map的数组。（上一层的输出也就是这一层的输入。所以上一层的误差项的维度和这一层的输入的维度相同）  
   def create_delta_array(self):  
       return np.zeros((self.channel_number,self.input_height, self.input_width))  
```
接下来，是计算梯度的代码

```
# 计算梯度。根据误差值，计算本层每个过滤器的w和b的梯度  
    def bp_gradient(self, sensitivity_array):  
        # 处理卷积步长，对原始sensitivity map进行扩展  
        expanded_error_array = self.expand_sensitivity_map(sensitivity_array)  
        for i in range(self.filter_number):  # 每个过滤器产生一个输出  
            # 计算每个权重的梯度  
            filter = self.filters[i]  
            for d in range(filter.weights.shape[0]):   # 过滤器的每个通道都要计算梯度  
                conv(self.padded_input_array[d],expanded_error_array[i],filter.weights_grad[d], 1, 0)   #  公式（31、32中间）  
  
            # 计算偏置项的梯度  
            filter.bias_grad = expanded_error_array[i].sum()   # 公式（34）  
```
最后，是按照梯度下降算法更新参数的代码，这部分非常简单

```
# 按照梯度下降，更新权重  
def update(self):  
    for filter in self.filters:  
        filter.update(self.learning_rate)   # 每个过滤器  
```
#### 卷积层的梯度检查
为了验证我们的公式推导和代码实现的正确性，我们必须要对卷积层进行梯度检查。下面是代吗实现：

```
def init_test():  
    a = np.array(  # 作为输入  
        [[[0, 1, 1, 0, 2],  
          [2, 2, 2, 2, 1],  
          [1, 0, 0, 2, 0],  
          [0, 1, 1, 0, 0],  
          [1, 2, 0, 0, 2]],  
         [[1, 0, 2, 2, 0],  
          [0, 0, 0, 2, 0],  
          [1, 2, 1, 2, 1],  
          [1, 0, 0, 0, 0],  
          [1, 2, 1, 1, 1]],  
         [[2, 1, 2, 0, 0],  
          [1, 0, 0, 1, 0],  
          [0, 2, 1, 0, 1],  
          [0, 1, 2, 2, 2],  
          [2, 1, 0, 0, 1]]])  
    b = np.array(   # 作为输出误差  
        [[[0, 1, 1],  
          [2, 2, 2],  
          [1, 0, 0]],  
         [[1, 0, 2],  
          [0, 0, 0],  
          [1, 2, 1]]])  
    cl = ConvLayer(5, 5, 3, 3, 3, 2, 1, 2, IdentityActivator(), 0.001)  
    cl.filters[0].weights = np.array(   # 初始化第一层卷积层权重  
        [[[-1, 1, 0],  
          [0, 1, 0],  
          [0, 1, 1]],  
         [[-1, -1, 0],  
          [0, 0, 0],  
          [0, -1, 0]],  
         [[0, 0, -1],  
          [0, 1, 0],  
          [1, -1, -1]]], dtype=np.float64)  
    cl.filters[0].bias = 1  # 初始化第一层卷积层偏重  
    cl.filters[1].weights = np.array(   # 初始化第二层卷积层权重  
        [[[1, 1, -1],  
          [-1, -1, 1],  
          [0, -1, 1]],  
         [[0, 1, 0],  
          [-1, 0, -1],  
          [-1, 1, 0]],  
         [[-1, 0, 0],  
          [-1, 0, 1],  
          [-1, 0, 0]]], dtype=np.float64)  
    return a, b, cl  
  
  
# 测试前向传播  
def test():  
    a, b, cl = init_test()  
    cl.forward(a)     # 对输出进行以一次前向预测  
    # print(cl.output_array)  
  
# 测试后向传播  
def test_bp():  
    a, b, cl = init_test()  
    cl.backward(a, b, IdentityActivator())  # 对输出误差后向传播  
    cl.update()  # 跟新权重  
    # print(cl.filters[0])  # 查看说过更新一次后的滤波器权重  
    # print(cl.filters[1])  # 查看说过更新一次后的滤波器权重  
  
# 梯度检查  
def gradient_check():  
    # 设计一个误差函数，取所有节点输出项之和  
    error_function = lambda o: o.sum()  
  
    # 计算forward值  
    a, b, cl = init_test()  
    cl.forward(a)  # 对输入进行一次预测  
  
    # 求取sensitivity map  
    sensitivity_array = np.ones(cl.output_array.shape,dtype=np.float64)  
  
    # 计算梯度  
    cl.backward(a, sensitivity_array,IdentityActivator())  
    # 检查梯度  
    epsilon = 10e-4  
    for d in range(cl.filters[0].weights_grad.shape[0]):  
        for i in range(cl.filters[0].weights_grad.shape[1]):  
            for j in range(cl.filters[0].weights_grad.shape[2]):  
                cl.filters[0].weights[d, i, j] += epsilon  
                cl.forward(a)  
                err1 = error_function(cl.output_array)  
                cl.filters[0].weights[d, i, j] -= 2 * epsilon  
                cl.forward(a)  
                err2 = error_function(cl.output_array)  
                expect_grad = (err1 - err2) / (2 * epsilon)  
                cl.filters[0].weights[d, i, j] += epsilon  
                print('weights(%d,%d,%d): expected - actural %f - %f' % (d, i, j, expect_grad, cl.filters[0].weights_grad[d, i, j])) 
```
上面代码值得思考的地方在于，传递给卷积层的sensitivity map是全1数组，留给读者自己推导一下为什么是这样（提示：激活函数选择了identity函数：）。
运行上面梯度检查的代码，我们得到的输出如下，期望的梯度和实际计算出的梯度一致，这证明我们的算法推导和代码实现确实是正确的。
![](http://upload-images.jianshu.io/upload_images/2256672-c7071f47ea5f8f9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480) 以上就是卷积层的实现。

###  Max Pooling层的实现
max pooling层的实现相对简单，我们直接贴出全部代码如下：

```
# Max Pooling层的实现。就是一个卷积区域取最大值，形成输出。除了Max Pooing之外，常用的还有Mean Pooling——取各样本的平均值。  
class MaxPoolingLayer(object):  
    def __init__(self, input_width, input_height,  
                 channel_number, filter_width,  
                 filter_height, stride):  
        self.input_width = input_width  
        self.input_height = input_height  
        self.channel_number = channel_number  
        self.filter_width = filter_width  
        self.filter_height = filter_height  
        self.stride = stride  
        self.output_width = int((input_width -filter_width) / self.stride + 1)  
        self.output_height = int((input_height -filter_height) / self.stride + 1)  
        self.output_array = np.zeros((self.channel_number,self.output_height, self.output_width))  
  
    # 前向计算。  
    def forward(self, input_array):  
        for d in range(self.channel_number):  
            for i in range(self.output_height):  
                for j in range(self.output_width):  
                    self.output_array[d, i, j] = (get_patch(input_array[d], i, j,self.filter_width,self.filter_height,self.stride).max())   # 获取卷积区后去最大值  
  
    # 后向传播更新w和b  
    def backward(self, input_array, sensitivity_array):  
        self.delta_array = np.zeros(input_array.shape)  
        for d in range(self.channel_number):  
            for i in range(self.output_height):  
                for j in range(self.output_width):  
                    patch_array = get_patch(input_array[d], i, j,self.filter_width,self.filter_height,self.stride)  # 获取卷积区  
                    k, l = get_max_index(patch_array)  # 获取最大值的位置  
                    self.delta_array[d,i * self.stride + k,j * self.stride + l] = sensitivity_array[d, i, j]   # 更新误差
```
全连接层的实现和上一篇文章类似，在此就不再赘述了。至此，你已经拥有了实现了一个简单的卷积神经网络所需要的基本组件。对于卷积神经网络，现在有很多优秀的开源实现，因此我们并不需要真的自己去实现一个。贴出这些代码的目的是为了让我们更好的了解卷积神经网络的基本原理。

## 卷积神经网络的应用
MNIST手写数字识别
LeNet-5是实现手写数字识别的卷积神经网络，在MNIST测试集上，它取得了0.8%的错误率。LeNet-5的结构如下：![](http://upload-images.jianshu.io/upload_images/2256672-31b42c6c9daa16a4.png)
关于LeNet-5的详细介绍，网上的资料很多，因此就不再重复了。感兴趣的读者可以尝试用我们自己实现的卷积神经网络代码去构造并训练LeNet-5（当然代码会更复杂一些）。

