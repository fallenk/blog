---
title: TensorflowFederatedGuide
date: 2019-05-09 16:27:39
tags: [联合学习]
category: [Tensorflow]   
---

# Overview

TensorFlow联合（TFF）平台由两层组成：

- 联合学习（FL），将现有Keras或非Keras机器学习模型插入TFF框架的高级接口。您可以执行基本任务，例如**联合训练或评估**，而无需研究联合学习算法的详细节。
- 联合核心（FC），通过在强类型函数编程环境中将**TensorFlow与分布式通信运算符**相结合，简洁地表达自定义联合算法的低级接口。

<!--more-->

首先阅读以下教程，使用实际示例向您介绍主要的TFF概念和API。确保按照安装说明配置环境以与TFF一起使用。

- 用图像分类的**联合学习介绍了联合学习**（FL）API的关键部分，并演示了如何使用TFF模拟联合类MNIST数据的联合学习。
- 用于文本生成的联合学习进一步演示了如何使用TFF的FL API来优化语言建模任务的序列化预训练模型。
- 自定义联合算法，第1部分：联合核心简介和第2部分：实现联合平均，介绍了**联合核心API（FC API）提供的关键概念和接口**，并演示了如何实现简单的联合平均训练算法以及如何执行联合评估。

# Installation

```Python
virtualenv --python python3 "venv"
source "venv/bin/activate"
pip install --upgrade pip
pip install --upgrade tensorflow_federated
```

# Federated Learning

### Overview

本文档介绍了促进联合学习任务的接口，例如联合训练或使用TensorFlow中实现的现有机器学习模型进行评估。在设计这些接口时，我们的主**要目标是使其能够在不需要了解其工作原理的情况下进行联邦学习**的实验，并评估在各种现有模型和数据上实施的联合学习算法。我们鼓励您回馈该平台。 TFF的设计考虑了可扩展性和可组合性，我们欢迎贡献;我们很高兴看到你想出了什么！

该层提供的接口包括以下三个关键部分：

- **Models**: **Classes and helper functions that allow you to wrap your existing models for use with TFF**。包装模型可以像调用单个包装函数（例如，tff.learning.from_keras_model）一样简单，也可以定义tff.learning.Model接口的子类以实现完全可定制性。
- **Federated Computation Builders**. 辅助函数，使用**现有模型**构建用于训练或评估的联合计算。
- Datasets: 可以在Python中下载和访问的罐装数据集，用于模拟联合学习方案。虽然联邦学习设计用于分散数据，无法简单地在集中位置下载，但在研究和开发阶段，使用可在本地下载和操作的数据进行初始实验通常很方便，特别是对于可能是方法的新手。

这些**接口主要在tff.learning命名空间中定义**，但除研究数据集和已在tff.simulation中分组的其他模拟相关功能外。该层FL使用Federated Core（FC）提供的低级接口实现，该接口还提供运行时环境。

在继续之前，我们建议您首先查看有关图像分类和文本生成的教程，因为它们使用具体示例介绍了此处描述的大多数概念。如果您有兴趣了解有关TFF如何工作的更多信息，您可能希望略过自定义算法教程and作为我们用来表达联邦计算逻辑的低级接口的介绍，并研究现有的实现。 tff.learning接口。

### Models

#### Architectural assumptions

#### Serialization[序列化]

TFF旨在支持各种分布式学习场景，其中您编写的机器学习模型代码可能在具有不同功能的大量异构客户端上执行。虽然在一端，但在某些应用中，这些客户端可能是功能强大的数据库服务器，我们平台打算支持的许多重要用途涉及资源有限的移动和嵌入式设备。我们不能假设这些设备能够托管Python运行时;我们现在唯一可以假设的是它们能够托管本地TensorFlow运行时。因此，我们在TFF中做出的基本架构假设是您的**模型代码必须可序列化为TensorFlow图**。

您可以（并且应该）按照最新的最佳实践（如使用预先模式）开发TF代码。但是，**最终代码必须是可序列化的**（例如，可以包装为eager-mode代码的tf.function）。这可以确保执行时所需的任何Python状态或控制流都可以序列化（可能在Autograph的帮助下）。

Currently, TensorFlow does not fully support serializing and deserializing eager-mode TensorFlow. 因此，TFF中的序列化目前遵循TF 1.0模式，其中**所有代码必须在TFF控制的tf.Graph内构建**。这意味着**目前TFF不能使用已经构建的模型;相反，模型定义逻辑打包在一个返回tff.learning.Model的无参数函数中。然后，TFF调用此函数以确保模型的所有组件都已序列化**。此外，作为强类型环境，TFF将需要一些额外的元数据，例如模型输入类型的规范。 *使用 tff.learning.Model来包装模型*

#### Aggregation[聚合]

我们强烈建议大多数用户使用Keras构建模型，请参阅下面的Converters for Keras部分。这些包装器自动化处理**模型更新的聚合以及模型定义的任何度量**。但是，了解如何处理常规**tff.learning.Model**的聚合可能仍然有用。

联合学习中始终至少有两层聚合：本地设备上聚合和跨设备（或联合）聚合：

- **本地聚合**。此聚合级别是指由**单个客户端拥有的多批示例之间的聚合**。它适用于模型参数（变量），它们随着模型的本地训练而继续顺序演变，以及您计算的统计数据（例如平均损失，准确度和其他指标），您的模型将再次在本地更新因为它遍历每个客户端的本地数据流。
  - 在此级别执行**聚合是模型代码的责任，并使用标准TensorFlow构造完成**
  - 处理的一般结构如下：
    - 该模型**首先构造tf.Variables以保存聚合**，例如batches次数或处理的示例数，每批或每例损失的总和等。
    - TFF多次在TF模型上调用forward_pass方法，依次在后续批次的客户端数据上调用，这**允许您更新包含各种聚合的变量作为副作用**。
    - 最后，**TFF在您的模型上调用`report_local_outputs`方法**，以允许您的模型将其收集的所有摘要统计的信息编译为一组紧凑的度量标准，以供客户端导出。例如，**您的模型代码可以将损失总和除以处理的示例数量，以输出平均损失等**。

- **联合聚合**。此聚合级别是指系统中多**个客户端（设备）之间的聚合**。同样，它适用于在客户端之间上进行平均的模型参数（变量）和模型作为本地聚合的结果导出的度量。
  - 在此级别**执行聚合是TFF的责任**。但是，作为模型创建者，您可以控制此过程（以下更多内容）。
  - 处理的一般结构如下：
    - 初**始模型和培训所需的任何参数**由服务器**分发**给将参与一轮培训或评估的客户端子集。
    - 在**每个客户端上**，独立地并行地，**在本地数据批次流上重复调用模型代码以生成一组新的模型参数**（在培训时），以及一组新的本地度量标准，如上所述（这是本地的聚合）。
    - **TFF运行分布式聚合协议**，以在整个系统中**累积和聚合模型参数和本地导出的指标**。**在Model的`federated_output_computation`中，使用TFF自己的联合计算语言（不在TensorFlow中）以声明方式表示此逻辑**。有关聚合API的更多信息，请参阅[custom algorithms](https://www.tensorflow.org/federated/tutorials/custom_federated_algorithms_1) 。

### Abstract interfaces[接口tff.learning.Model]

此基本构造函数+元数据接口由**接口tff.learning.Model**表示实现，如下所示：

- The constructor，`forward_pass和report_local_outputs`方法应相应地**构造和导出的模型变量，正向传递和统计信息**。如上所述，由这些方法构造的TensorFlow必须是可序列化的。
- **input_spec属性**以及返回**可训练**，**不可训练**和**本地变量**子集的3个属性表示元数据。 TFF使用此信息来**确定如何将模型的各个部分连接到联合优化算法**，以及**定义内部类型签名以帮助验证构造系统的正确性**（以便您的模型不能通过与不匹配的数据相匹配的数据进行实例化该模型旨在消费）。

此外，抽象接口tff.learning.Model公开了一个属性`federated_output_computation`，它与前面提到的`report_local_outputs`属性一起去控制聚合摘要统计信息的过程。

最后，派生的抽象接口`tff.learning.TrainableModel`**允许您自定义TFF执行各个训练步骤的方式**，例如通过指定自己的优化器，或定义在训练之前和之后计算的单独度量。您可以通过覆盖新引入的抽象方法`train_on_batch`来自定义它。但是，如果您只打算使用模型进行评估，或者您满意TFF为您选择标准优化器，则不必这样做。

您可以在我们的[image classification](https://www.tensorflow.org/federated/tutorials/federated_learning_for_image_classification)教程的第二部分以及我们用于在[`model_examples.py`](https://github.com/tensorflow/federated/blob/master/tensorflow_federated/python/learning/model_examples.py)中进行测试的示例模型中找到如何定义自己的自定义tf.learning.Model的示例。

### Converters for Keras

几乎所有TFF所需的信息都可以通过调用tf.keras接口来获得，所以如果你有一个Keras模型，你可以依赖以下两种方法之一为你构建一个tff.learning.TrainableModel实例：

- `tff.learning.from_keras_model`
- `tff.learning.from_compiled_keras_model`

请注意，TFF仍然希望您提供构造函数 - 无参数模型函数，如下所示：

```python
def model_fn():
  keras_model = ...
  keras_model.compile(...)
  return tff.learning.from_compiled_keras_model(keras_model, sample_batch)
```

除了模型本身，您还需要**提供一组样本数据**，TFF使用这些数据来**确定模型输入的类型和形状**。这确保了**TFF可以正确地实例化实际存在于客户端设备上的数据的模型**（因为我们假设在构建要序列化的TensorFlow时，这些数据通常不可用）。

The use of Keras wrappers is illustrated in our [image classification](https://www.tensorflow.org/federated/tutorials/federated_learning_for_image_classification) and [text generation](https://www.tensorflow.org/federated/tutorials/federated_learning_for_text_generation) tutorials.

## Federated Computation Builders[tff.Computations]

tff.learning包为**执行学习相关任务的tff.Computations提供了几个builder**。我们希望未来可以扩展这类计算。

### Architectural assumptions

#### Execution[运行计算]

运行联合计算有两个不同的阶段。

- **编译**：TFF首先**将联合学习算法编译成整个分布式计算的抽象序列化表示**。这是TensorFlow序列化发生的时候，但是可以进行其他转换以支持更高效的执行。我们将编译器发出的**序列化表示**称为*federated computation*。
- **Execution** ：TFF提供了执行这些computations的方法。目前，仅通过本地模拟（例如，在使用模拟分散数据的Jupyter笔记本中）支持执行。

**A federated computation** generated by TFF's Federated Learning API,  such as a training algorithm that uses [federated model averaging](https://arxiv.org/abs/1602.05629), or a federated evaluation, includes a number of elements, most notably:

- **A serialized form of your model code** as well as additional TensorFlow code constructed by the Federated Learning framework to **drive your model's training/evaluation loop** (such as constructing optimizers, applying model updates, iterating over `tf.data.Dataset`s, and computing metrics, and applying the aggregated update on the server, to name a few).模型的序列化形式以及联邦学习框架构建的其他TensorFlow代码，**用于驱动模型的训练/评估循环**(例如构建优化器，应用模型更新，迭代tf.data.Datasets和计算度量，以及在服务器上应用聚合更新，仅举几例）。
- A declarative specification of the communication between the *clients* and a *server* (typically various forms of *aggregation* across the client devices, and *broadcasting* from the server to all clients), and how this distributed communication is interleaved with the client-local or server-local execution of TensorFlow code.**客户端与服务器之间通信的声明性规范**（通常是客户端设备之间的各种形式的聚合，以及从服务器向所有客户端广播），以及此分布式通信如何与客户端本地或服务器本地执行交错TensorFlow代码。

以**序列化形式表示的*federated computations*** 以与Python不同的独立于平台的内部语言表示，但是要使用联合学习API，您无需关心此表示的详细信息。计算在您的Python代码中表示为tff.Computation类型的对象，在大多数情况下，您可以将其视为不透明的Python可调用对象。

在教程中，您将**调用那些federated computations**，就像它们是常规Python函数一样，**在本地执行**。然而，TFF**被设计为以对执行环境的大多数方面不可知的方式表达联合计算**，使得它们可以潜在地部署到例如运行Android的设备组或数据中心中的集群。同样，其主要结果是对序列化的强烈假设。特别是，当您调用下面描述的某个build _...方法时，计算将完全序列化(使用tff.learning.Model)。

#### Modeling state[建模状态]

TFF是一个功能性编程环境，但联邦学习中许多有关的过程都**是有状态**的。例如，**涉及多轮联合模型平均的训练迭代过程**是我们可以归类为**有状态过程**的一个示例。在该过程中，从一轮到另一轮发展的状态包括**正在训练的一组模型参数**，以及可能与优**化器相关联的附加状态**（例如，动量矢量）。

由于TFF是有功能性的，因此有**状态进程在TFF中被建模**为**接受当前状态作为输入**然后**提供更新状态作为输出**的计算。为了完全定义有状态进程，还需**要指定初始状态的来源**（否则我们无法引导进程）。这是在辅助类**tff.utils.IterativeProcess**的定义中捕获的，其中2个属性initialize，next分别对应于初始化和迭代。

### Available builders

目前，TFF提供了两个构建器函数，用于生成联合训练和评估的联合计算：

- `tff.learning.build_federated_averaging_process` takes a ***model function*, and returns a stateful** `tff.utils.IterativeProcess`.
- `tff.learning.build_federated_evaluation` **takes a *model function* and returns a single federated computation** for federated evaluation of models, since evaluation is not stateful.

## Datasets

### Architectural assumptions

#### Client selection

在典型的联合学习场景中，我们拥有大量潜在的数亿个客户端设备，其中只有一小部分可能处于活动状态且可在任何给定时刻进行培训（例如，这可能仅限于客户端插入电源，而不是计量网络，否则空闲）。通常，可用于参与培训或评估的客户端集合不受开发人员的控制。此外，由于协调数百万客户是不切实际的，因此**典型的一轮培训或评估将仅包括可用客户端的一小部分**，其可以随机采样。

这样做的关键结果是，联邦计算(federated computations,)在设计上以对参与者的确切集合无关的方式表达;所有处理都表示为一组抽象的匿名客户端上的聚合操作，并且该组可能会因一轮培训而异。因此，计算与具体参与者的实际绑定，以及因此它们输入计算的具体数据，在计算本身之外被建模。

为了模拟联邦学习代码的实际部署，您通常会编写一个如下所示的训练循环：

```python
trainer = tff.learning.build_federated_averaging_process(...)
state = trainer.initialize()
federated_training_data = ...

def sample(federate_data):
  return ...

while True:
  data_for_this_round = sample(federated_training_data)
  state, metrics = trainer.next(state, data_for_this_round)
```

为了实现这一点，在模拟中使用TFF时，联邦数据被接受为Python列表，每个参与的客户端设备有一个元素来表示该设备的本地tf.data.Dataset。

### Abstract interfaces

为了标准化处理模拟联邦数据集，TFF提供了一个抽象接口**tff.simulation.ClientData**，它允许人们**枚举客户端集，并构造一个包含特定客户端数据的tf.data.Datase**t。那些tf.data.Datasets可以作为输入直接馈送到急切模式下生成的联合计算。

应该注意的是，访问客户端身份的能力是仅由用于模拟的数据集提供的特征，其中可能需要训练来自客户的特定子集的数据的能力（例如，模拟不同的昼夜可用性）。客户类型）。**编译的计算和底层运行时不涉及客户端身份的任何概念**。一旦选择了来自特定客户端子集的数据作为输入，例如，在对tff.utils.IterativeProcess.next的调用中，客户端身份不再出现在其中。

### Available data sets

我们为实现**tff.simulation.ClientData**接口的数据集专门设置了**名称空间tff.simulation.datasets**，用于模拟，并将其与2个数据集一起播种，以支持图像分类和文本生成教程。我们建议您鼓励您将自己的数据集提供给平台。

# Federated Core

本文档介绍了TFF的核心层，它作为联合学习的基础，以及未来可能的非学习联合算法。

有关Federated Core的简要介绍，请阅读以下教程，因为它们通过示例介绍了一些基本概念，并逐步演示了简单联合平均算法的构建。

- [Custom Federated Algorithms, Part 1: Introduction to the Federated Core](https://www.tensorflow.org/federated/tutorials/custom_federated_algorithms_1).
- [Custom Federated Algorithms, Part 2: Implementing Federated Averaging](https://www.tensorflow.org/federated/tutorials/custom_federated_algorithms_2).

我们还鼓励您熟悉联合学习以及关于图像分类和文本生成的相关教程，因为联邦核心学习API（FC API）用于联合学习为我们在以下方面做出的一些选择提供了重要的背景。设计这一层。

## Overview

### Goals, Intended Uses, and Scope[目标，预期用途和范围]

联邦核心（FC）最好被理解为**用于实现分布式计算的编程环境**，即涉及可以**各自执行非计算机的多个计算机**（移动电话，平板电脑，嵌入式设备，台式计算机，传感器，数据库服务器等）的计算。**在本地进行简单处理，并通过网络进行通信以协调其工作**。

分布式术语非常通用，而TFF并未针对所有可能类型的分布式算法，因此我们更倾向于使用较不通用的术语:联合计算(*federated computation*)来**描述可在此框架中表达的算法类型**。

虽然以完全正式的方式定义术语联合计算超出了本文档的范围，但请考虑在描述新的分布式学习算法的[research publication](https://arxiv.org/pdf/1602.05629.pdf)中您可能在伪代码中看到的算法类型。

FC的目标，就是在类似 **伪代码的抽象级别上实现类似的紧凑表示**，不是伪代码的程序逻辑，而是在各种目标环境中可执行的。

FC旨在表达的各种算法的关键定义特征是**系统参与者的动作以集体方式描述**。因此，我们倾向于讨论每个设备在本地转换数据，并且多个设备通过集中协调器**协调工作，广播，收集或聚合其结果**。

虽然TFF的设计能够超越简单的客户端 - 服务器架构，但**集体处理的概念才是最基本**的。这是由于联邦学习中TFF的起源，联合学习是一种最初设计用于支持对仍然受客户端设备控制的潜在敏感数据的计算的技术，并且出于隐私原因可能不会简单地下载到集中式位置。虽然此类系统中的每个客户端都有助于计算系统结果的数据和处理能力（我们通常期望这对所有参与者都有价值），但我们也努力保护每个客户的隐私和匿名性。

因此，尽管分布式计算的大多数框架被设计为从个体参与者的角度表达处理 - 即，在单个点对点消息交换的层面上，以及参与者的本地状态转换与传入和传出消息的相互依赖性。 ，TFF的Federated Core旨在从全球系统的角度描述系统的行为（类似于MapReduce）。

因此，虽然用于一般目的的分布式框架可以提供诸如**发送和接收之类的操作作为构建块**，但FC提供诸如**tff.federated_sum**，**tff.federated_reduce**或**tff.federated_broadcast**的构建块，其封装简单的分布式协议。

## Language

### Python Interface

TFF使用内部语言来表示联合计算，其语法由[computation.proto](https://github.com/tensorflow/federated/blob/master/tensorflow_federated/proto/v0/computation.proto)中的可序列化表示定义。但是，FC API的用户通常不需要直接与该语言交互。相反，我们提供了一个Python API（tff命名空间），它围绕它作为定义计算的方式。

具体来说，TFF**提供Python函数装饰器**，如**tff.federated_computation**，它**跟踪修饰函数的主体，并以TFF语言生成联合计算逻辑的序列化表示**。用**tff.federated_computation修饰的函数充当这种序列化表示的载体**，并且可以将其作**为构建块嵌入另一个计算的主体中**，或者**在调用时按需执行**。

Here's just one example; more examples can be found in the [custom algorithms](https://www.tensorflow.org/federated/tutorials/custom_federated_algorithms_1) tutorials.

```python
@tff.federated_computation(tff.FederatedType(tf.float32, tff.CLIENTS))
def get_average_temperature(sensor_readings):
  return tff.federated_mean(sensor_readings)
```

### 装饰器

注：装饰器**本质上是一个 Python 函数或类**，它可以让其他函数或类在不需要做任何代码修改的前提下**增加额外功能**，装饰器的返回值也是一个**函数/类对象**。它经常用于有切面需求的场景，比如：插入日志、性能测试、事务处理、缓存、权限校验等场景，装饰器是解决这类问题的绝佳设计。有了装饰器，我们就可以抽离出大量与函数功能本身无关的雷同代码到装饰器中并继续重用。概括的讲，**装饰器的作用就是为已经存在的对象添加额外的功能**。

谈装饰器前，还要先要明白一件事，Python 中的函数和 Java、C++不太一样，Python 中的函数可以像普通变量一样当做参数传递给另外一个函数，例如：

```
def foo():
    print("foo")

def bar(func):
    func()

bar(foo)
```

#### 简单装饰器

```Python
def use_logging(func):

    def wrapper():
        logging.warn("%s is running" % func.__name__)
        return func()   # 把 foo 当做参数传递进来时，执行func()就相当于执行foo()
    return wrapper

def foo():
    print('i am foo')

foo = use_logging(foo)  # 因为装饰器 use_logging(foo) 返回的时函数对象 wrapper，这条语句相当于  foo = wrapper
foo()
```

use_logging 就是一个装饰器，它一个普通的函数，它把执行真正业务逻辑的函数 func 包裹在其中，看起来像 foo 被 use_logging 装饰了一样，use_logging 返回的也是一个函数，这个函数的名字叫 wrapper。在这个例子中，函数进入和退出时 ，被称为一个横切面，这种编程方式被称为面向切面的编程。

#### @ 语法糖

如果你接触 Python 有一段时间了的话，想必你对 @ 符号一定不陌生了，没错 @ 符号就是装饰器的语法糖，它放在函数开始定义的地方，这样就可以**省略最后一步再次赋值的操作**。

```
def use_logging(func):

    def wrapper():
        logging.warn("%s is running" % func.__name__)
        return func()
    return wrapper

@use_logging
def foo():
    print("i am foo")
foo()
```

#### 带参数的装饰器

```Python
def use_logging(level):
    def decorator(func):
        def wrapper(*args, **kwargs):
            if level == "warn":
                logging.warn("%s is running" % func.__name__)
            elif level == "info":
                logging.info("%s is running" % func.__name__)
            return func(*args)
        return wrapper

    return decorator

@use_logging(level="warn")
def foo(name='foo'):
    print("i am %s" % name)

foo()
```

上面的 use_logging 是**允许带参数的装饰器**。它实际上是**对原有装饰器的一个函数封装，并返回一个装饰器**。我们可以将它理解为一个含有参数的闭包。当我 们使用`@use_logging(level="warn")`调用的时候，Python 能够发现这一层的封装，并把参数传递到装饰器的环境中。

```
@use_logging(level="warn")`等价于`@decorator
```

熟悉 non-eager TensorFlow的读者会发现这种方法类似于编写Python代码，该代码在定义TensorFlow图的Python代码部分中使用**tf.add或tf.reduce_sum**等函数。虽然代码在技术上用Python表示，但其目的是在下面构造一个**tf.Graph的可序列化表示**，它是**由TensorFlow运行时内部执行的图形**，**而不是Python代码**。同样，可以将tff.federated_mean视为将联合op插入由get_average_temperature表示的联合计算中。

FC定义语言的部分原因与如下事实有关：如上所述，联合计算指定分布式集体行为，因此，它们的逻辑是非本地的。例如，TFF提供的operators，输入和输出可能存在于网络中的不同位置。

这需要一种捕获分布式概念的语言和类型系统。

### Type System[类型系统]

Federated Core提供以下类别的类型。在描述这些类型时，我们指向**类型构造函数**以及**引入紧凑符号**，因为它是一种方便的方式或描述计算和运算符的类型。

首先，以下是概念上与现有主流语言类似的类型类别：

- **Tensor types**（**tff.TensorType**）。就像在TensorFlow中一样，它们**具有dtype和shape**。唯一的区别是这种类型的对象不限于是Python中的**tf.Tensor实例**， which表示TensorFlow图中TensorFlow操作的输出，但也可以包括**可以生成的数据单元**，例如，作为分布式的输出聚合协议。因此，TFF张量类型只是Python或TensorFlow中此类型的具体物理表示的抽象版本。

  张量类型的紧凑符号是dtype或dtype [shape]。例如，int32和int32 [10]分别是整数和int向量的类型。

- Sequence types（tff.SequenceType）。这些是TFF的抽象等同于TensorFlow的tf.data.Datasets的具体概念。**序列的元素可以以顺序方式消费，并且可以包括复杂类型**。序列类型的紧凑表示是**T ***，其中**T是元素的类型**。例如，int32 *表示整数序列。

- Named tuple types命名元组类型（tff.NamedTupleType）。这些是TFFNamed tuple types的方式，这些结构具有预定义数量的具有特定类型（命名或未命名）的元素。重要的是，TFF的命名元组概念包含Python的参数元组的抽象等价物，即**元素的集合**，其中一些（但不是全部）被命名，一些是**位置元素**。

  命名元组的紧凑表示法是<n_1 = T_1，...，n_k = T_k>，其中n_k是**可选元素名称**，T_k是**元素类型**。例如，<int32，int32>是一对未命名整数的紧凑表示法，<X = float32，Y = float32>是一对名为X和Y的浮点的紧凑表示法，可以表示平面上的一个点。元组可以嵌套以及与其他类型混合，例如，<X = float32，Y = float32> *将是一系列点的紧凑符号。

- Function types函数类型（tff.FunctionType）。 TFF是一个函数式编程框架，其函数被视为[first-class values](https://en.wikipedia.org/wiki/First-class_citizen)。函数最多只有一个参数，而且只有一个结果。

  函数的紧凑表示法是（T  - > U），**其中T是参数的类型，U是结果的类型**，或者（ - > U）如果没有参数（尽管无参数函数是退化的）主要存在于Python级别的概念）。例如（int32 *  - > int32）是一种将整数序列减少为单个整数值的函数的表示法。

以下类型解决了TFF计算的分布式系统方面。由于这些概念在某种程度上与TFF无关，因此我们建议您参考[custom algorithms](https://www.tensorflow.org/federated/tutorials/custom_federated_algorithms_1)自定义算法教程以获取其他评论和示例。

- Placement type展示位置类型。此类型尚未在公共API中公开，而不是以2个文字tff.SERVER和tff.CLIENTS的形式公开，您可以将其视为此类型的常量。但是，它在内部使用，并将在将来的版本中引入公共API。这种类型的紧凑表示是放置。

  展示位置代表一组扮演特定角色的系统参与者。初始版本针对客户端 - 服务器计算，其中有2组参与者：客户端和服务器（您可以将后者视为单例组）。但是，在更复杂的体系结构中，可能还有其他角色，例如**多层系统中的中间聚合器，可能正在执行不同类型的聚合，或者使用与服务器或服务器使用的不同类型的数据压缩/解压缩。客户**。

  定义展示位置概念的主要目的是**作为定义联合类型的基础**。

- **Federated types**联合类型（tff.FederatedType）。联合类型的值是由**特定放置**（例如tff.SERVER或tff.CLIENTS）定义的一组系统参与者托管的值。联合类型由**放置值**（因此，它是依赖类型），**成员组成部分的类型**（每个参与者在本地托管的内容类型）以及**指定所有参与者是否在本地的附加位all_equal**定义托管相同的项目。

  包含类型T的项（成员成分）的联合类型值的紧凑表示法，每个由组（放置）G托管，分别是设置或未设置all_equal位的T @ G或{T} @G。

  For example:

  - {int32} @CLIENTS表示一个联合值，它由一组可能不同的整数组成，**每个客户端设备一个整数**。请注意，我们所讨论的是单个联合值，它包含出现在网络中多个位置的多个数据项。考虑它的一种方式是作为一种具有“网络”维度的张量，尽管这种类比并不完美，因为TFF不允许随机访问联合值的成员组成部分。
  - {<X = float32，Y = float32> *} @ CLIENTS表示联合数据集，该值由多个XY坐标序列组成，每个客户端设备一个序列。
  - <weights = float32 [10,5]，bias [5]> @ SERVER**表示服务器上的权重和偏差张量的命名元组**。由于我们删除了花括号，这表示all_equal位已设置，即只有一个元组（无论托管此值的群集中可能有多少个服务器副本）。

### Building Blocks

Federated Core的语言是lambda演算[lambda-calculus](https://en.wikipedia.org/wiki/Lambda_calculus)的一种形式，还有一些额外的元素。

它提供了当前在公共API中公开的以下编程抽象：

- TensorFlow计算（tff.tf_computation）。这些是TensorFlow代码的部分，使用tff.tf_computation装饰器包装为TFF中的可重用组件。它们总是具有函数类型，与TensorFlow中的函数不同，它们可以采用结构化参数或返回序列类型的结构化结果。

  这是一个例子，**类型（int32 *  - > int）的TF计算**，它使用tf.data.Dataset.reduce运算符来计算整数之和：

  ```python
  @tff.tf_computation(tff.SequenceType(tf.int32))
  def add_up_integeres(x):
    return x.reduce(np.int32(0), lambda x, y: x + y)
  ```

- 内在函数或联合运算符（tff.federated _...）。这是一个函数库，如**tff.federated_sum**或**tff.federated_broadcast**，它们构成了FC API的大部分，其中大部分代表了与TFF一起使用的分布式通信运算符。

  我们将这些称为内在函数，因为它们有点像内部函数，它们是TFF理解的开放式，可扩展的运算符集，并且编译成较低级别的代码。

  这些运算符中的大多数都具有联合类型的参数和结果，并且大多数是可以应用于各种数据的模板。

  例如，tff.federated_broadcast可以被认为是函数类型T @ SERVER  - > T @ CLIENTS的模板运算符。

- Lambda表达式（tff.federated_computation）。 TFF中的lambda表达式相当于Python中的lambda或def;它由参数名称和包含对此参数的引用的body（表达式）组成。

  在Python代码中，可以通过使用tff.federated_computation修饰Python函数并定义参数来创建这些代码。

  这是我们之前已经提到过的lambda表达式的一个例子：

  ```python
  @tff.federated_computation(tff.FederatedType(tf.float32, tff.CLIENTS))
  def get_average_temperature(sensor_readings):
    return tff.federated_mean(sensor_readings)
  ```

- 放置文字**Placement literals**.。目前，只有tff.SERVER和tff.CLIENTS允许定义简单的客户端 - 服务器计算。

- 函数调用**Function invocations**（`__call__`）。可以使用标准Python `__call__`语法调用任何具有函数类型的函数。调用是一个表达式，其类型与被调用函数的结果类型相同。

  For example:

  - add_up_integeres（x）表示先前在参数x上定义的TensorFlow计算的调用。这个表达式的类型是int32。

  - tff.federated_mean（sensor_readings）表示对sensor_readings的**联合平均运算符的调用**。此表达式的类型是float32 @ SERVER（假设上面示例中的上下文）。

- Forming **tuples** and **selecting** their elements 形成元组并选择它们的元素。形式为[x，y]，x [y]或x.y的Python表达式，出现在用tff.federated_computation修饰的函数体中。

