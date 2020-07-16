---
title: TensorFlowFederatedTutorial
date: 2019-05-09 22:30:31
tags: [coding, 联合学习]
category: [Tensorflow] 
---

# Federated Learning for Image Classification

注意：此colab已经过验证，可以与tensorflow_federated pip包的0.4.0版本一起使用，但Tensorflow Federated项目仍处于预发布版开发阶段，可能无法在master上运行。

在本教程中，我们使用**经典的MNIST培训示例来介绍TFF的联合学习（FL）API层**，`tff.learning`-一组更高级别的接口，可用于执行常见类型的联合学习任务，例如联合训练，针对TensorFlow中实现的用户提供的模型。

<!--more-->

本教程和Federated Learning API主要面向希望将自己的TensorFlow模型插入TFF，将后者主要视为黑盒子的用户。有关TFF以及如何实现自己的联合学习算法的更深入理解，请参阅FC Core API上的教程 - 自定义联合算法第1部分和第2部分。

有关tff.learning的更多信息，请继续使用Federated Learning for Text Generation，该教程除了涵盖周期性模型之外，还演示加载预训练的序列化Keras模型，以便使用联合学习结合使用Keras进行评估。

## Before we start

在开始之前，请运行以下命令以确保您的环境设置正确。如果您没有看到问候语，请参阅“安装指南”以获取相关说明。

## Preparing the input data

让我们从数据开始。**联合学习需要联合数据集**，即来自多个用户的数据集合。联合数据通常是非i.i.d.，这带来了一系列独特的挑战。

为了便于实验，我们在TFF存储库中添加了一些数据集，包括MNIST的联合版本，其中包含已使用Leaf(62 different classes (10 digits, 26 lowercase, 26 uppercase), images are 28 by 28 pixels (with option to make them all 128 by 128 pixels), 3500 users)重新处理的原始NIST数据集的版本，以便数据由原始作者键入。数字。由于每个作者都有一个独特的风格，这个数据集展示了非i.i.d的类型。联合数据集的预期行为。

这是我们如何加载它。

```
#@test {"output": "ignore"}
emnist_train, emnist_test = tff.simulation.datasets.emnist.load_data()
```

load_data（）返回的数据集是**tff.simulation.ClientData的实例**，这个接口允许您枚举用户集，**构造表示特定用户数据的tf.data.Dataset**，并查询个别元素的结构。以下是如何使用此界面探索数据集的内容。请记住，虽然此接口允许您迭代客户端ID，但这只是模拟数据的一个功能。您很快就会看到，联合学习框架不使用客户端身份 - 它们的唯一目的是允许您选择数据的子集进行模拟。

```
len(emnist_train.client_ids)
3383

emnist_train.output_types, emnist_train.output_shapes
(OrderedDict([(u'label', tf.int32), (u'pixels', tf.float32)]),
 OrderedDict([(u'label', TensorShape([])), (u'pixels', TensorShape([28, 28]))]))
 
example_dataset = emnist_train.create_tf_dataset_for_client(
    emnist_train.client_ids[0])

example_element = iter(example_dataset).next()

example_element['label'].numpy()
5
```

```
#@test {"output": "ignore"}
from matplotlib import pyplot as plt
plt.imshow(example_element['pixels'].numpy(), cmap='gray', aspect='equal')
plt.grid('off')
_ = plt.show()
```

在模拟中将联邦数据提供给TFF的方法之一就是Python列表，列表中的每个元素都包含单个用户的数据，无论是作为列表还是作为tf.data.Dataset。由于我们已经有一个提供后者的接口，让我们使用它。

这是一个简单的**辅助函数，它将构建一组给定用户的数据集列表，作为一轮培训或评估的输入**。

```
def make_federated_data(client_data, client_ids):
  return [preprocess(client_data.create_tf_dataset_for_client(x))
          for x in client_ids]
```

现在，我们如何选择客户？

在典型的联合训练场景中，我们正在处理可能非常大量的用户设备，其中只有一小部分可用于在给定时间点进行训练。例如，当客户端设备是仅在插入电源，关闭计量网络以及以其他方式空闲时参与训练的移动电话时就是这种情况。

当然，我们处于模拟环境中，所有数据都是本地可用的。通常情况下，在运行模拟时，我们**只需对客户的随机子集进行抽样，以参与每轮培训，每轮培训通常不同**。

也就是说，正如你可以通过研究联邦平均算法的论文所发现的那样，在每轮中随机抽样的客户子集的系统中实现收敛可能需要一段时间，并且必须运行数百轮是不切实际的。这个互动教程。

我们要做的是**对客户端集进行一次采样，并在多轮中重复使用相同的集合以加速收敛**（故意过度拟合这些少数用户的数据）。我们将其作为练习让读者修改本教程以模拟随机抽样 - 这很容易做到（一旦你这样做，请记住，让模型收敛可能需要一段时间）。

```
#@test {"output": "ignore"}
NUM_CLIENTS = 3

sample_clients = emnist_train.client_ids[0:NUM_CLIENTS]

federated_train_data = make_federated_data(emnist_train, sample_clients)

len(federated_train_data), federated_train_data[0]
```

## Creating a model with Keras

如果您使用Keras，您可能已经拥有构建Keras模型的代码。这是一个简单模型的例子，足以满足我们的需求。

```python
def create_compiled_keras_model():
  model = tf.keras.models.Sequential([
      tf.keras.layers.Dense(
          10, activation=tf.nn.softmax, kernel_initializer='zeros', input_shape=(784,))])
  
  def loss_fn(y_true, y_pred):
    return tf.reduce_mean(tf.keras.losses.sparse_categorical_crossentropy(
        y_true, y_pred))
 
  model.compile(
      loss=loss_fn,
      optimizer=gradient_descent.SGD(learning_rate=0.02),
      metrics=[tf.keras.metrics.SparseCategoricalAccuracy()])
  return model
```

关于编译的一个重要说明。当在Federated Averaging算法中使用时，如下所示，优化器只是总优化算法的一半，因为它仅用于计算每个客户端上的本地模型更新。算法的其余部分涉及如何通过客户端对这些更新进行平均，以及如何将它们应用于服务器上的全局模型。特别是，这意味着此处使用的优化器和学习速率的选择可能需要与您在标准i.i.d上训练模型的选择不同。数据集。我们建议从常规SGD开始，可能比平时学习率更低。我们在这里使用的学习率没有经过仔细调整，随意进行实验。

为了**使用任何带有TFF的模型，需要将其包装在tff.learning.Model接口**的实例中，该接口公开方法以标记模型的**正向传递，元数据属性等**，类似于Keras，但也引入了额外的元素，例如控制计算联合度量的过程的方法。我们暂时不要担心这个问题;如果你有一个我们上面刚刚定义的编译过的Keras模型，你可以**通过调用tff.learning.from_compiled_keras_model让TFF为你包装它**，将模型和样本数据批量作为参数传递，如下所示

```python
def model_fn():
  keras_model = create_compiled_keras_model()
  return tff.learning.from_compiled_keras_model(keras_model, sample_batch)
```

## Training the model on federated data

现在我们有一个包装为tff.learning.Model的模型用于TFF，我们可以通过调用辅助函数tff.learning.build_federated_averaging_process让TFF构造一个联合平均算法，如下所示。

请记住，参数需要是构造函数（例如上面的model_fn），而不是已经构造的实例，因此模型的构造可以在由TFF控制的上下文中发生（如果您对原因感到好奇）为此，我们建议您阅读有关自定义算法的后续教程。

```python
#@test {"output": "ignore"}
iterative_process = tff.learning.build_federated_averaging_process(model_fn)
```

刚刚发生了什么？ TFF构建了一对联合计算并将它们打包成tff.utils.IterativeProcess，其中这些计算可作为一对初始化和下一个属性使用。

简而言之，联合计算是TFF内部语言中可以表达各种联合算法的程序（您可以在自定义算法教程中找到更多相关信息）。在这种情况下，**生成并打包到iterative_process中的两个计算实现联合平均**。

TFF的目标是以**可以在真实的联合学习设置中执行的方式定义计算**，但是**目前仅实现本地执行模拟运行时**。要在模拟器中执行计算，只需像Python函数一样调用它。此默认解释环境不是为高性能而设计的，但它足以满足本教程的需要;我们希望提供更高性能的仿真运行时，以便在未来版本中进行更大规模的研究。

让我们从初始化(initialize)计算开始。与所有联合计算的情况一样，您可以将其视为一种函数。计算不带参数，并返回一个**结果-服务器上联合平均过程的状态**表示。虽然我们不想深入了解TFF的细节，但看看这种状态看起来可能是有益的。您可以将其可视化如下。

```
#@test {"output": "ignore"}
str(iterative_process.initialize.type_signature)

'( -> <model=<trainable=<dense/kernel=float32[784,10],dense/bias=float32[10]>,non_trainable=<>>,optimizer_state=<int64>>@SERVER)'
```

虽然上面的类型签名可能起初看起来有点神秘，但您可以认识到服务器状态包含一个模型（MNIST的初始模型参数将分发给所有设备），以及optimizer_state（由服务器维护的附加信息，例如用于超参数调度表的轮数等。

Let's invoke the `initialize` computation to construct the server state.

```
state = iterative_process.initialize()
```

接下来，这对联合计算中的第二个表示单轮联合平均,next，其包括将服务器状态（包括模型参数）推送到客户端，对其本地数据进行设备上**培训，收集和平均模型更新，并在服务器上生成新的更新模型**。

从概念上讲，您可以将next视为具有如下功能类型签名。

```
SERVER_STATE, FEDERATED_DATA -> SERVER_STATE, TRAINING_METRICS
```

特别是，应该考虑next（）不是作为在服务器上运行的函数，而是作为整个分散计算的声明性功能表示 - 一些输入由服务器（SERVER_STATE）提供，但每个参与设备提供自己的本地数据集。

让我们进行一轮培训，并将结果可视化。我们可以将上面已经生成的联合数据用于用户样本。

```
#@test {"timeout": 600, "output": "ignore"}
state, metrics = iterative_process.next(state, federated_train_data)
print('round  1, metrics={}'.format(metrics))
```

```
round  1, metrics=<sparse_categorical_accuracy=0.142909,loss=3.14069>
```

让我们再进行几轮。如前所述，通常在此时，您将从每轮随机选择的新用户样本中选择一部分模拟数据，以模拟用户不断进出的实际部署，但在此交互式笔记本中，为了演示，我们将重用相同的用户，以便系统快速收敛。

```
#@test {"skip": true}
for round_num in range(2, 11):
  state, metrics = iterative_process.next(state, federated_train_data)
  print('round {:2d}, metrics={}'.format(round_num, metrics))
```

```
round  2, metrics=<sparse_categorical_accuracy=0.166909,loss=2.90004>
round  3, metrics=<sparse_categorical_accuracy=0.203273,loss=2.64551>
round  4, metrics=<sparse_categorical_accuracy=0.248364,loss=2.41201>
round  5, metrics=<sparse_categorical_accuracy=0.291636,loss=2.19657>
round  6, metrics=<sparse_categorical_accuracy=0.341818,loss=1.99344>
round  7, metrics=<sparse_categorical_accuracy=0.397455,loss=1.81096>
round  8, metrics=<sparse_categorical_accuracy=0.446182,loss=1.65356>
round  9, metrics=<sparse_categorical_accuracy=0.486182,loss=1.51823>
round 10, metrics=<sparse_categorical_accuracy=0.533455,loss=1.39974>
```

每轮联合训练后训练损失减少，表明模型正在趋同。这些培训指标有一些重要的注意事项，但请参阅本教程后面的评估部分。



# Custom Federated Algorithms, Part 1: Introduction to the Federated Core

本教程是两部分系列的第一部分，演示**如何使用联合核心（FC）**在TensorFlow Federated（TFF）中实现自定义类型的联合算法 - 这是一组作为基础的低级接口。我们已经实现了**联邦学习（FL）层**。

第一部分更具概念性;我们介绍了TFF中使用的一些**关键概念和编程抽象**，并且我们**使用温度传感器的分布式阵列演示了它们在一个非常简单的例子中的用法**。在本系列的第二部分中，我们使用我们在此介绍的机制来**实现联合训练和评估算法的简单版本**。作为后续行动，我们鼓励您研究tff.learning中联邦平均的实施。

在本系列的最后，您应该能够认识到Federated Core的应用程序不一定仅限于学习。我们提供的编程抽象是非常通用的，并且可以用于例如实现分布式数据上的分析和其他定制类型的计算。

虽然本教程是独立设计的，但我们鼓励您首先阅读有关图像分类和文本生成的教程，以便更高层次，更温和地介绍TensorFlow联合框架和联合学习API（tff.learning），它将帮助您将我们在此描述的概念放在上下文中。

## Intended Uses[预期用途]

简而言之，**Federated Core（FC）是一种开发环境，可以紧凑地表达将TensorFlow代码与分布式通信运算符相结合的程序逻辑**，例如在联合平均中使用的运算符如：计算分布式和，平均值和其他类型分布式聚合在系统中的一组客户端设备上，向这些设备分发**模型和参数**等。

你可能知道tf.contrib.distribute，在这一点上要问的一个自然问题可能是：这个框架在哪些方面有所不同？毕竟，两个框架都**试图分发TensorFlow计算**。

考虑它的一种方法是，虽然tf.contrib.distribute的既定目标是**允许用户使用现有模型和培训代码**进行最小的更改以实现分布式培训，并且**更加关注如何利用分布式基础架构为了提高现有培训代码**的效率，TFF联邦核心的目标是让研究人员和从业人员**明确控制他们将在系统中使用的分布式通信的具体模式**。 FC的重点是**提供一种灵活且可扩展的语言来表达分布式数据流算法**，而不是一组具体的已实现的分布式训练功能。

TFF FC API的主要目标受众之一是**研究人员和从业人员，他们可能希望尝试新的联合学习算法，并评估影响分布式系统中数据流的编排方式的微妙设计选择的后果，不受系统实现细节的困扰**。 FC API的目标抽象级别大致对应于**伪代码**，可用于描述研究出版物中联合学习算法的机制 - 系统中存在哪些数据以及如何转换，但不会降低到单个点对点网络消息交换。

TFF作为一个整体是针对数据被分发的场景，并且必须保持这样，例如出于隐私原因，并且在集中位置收集所有数据可能不是可行的选择。与其中所有数据可以在数据中心的集中位置累积的情况相比，这**暗示了需要更高程度的显式控制的机器学习算法的实现**。

## Before we start

Before we dive into the code, please try to run the following "Hello World" example to make sure your environment is correctly setup. If it doesn't work, please refer to the [Installation](https://www.tensorflow.org/federated/install) guide for instructions.

```Python
#@test {"skip": true}

# NOTE: If you are running a Jupyter notebook, and installing a locally built
# pip package, you may need to edit the following to point to the '.whl' file
# on your local filesystem.

!pip install -q tensorflow_federated
```

```python
from __future__ import absolute_import, division, print_function

import numpy as np
from six.moves import range
import tensorflow as tf

from tensorflow_federated import python as tff

tf.enable_resource_variables()

@tff.federated_computation
def hello_world():
  return 'Hello, World!'

hello_world()

Hello, World!
```

## Federated data

TFF的一个显着特点是它允许您在**联合数据上紧凑地表达基于TensorFlow的计算**。我们将在本教程中使用术语联合数据来引用分布式系统中一组设备上托管的数据项集合。例如，在移动设备上运行的应用程序可以收集数据并将其存储在本地，而无需上传到集中位置。或者，**分布式传感器阵列可以收集并存储其位置处的温度读数**。

像上面例子中那样的联合数据在TFF中被视为一等公民[first-class citizens](https://en.wikipedia.org/wiki/First-class_citizen)，即它们可能作为**参数和函数结果**出现，并且它们具有类型。为了强化这一概念，我们将**联合数据集称为联合值或联合类型的值** 。*federated values*; *values of federated types*; 

需要理解的重点是，我们将所有设备上的**整个数据项集合（例如，分布式阵列中所有传感器的整个集合温度读数）建模为单个联合值**。

例如，以下是如何在TFF中定义由一组客户端设备托管的 *federated float*的类型。可以**将在一组分布式传感器中实现的温度读数的集合建模为该联合类型的值**。

```python
federated_float_on_clients = tff.FederatedType(tf.float32, tff.CLIENTS)
```

更一般地，TFF中的(federated type)联合类型是通过指定其**成员成分的类型T**（驻留在各个设备上的数据项）以及托管**此类(federated values)联合值的设备组G**（以及第三个，我们将在**短期内提及的可选信息**。我们将托管联合值的**设备组G称为值的位置**。因此，tff.CLIENTS是展示位置的示例。

```
str(federated_float_on_clients.member)
'float32'

str(federated_float_on_clients.placement)
'CLIENTS'
```

具有**成员成分T和位置G的联合类型**可以紧凑地表示为{T} @G，如下所示

```
str(federated_float_on_clients)

'{float32}@CLIENTS'
```

这个简洁符号中的花括号{}提醒您，**成员组成部分（不同设备上的数据项）可能会有所不同**，正如您所期望的那样，例如温度传感器读数，因此客户端作为一个组的拥有联合多个 T型项目 —— 一起构成联合值的T型项目。

值得注意的是，联合值的**成员组成部分通常对程序员不透明**，即联合值不应被视为用系统中设备的标识符作为键的简单dict  - **这些值旨在仅由抽象地表示集体转换，是通过各种分布式通信协议（例如聚合）的联合操作 *federated operators***。如果这听起来过于抽象，请不要担心 - 我们将很快回到这一点，我们将用具体的例子来说明它。 these values are intended to be collectively transformed only by *federated operators* that abstractly represent various kinds of distributed communication protocols (such as aggregation).

TFF中的联合类型有两种形式：联合值的成员组成部分可能不同（如上所述），以及已知它们全部相等的类型。这由tff.FederatedType构造函数中的第三个可选的all_equal参数控制（默认为False）

```
federated_float_on_clients.all_equal
```

具有放置G的federated type联合类型，其中**已知所有T型成员成分相等，可以紧凑地表示为T @ G（与{T} @G相对**，即，花括号去掉以反映多组成员组成部分由单个项目组成的事实。

```
str(tff.FederatedType(tf.float32, tff.CLIENTS, all_equal=True))
'float32@CLIENTS'
```

在实际场景中可能出现的这种类型的**联合值的一个示例是超参数**（诸如学习速率，规范等），其已经**由服务器分发到参与联合训练的一组设备**。

另一个示例是用于在**服务器处预训练的机器学习模型的一组参数**，然后将其广播到一组客户端设备，其中它们可以针对每个用户进行个性化。

例如，假设我们有一对float32参数a和b用于简单的一维线性回归模型。我们可以构建（非联合）类型的此类模型以在TFF中使用，如下所示。打印类型字符串中的**角度括号<>是命名或未命名元组的紧凑TFF表示法**。

```
simple_regression_model_type = (
    tff.NamedTupleType([('a', tf.float32), ('b', tf.float32)]))

str(simple_regression_model_type)
```

```
'<a=float32,b=float32>'
```

请注意，我们仅指定上面的dtypes。还支持非标量类型。在上面的代码中，tf.float32是更通用的tff.TensorType（dtype = tf.float32，shape = []）的快捷符号。

将此模型广播给客户端时，生成的联合值的类型可以表示如下。

```
str(tff.FederatedType(
    simple_regression_model_type, tff.CLIENTS, all_equal=True))
   
'<a=float32,b=float32>@CLIENTS'
```

对于上面使用联合浮点数的对称性，我们将这种类型称为联合元组。更一般地说，我们经常使用术语联合XYZ来指代成员组成部分类似于XYZ的联合值。因此，我们将讨论**联合元组，联合序列，联合模型等内容**。

现在，回到float32 @ CLIENTS  - 虽然它似乎在多个设备上复制，但它实际上是一个float32，因为所有成员都是相同的。一般来说，您可能会想到任何所有相等的联合类型，即T @ G形式之一，与非联合类型T同构，因为在这两种情况下，实际上只有一个（尽管可能是复制的）项目T型。

鉴于T和T @ G之间的同构，您可能想知道后者类型可能起什么作用（如果有的话）。继续阅读。.

## Placements

### Design Overview

在上一节中，我们介绍了**放置的概念 - 可能共同托管联合值**的系统参与者组，我们已经演示了如何使用tff.CLIENTS作为放置的示例规范。

为了解释为什么放置Placements的概念是如此基本以至于我们需要将它结合到TFF类型系统中，回想一下我们在本教程开头提到的关于TFF的一些预期用途的内容。

虽然在本教程中，您将只看到在模拟环境中本地执行的TFF代码，但我们的目标是让**TFF能够编写您可以部署的代码，以便在分布式系统中的物理设备组上执行，可能包括移动或嵌入式设备运行Android**。**这些设备中的每一个将接收一组单独的指令以在本地执行**，这取决于它在系统中扮演的角色（**最终用户设备，集中协调器，多层体系结构中的中间层等**）。重要的是能够推断哪些设备子集执行什么代码，以及**数据的不同部分可能在物理上实现的位置**。

在处理例如移动设备上的应用数据时，这尤其重要。由于数据是私有的并且可能是敏感数据，因此我们需要能够静态验证此数据永远不会离开设备（并证明有关数据处理方式的事实）。 placement specifications放置规范是旨在支持此功能的机制之一。

TFF被设计为一个以数据为中心的编程环境，因此，与一些专注于操作和可能运行这些操作的现有框架不同，**TFF专注于数据，数据的实现以及数据的转换方式**。因此，placement放置被建模为TFF中**数据的属性，而不是作为数据操作的属性**。实际上，正如您将在下一节中看到的那样，**一些TFF操作跨越各个位置，并且“在网络中”运行，可以这么说，而不是由单个机器或一组机器执行**。

将某个值的类型表示为T @ G或{T} @G（而不仅仅是T）使数据放置决策明确，并且与TFF编写的程序的静态分析一起，它可以作为提供的基础敏感的设备数据的正式隐私保证。

然而，在这一点上需要注意的一件重要事情是，虽然我们**鼓励TFF用户明确表示托管数据的参与设备组（展示位置**），但程序员永远**不会处理个人参与者的原始数据或身份**。

（注意：虽然它远远超出了本教程的范围，但我们应该提到上面有一个值得注意的例外，一个tff.federated_collect运算符用作低级原语，仅用于特殊情况。在不建议可以避免的情况下，因为它可能限制未来可能的应用程序。例如，如果在静态分析过程中，我们确定计算使用这种低级机制，我们可能会禁止它访问某些数据类型。）

在TFF代码的主体内，按照设计，**无法枚举构成由tff.CLIENTS表示的组的设备，或探测组中特定设备的存在**。在Federated Core API，没有任何设备或客户端标识的概念。架构抽象的基础集或我们提供的支持模拟的核心运行时基础架构中。您编写的**所有计算逻辑将表示为整个客户端组上的操作**。

回想一下我们之前提到的关于联邦类型的值与Python dict不同的内容，因为它不能简单地枚举它们的成员组成部分。将您的**TFF程序逻辑操作的值视为与展示位置（组）相关联**，而不是与单个参与者相关联。

Placements 展示位置也被设计为TFF中的一等公民，并且可以作为展示位置类型的参数和结果显示（由API中的**tff.PlacementType**表示）。将来，我们计划提供各种运算符来转换或组合展示位置，但这超出了本教程的范围。目前，在TFF中将place放置为不透明的原始内置类型就足够了，类似**于int和bool是Python中不透明的内置类型**，tff.CLIENTS是这种类型的常量文字，与1不同是int类型的常量文字。

### Specifying Placements[具体]

TFF提供了两个基本的放置文字，即**tff.CLIENTS和tff.SERVER**，可以很容易地表达丰富多样的实际场景，这些场景自然地建模为**客户端 - 服务器架构，具有多个客户端设备（移动电话，嵌入式设备，分布式数据库） ，传感器等）由单个中央服务器协调器编排**。 TFF还支持自定义放置，多个客户端组，多层和其他更通用的分布式体系结构，但讨论它们超出了本教程的范围。

TFF没有规定tff.CLIENTS或tff.SERVER实际代表什么。

特别是，**tff.SERVER可能是单个物理设备**（单个组的成员），但它也可能是运行状态机复制的容错集群中的一组副本 - 我们不做任何特殊的体系结构假设。相反，我们使用前一节中提到的all_equal位来表示我们通常只处理服务器上的单个数据项。

同样，某些应用程序中的tff.CLIENTS可能代表系统中的所有客户端 - 在联邦学习的上下文中我们有时称之为人口，但是例如，在联合平均的生产实现中，它可能代表**一个群组 - 一个子集选择参加特定轮次培训的客户**。抽象定义的展示位置在其出现的计算被部署用于执行时（或者在模拟环境中简单地像Python函数一样调用，如本教程中所示）具有具体含义。在我们的本地模拟中，**客户端组由作为输入提供的联合数据确定**。

## Federated computations

### Declaring federated computations

TFF被设计为**支持模块化开发的强类型函数编程环境**。

TFF中的基本组合单元是联合计算*federated computation* - 可以**接受federated values联合值作为输入并将联合值作为输出返回的逻辑部分**。以下是如何定义计算，计算上一个示例中传感器阵列报告的温度平均值。

```python
@tff.federated_computation(tff.FederatedType(tf.float32, tff.CLIENTS))
def get_average_temperature(sensor_readings):
  return tff.federated_mean(sensor_readings)
```

看看上面的代码，此时您可能会问 - 是不是已经有装饰器构造来定义TensorFlow中的可组合单元，如tf.contrib.eager.defun，如果是这样，为什么要引入另一个，以及如何它不同？

简短的回答是，tff.federated_computation**包装器生成的代码既不是TensorFlow，也不是Python  - 它是内部平台独立粘合语言中的分布式系统规范**。在这一点上，这无疑听起来很神秘，但请将联邦计算的这种直观解释作为分布式系统的抽象规范。我们马上解释一下。

首先，让我们来看一下这个定义。 TFF计算通常被**建模为函数** - 有或没有参数，但**具有明确定义的类型签名**。您可以通过查询其type_signature属性来打印计算的类型签名，如下所示。

```
str(get_average_temperature.type_signature)

'({float32}@CLIENTS -> float32@SERVER)'
```

类型签名告诉我们，**计算接受客户端设备上不同传感器读数的集合，并在服务器上返回单个平均值**。

在我们进一步讨论之前，让我们反思一下 - **这个计算的输入和输出位于不同的地方（在CLIENTS和SERVER上）**。回想一下我们在前一节中关于**TFF操作如何跨越位置并在网络中运行的放置**，以及**我们刚才所说的关于联邦计算表示分布式系统的抽象规范的内容**。我们只定义了一个这样的计算 - **一个简单的分布式系统，其中数据在客户端设备上消耗，并且聚合结果出现在服务器上**。

在许多实际场景中，表示顶级任务的计算将倾向于接受其输入并在服务器上报告其输出 - 这反映了计算可能由在服务器上发起和终止的查询触发的想法。

但是，FC API没有强加这个假设，**我们内部使用的许多构建块**（包括API中可能找到的许多tff.federated _...运算符）都有**不同位置的输入和输出**，所以一般来说，你**应该不要将联合计算视为在服务器上运行或由服务器执行的事物**。服务器只是联合计算中的一种参与者。在考虑这种计算的机制时，最好始终默认为全局网络范围的视角，而不是单个集中协调器的视角。

通常，功能类型签名分别紧凑地表示为**输入和输出的类型T和U的（T→U）**。形式参数的类型（在本例中为sensor_readings）被指**定为装饰器的参数**。您无需指定结果的类型 - 它是自动确定的。

尽管TFF确实提供了有限形式的多态性，但强烈建议程序员明确它们使用的数据类型，因为这样可以更容易地理解，调试和正式验证代码的属性。在某些情况下，明确指定类型是一项要求（例如，多态计算当前不能直接执行）。

### Executing federated computations[执行]

为了支持开发和调试，**TFF允许您直接调用以Python函数的方式定义的计算**，如下所示。如果计算需要将all_equal位设置为False的联合类型的值，则可以将其作为Python中的普通列表提供，对于all_equal位设置为True的联合类型，您可以直接提供（单个）成员组成。这也是结果报告给您的方式。

```
get_average_temperature([68.5, 70.3, 69.8])

69.533333
```

在仿真模式下运行这样的计算时，您可以充当具有系统范围视图的外部观察者，他可以在网络中的任何位置提供输入和消耗输出，就像这里的情况一样 - 您提供了客户端值在输入，并消耗服务器结果。

现在，让我们回到我们之前关于tff.federated_computation装饰器的注释，该装饰器以粘合语言发出代码。虽然TFF计算的逻辑可以表示为Python中的普通函数（你只需要像我们上面所做的那样使用tff.federated_computation来装饰它们），你可以直接使用Python参数调用它们，就像这里的任何其他Python函数一样。笔记本，在幕后，正如我们前面提到的，TFF计算实际上不是Python。

我们的意思是**当Python解释器遇到用tff.federated_computation修饰的函数时，它会跟踪此函数体中的语句一次（在定义时），然后构造计算逻辑的序列化表示以供将来使用 - 是否用于执行，或作为子组件合并到另一个计算中**。

您可以通过添加print语句来验证这一点，如下所示：

```python
@tff.federated_computation(tff.FederatedType(tf.float32, tff.CLIENTS))
def get_average_temperature(sensor_readings):

  print ('Getting traced, the argument is "{}".'.format(
      type(sensor_readings).__name__))

  return tff.federated_mean(sensor_readings)

Getting traced, the argument is "ValueImpl".
```

您可以考虑定义联合计算的Python代码，类似于您在非热切的上下文中构建TensorFlow图的Python代码的方式（如果您不熟悉TensorFlow的非急切使用，请考虑您的Python代码定义了稍后要执行的操作图，但实际上并未实际运行它们。 TensorFlow中非热切的图形构建代码是Python，但由此**代码构造的TensorFlow图是独立于平台且可序列化的**。

同样，**TFF计算是在Python中定义的，但是它们体内的Python语句（例如我们刚才显示的示例中的tff.federated_mean）被编译成一个可移植的，与平台无关的可序列化表示**。

作为开发人员，您不需要关心此表示的详细信息，因为您永远不需要直接使用它，但您应该意识到它的存在，事实上TFF计算从根本上不急于求成，并且无法捕获任意Python状态。包含在TFF计算主体中的Python代码在定义时执行，此时在序列化之前跟踪用tff.federated_computation修饰的Python函数的主体。它不会在调用时再次回溯（除非函数是多态的;有关详细信息，请参阅文档页面）。

您可能想知道为什么我们选择引入专用的内部非Python表示。一个原因是最终，TFF计算旨在可部署到真实的物理环境，并托管在可能无法使用Python的移动或嵌入式设备上。

另一个原因是**TFF计算表达了分布式系统的全局行为，而不是表达个体参与者的本地行为的Python程序**。您可以在上面的简单示例中看到，使用特殊运算符tff.federated_mean接受客户端设备上的数据，但将结果存储在服务器上。

**???运算符tff.federated_mean不能轻易地在Python中建模为普通运算符**，因为它**不在本地执行 - 如前所述，它代表一个协调多个系统参与者行为的分布式系统**。我们将这些运算符称为联合运算符，以区别于Python中的普通（本地）运算符。

TFF类型系统以及TFF语言支持的基本操作集因此与Python中的操作明显不同，因此需要使用专用表示。

### Composing federated computations[组成]

如上所述，**联邦计算及其组成部分**最好被理解为**分布式系统的模型，您可以将组合联合计算视为从更简单的系统组成更复杂的分布式系统**。您可以将tff.federated_mean运算符视为一种**带有类型签名的内置模板联合计算（{T} @CLIENTS  - > T @ SERVER）**（实际上，就像您编写的计算一样，此运算符也具有复杂性结构 - 在引擎盖下我们将其分解为更简单的操作符）。

组合联合计算也是如此。计算get_average_temperature可以在用tff.federated_computation修饰的另一个Python函数的主体中调用 - 这样做会使它嵌入父类的主体中，就像之前tff.federated_mean嵌入它自己的主体一样。

**要注意的一个重要限制是用tff.federated_computation修饰的Python函数体必须只包含联合运算符**，即它们不能直接包含TensorFlow操作。例如，您不能直接使用tf.contrib.nest接口添加一对联合值。 TensorFlow代码必须限制为使用下一节中讨论的tff.tf_computation修饰的代码块。只有在以这种方式包装时，才能在tff.federated_computation的主体中调用包装的TensorFlow代码。

这种分离的原因是技术性的（很难欺operator如tf.add与非张量一起工作）以及架构。**联合计算的语言（即，使用tff.federated_computation修饰的Python函数的序列化主体构造的逻辑）被设计为用作独立于平台的粘合语言**。这种粘合语言目前用于从TensorFlow代码的嵌入部分构建分布式系统（仅限于tff.tf_computation块）。在充足的时间内，我们预计需要嵌入其他非TensorFlow逻辑的部分，例如可能代表输入管道的关系数据库查询，所有这些都使用相同的粘合语言（tff.federated_computation块）连接在一起。

## TensorFlow logic

### Declaring TensorFlow computations[申明]

TFF设计用于TensorFlow。因此，您将在TFF中编写的大部分代码可能是普通的（即本地执行的）TensorFlow代码。为了在TFF中使用这样的代码，如上所述，它只需要用tff.tf_computation进行修饰。

For example, here's how we could implement a function that takes a number and adds `0.5` to it.

```
@tff.tf_computation(tf.float32)
def add_half(x):
  return tf.add(x, 0.5)
```

再一次，看看这个，你可能想知道为什么我们应该定义另一个装饰器tff.tf_computation而不是简单地使用现有的机制，如tf.contrib.eager.defun。与前一节不同，这里我们处理一个普通的TensorFlow代码块。

这有几个原因，其全部处理超出了本教程的范围，但值得命名的主要有两个：

- 为了**嵌入在联邦计算体中使用TensorFlow代码实现的可重用构建块**，它们需要满足某些属性 - 例如在定义时**获取跟踪和序列化，具有类型签名**等。这通常需要某种形式的装饰器。
- 此外，TFF**需要能够接受数据流（表示为tf.data.Datasets）的计算能力**，例如机器学习应用程序中的训练示例批处理流，作为输入或输出。 TensorFlow目前不存在此功能; tff.tf_computation装饰器为它提供部分（现在仍然是实验性的）支持。

通常，我们建**议尽可能使用TensorFlow的组合本机机制**，例如tf.contrib.eager.defun，因为可以预期TFF的装饰器与渴望函数交互的确切方式。

现在，回到上面的示例代码片段，我们刚刚定义的计算add_half可以像任何其他TFF计算一样由TFF处理。特别是，它具有TFF类型签名。

```
str(add_half.type_signature)

'(float32 -> float32)'
```

请注意，此类型签名没有展示位置。 TensorFlow计算不能使用或返回联合类型。

您现在还可以将add_half用作其他计算中的构建块。例如，**以下是如何使用tff.federated_map运算符将add_half逐点应用于客户端设备上联合浮点数的所有成员组成部分**。

```
@tff.federated_computation(tff.FederatedType(tf.float32, tff.CLIENTS))
def add_half_on_clients(x):
  return tff.federated_map(add_half, x)
  
  str(add_half_on_clients.type_signature)
  
  '({float32}@CLIENTS -> {float32}@CLIENTS)'
```

### Executing TensorFlow computations[执行]

用`tff.tf_computation`定义的计算的执行遵循与我们为`tff.federated_computation`描述的规则相同的规则。它们可以在Python中作为普通的callable调用，如下所示。

```
add_half_on_clients([1.0, 3.0, 2.0])

[1.5, 3.5, 2.5]
```

再一次，值得注意的是，以这种方式调用计算add_half_on_clients模拟了一个分散的过程。**数据在客户端上使用，并在客户端上返回**。实际上，**该计算使每个客户端执行本地动作。在这个系统中没有明确提到的tff.SERVER**（即使在实践中，编排这样的处理可能涉及一个）。将这种定义的计算视为概念上类似于MapReduce中的Map阶段。

另外，请记住，我们在上一节中所说的关于TFF计算在定义时序列化的内容对于tff.tf_computation代码也是如此 -  add_half_on_clients的Python主体在定义时被跟踪一次。在后续调用中，**TFF使用其序列化表示**。

用tff.federated_computation修饰的Python方法与用tff.tf_computation修饰的方法之间的唯一区别是后者被序列化为TensorFlow图形（而前者不允许包含直接嵌入其中的TensorFlow代码）。

在引擎盖下，用tff.tf_computation修饰的每个方法暂时禁用急切执行，以便捕获计算的结构。虽然急切执行是在本地禁用的，但欢迎使用急切的TensorFlow，AutoGraph，TensorFlow 2.0构造等，只要您以一种能够正确序列化的方式编写计算逻辑。

For example, the following code will fail:

```
#@test {"output": "ignore"}

try:

  # Eager mode
  constant_10 = tf.constant(10.)

  @tff.tf_computation(tf.float32)
  def add_ten(x):
    return x + constant_10

except Exception as err:
  print (err)
  
Tensor("Const_1:0", shape=(), dtype=float32) must be from the same graph as Tensor("arg:0", shape=(), dtype=float32).
```

上面的失败是因为constant_10已经在图形之外构造了tff.tf_computation在序列化过程中在add_ten体内部构造。

另一方面，调用在tff.tf_computation中调用时修改当前图形的python函数很好：

```
def get_constant_10():
  return tf.constant(10.)

@tff.tf_computation(tf.float32)
def add_ten(x):
  return x + get_constant_10()

add_ten(5.0)

15.0
```

请注意，TensorFlow中的序列化机制正在发展，我们期望TFF如何序列化计算的细节也在不断发展。

### Working with `tf.data.Dataset`s

如前所述，tff.tf_computations的一个独特功能是它们允许您**使用代码抽象定义的tf.data.Datasets作为形式参数**。需要使用tff.SequenceType构造函数声明要在TensorFlow中表示为数据集的参数。

例如，**类型规范tff.SequenceType（tf.float32）定义了TFF中的float元素的抽象序列**。序列可以包含张量或复杂的嵌套结构（我们稍后会看到这些结构的例子）。一系列T型项目的简明表示是T *。

```
float32_sequence = tff.SequenceType(tf.float32)

str(float32_sequence)
'float32*'
```

假设在我们的温度传感器示例中，每个传感器不仅保持一个温度读数，而且保持多个。以下是如何在TensorFlow中定义TFF计算，该计算使**用tf.data.Dataset.reduce运算符计算单个本地数据集中的平均温度**。

```python
@tff.tf_computation(tff.SequenceType(tf.float32))
def get_local_temperature_average(local_temperatures):
  sum_and_count = (
      local_temperatures.reduce((0.0, 0), lambda x, y: (x[0] + y, x[1] + 1)))
  return sum_and_count[0] / tf.to_float(sum_and_count[1])

str(get_local_temperature_average.type_signature)
'(float32* -> float32)'
```

在**用tff.tf_computation修饰的方法体中，TFF序列类型的形式参数简单地表示为行为类似于tf.data.Dataset的对象，即支持相同的属性和方法**（它们目前不作为子类实现）该类型 - 这可能随着TensorFlow中数据集的支持的发展而改变）。

您可以按如下方式轻松验证。

```
@tff.tf_computation(tff.SequenceType(tf.int32))
def foo(x):
  return x.reduce(np.int32(0), lambda x, y: x + y)

foo([1, 2, 3])
6
```

请记住，与普通的tf.data.Datasets不同，这些类似数据集的对象是占位符。它们不包含任何元素，因为它们表示抽象的序列类型参数，在具体上下文中使用时绑定到具体数据。对于抽象定义的占位符数据集的支持在这一点上仍然有限，并且在TFF的早期，您可能会遇到某些限制，但我们在本教程中不需要担心它们（请参阅文档页面）详情）。

当在本地执行接受模拟模式中的序列的计算时，例如在本教程中，您可以将序列作为Python列表提供，如下所示（以及其他方式，例如，作为tf.data.Dataset in eager模式，但是现在，我们会保持简单）。

```
get_local_temperature_average([68.5, 70.3, 69.8])

69.533333
```

与所有其他TFF类型一样，上面定义的序列可以使用tff.NamedTupleType构造函数来定义嵌套结构。例如，以下**是如何声明接受A，B序列的计算的计算，并返回其产品的总和。我们在计算主体中包含跟踪语句**，以便您可以看到TFF类型签名如何转换为数据集的output_types和output_shapes。

```
@tff.tf_computation(tff.SequenceType([('A', tf.int32), ('B', tf.int32)]))
def foo(ds):
  print ('output_types = {}, shapes = {}'.format(
      ds.output_types, ds.output_shapes))
  return ds.reduce(np.int32(0), lambda total, x: total + x['A'] * x['B'])
  
output_types = OrderedDict([('A', tf.int32), ('B', tf.int32)]), shapes = OrderedDict([('A', TensorShape([])), ('B', TensorShape([]))])

str(foo.type_signature)
'(<A=int32,B=int32>* -> int32)'

foo([{'A': 2, 'B': 3}, {'A': 4, 'B': 5}])
26
```

使用tf.data.Datasets作为形式参数的支持仍然有些限制和发展，尽管在本教程中使用的简单场景中起作用。

## Putting it all together

现在，让我们再次尝试在**联合设置中使用TensorFlow计算**。假设我们有一组传感器，每个传感器都有一个本地的温度读数序列。我们**可以通过平均传感器的局部平均值来计算全球温度平均值**，如下所示。

```python
@tff.federated_computation(
    tff.FederatedType(tff.SequenceType(tf.float32), tff.CLIENTS))
def get_global_temperature_average(sensor_readings):
  return tff.federated_mean(
      tff.federated_map(get_local_temperature_average, sensor_readings))
```

请注意，这不是来自所有客户的所有本地温度读数的简单平均值，因为这将需要根据它们在本地维护的读数的数量来衡量来自不同客户的贡献。我们将其作为练习让读者更新上述代码; tff.federated_mean运算符接受权重作为可选的第二个参数（预期是联合浮点数）。

另请注意，get_global_temperature_average的**输入现在变为联合int序列**。联**合序列是我们通常在联合学习中表示设备上数据的方式，序列元素通常表示数据批次**（您很快就会看到这样的示例）。

```
str(get_global_temperature_average.type_signature)

'({float32*}@CLIENTS -> float32@SERVER)'
```

**以下是我们如何在Python中本地执行数据样本的计算**。请注意，我们提供输入的方式现在是列表形式。外部列表遍历由tff.CLIENTS表示的组中的设备，**内部的迭代遍历每个设备的本地序列中的元素**。

```
get_global_temperature_average([[68.0, 70.0], [71.0], [68.0, 72.0, 70.0]])

70.0
```

他总结了本教程的第一部分......我们鼓励你继续第二部分。