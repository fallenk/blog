---
title: TFFIntroVideo
date: 2019-05-23 11:14:42
tags: [联合学习]
category: [TensorFlow]
---

## TensorFlow Federated (TFF): Machine Learning on Decentralized Data (TF Dev Summit ‘19)

[video url](https://www.youtube.com/watch?v=1YbPmkChcbo)

<!--more-->

# Intro

TensorFlow Federated (TFF) is an open-source framework for **machine learning and other computations on decentralized data**. TFF has been developed to **facilitate open research and experimentation with Federated Learning (FL)**, an approach to machine learning where a shared global model is trained across many participating clients that keep their training data locally.

![](./TFFIntroVideo/1.png)

1. server distribute initial model to clients;
2. each client train model on local own data[不用convergence收敛，训练一些会儿]
   1. each client produce new model trained and send it to server
3. **In practice, we send updates but not models**, implementation detail

![](./TFFIntroVideo/2.png)

6. in server, combined model

![](./TFFIntroVideo/3.png)

7. train with many many rounds

![](./TFFIntroVideo/4.png)

8. many extensions to add

![](./TFFIntroVideo/5.png)

9. TFF将代码编译成抽象表示 可运行在不同环境  伪代码，简介

![](./TFFIntroVideo/7.png)

10. 优点：同一书写界面

![image-20190523172537172](./TFFIntroVideo/8.png)

11. FL API训练模型，实现联合学习； FC API实现联合学习算法 ，本地环境模拟

![image-20190523213914134](./TFFIntroVideo/9.png)

12. 分层，从各个方面参与TFF的学习

![image-20190523214206108](./TFFIntroVideo/10.png)

13. FL API 训练流程如上； FC API内容如下

    ![image-20190523214601333](./TFFIntroVideo/11.png)

14. FC API实现优点: 1.为分布式计算的语言 2. 类Python的API 3. 部署时的方便

    ![image-20190523214958832](./TFFIntroVideo/12.png)

15. 在clients端的数据为一等公民，有类型{float32}@CLIENTS

![image-20190523215159579](./TFFIntroVideo/13.png)

16. 在server端融合模型，数据类型是float32@SERVER

![image-20190523215540970](./TFFIntroVideo/14.png)

17. 将联邦操作认为是协议

![image-20190523215710473](./TFFIntroVideo/15.png)

18. FC API时，设置 数据类型； 定义联合学习算法内容

![image-20190523220256610](./TFFIntroVideo/16.png)

19. 较为复杂的例子：两个输入，检测数据，门槛值；如何执行？以下

![image-20190523220433374](./TFFIntroVideo/17.png)

20. 第一步： 分发threshold值给每一个客户端

![image-20190523220606238](./TFFIntroVideo/18.png)

21. 使用类似于MapReduce的方式计算以上

    ![image-20190523220917545](./TFFIntroVideo/19.png)

22. 使用 tff.federated_average聚合；此为一个完整的例子

![image-20190523221255244](/Users/liulifeng/Workspaces/hexo/source/_posts/TFFIntroVideo/20.png)

23. 代码方面展示，如何创建底层的联合学习的算法； 表达联合学习的算法

![image-20190523221506366](./TFFIntroVideo/21.png)

24. 未来需要工作方向； 联合学习模型和数据集的增加；发展新的联合学习算法；改进FC API底层；部署环境增加

