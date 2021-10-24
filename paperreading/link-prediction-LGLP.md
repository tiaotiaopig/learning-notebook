# LGLP

## intro

Pytorch implementation of [Line Graph Neural Networks for Link Prediction](https://arxiv.org/pdf/2010.10046.pdf).

[github地址](https://github.com/LeiCaiwsu/LGLP)

## 主要方法

当前使用深度学习的方法进行网络的链路预测，通常是从以两个目标节点为中心的封闭子图学习特征，然后利用这个特征用于边的二分类，有/无。

作者认为这种方法的缺点是：为了做分类，需要学习固定尺寸的特征向量，会导致信息丢失。而作者则借助图理论中的线图（line graph）这个工具，将原始图转化为线图，线图中的每个节点代表了原图的一条边，这种转化是无损的，可以相互转化，原图的边分类就转化为线图的点分类任务。另外图神经网络可以很好的学习节点的特征，而边的特征可能就较差，经过转化，就可以充分利用图神经网络，提高性能

