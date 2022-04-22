# the-record-on-Cross-view-Geo-localization
Origin LPN:
recall1:75.93 AP:79.14

week1：

在resnet50的layer3后加入一个局部分支，由于feature map是16*16采用5x5dwconv作为局部注意力
recall1:76.2 AP:79.41

用7x7卷积正好可以包含相邻的part：
recall1:76.15 AP:79.41

input:
![image](https://user-images.githubusercontent.com/61531491/161293648-557f64a3-f144-4ffc-9403-a1f0282014c8.png)

ours：
![image](https://user-images.githubusercontent.com/61531491/161362630-f1c72463-1144-4e4f-99c9-febfdbcd95e7.png)


LPN:
![image](https://user-images.githubusercontent.com/61531491/161293484-d2f78512-34e3-46b3-b189-9acbf05c9be9.png)

在layer3和layer都加入这种局部注意力同时接入残差
recall1：75.95 AP：79.22

没有残差recall1：75.15 AP：78.34

LPN：
1.每层之间的特征不一定能配上对，最外层的特征并没带来很多涨点

![image](https://user-images.githubusercontent.com/61531491/161387638-2a735c7c-3be7-4809-b371-cdbe1e8d8e4a.png)

![image](https://user-images.githubusercontent.com/61531491/161378292-5d0458d8-7024-4571-94e3-a1e3b3f2be33.png)

联想到之前一篇reid的工作中，动态规划匹配的方法

![image](https://user-images.githubusercontent.com/61531491/161389049-6b5ed36f-30a7-4772-8523-8be3f12cef98.png)

以及一种基于PCB的软划分方法

![image](https://user-images.githubusercontent.com/61531491/161389226-153cef4c-436c-4716-b70f-e1b56fb37785.png)

采用这种软划分方法：

训练其中的part classifier 60个epoch，然后在完整训练50个epoch
recall1：56.5 AP：61.88

仔细检验发现，在代码中只是把2048个通道分为4部分，而不是把区域分为4部分，那是不是可以从LPN的4part入手，学习shuffle各个part直接哪些feature来做refine

2.从植被入手，尽量让注意力不要在植被上

week2：
在resnet50的featuremap上，统计每个像素位置的weight，得到一张16x16的heatmap，在用conv去得到4个channel，然后在4个channel上再做avgpool
![image](https://user-images.githubusercontent.com/61531491/161483697-c0fb0943-b7df-4522-828b-aacbc5e55da8.png)

用LPN的预训练，recall1：58.02 AP：62.96
重头训练，recall1：48.22 AP：53.33

对四通道采用paration loss: recall1:63.55 AP:67.97

![image](https://user-images.githubusercontent.com/61531491/161669126-b275de95-4022-4e36-9b99-a2432f8ba3cf.png)
![image](https://user-images.githubusercontent.com/61531491/161895746-c8b158d4-8323-47a0-b0e3-7ae222414fec.png)

paration loss只对方差进行了监督，同时因为log在零点的梯度大远反而小，将其改为：

![image](https://user-images.githubusercontent.com/61531491/161888746-8e4a28af-ba36-4666-99ee-ec1834b8473f.png)

recall：71.38 AP:75.29
![image](https://user-images.githubusercontent.com/61531491/161889517-f5f2fca3-109c-4dab-b764-51f2b785c31e.png)

week3：
将loss改为0.5次方：
recall1：68.8 AP：72.92


![image](https://user-images.githubusercontent.com/61531491/162189504-e218867e-4a79-4b4f-aa8d-8ed030dd4f74.png)

recall1：69.09 AP：72.2
修改rpp结构从2层卷积换到3层和4层均掉点严重。

将pixel summary的操作换为1x1conv,recall1:69.07 AP:73.17

将avgpool换成maxpool，recall1:70.54 AP:74.17


在rpp中加入non local block,在两层卷积之后，recall1：70.11 AP：74.10
4个part可视化
![image](https://user-images.githubusercontent.com/61531491/162727553-8179c6d9-d9fe-4fda-8714-70802981c4cf.png)

生成了LPN的4张partial mask,将rpp生成的4张heatmap与其做smoothL1loss+paration loss，recall1：61.75，AP：66.6

4个part可视化：
![image](https://user-images.githubusercontent.com/61531491/162949111-2c06420e-4e9d-43cb-9f6c-42e7ea62d8cd.png)

在rpp中多加一层conv3x3和conv1x1,增加heatmap生成的效果,recall1:62.10 AP:66.62
4个part可视化：
![image](https://user-images.githubusercontent.com/61531491/163002878-63943f6c-d941-48de-936b-c7eaaa82537f.png)

还是觉得是从pixel summary这边，信息量丢失了太多了可能，从resnet的输出开始，用了3层卷积将2048逐步压缩到4维，采用smoothL1loss+paration loss，recall1：64.69 AP:69.04

![image](https://user-images.githubusercontent.com/61531491/163090347-628d767b-3eb7-4187-b4ea-ce59121d38fd.png)


采用3个CBAM注意力模块去生成3张mask，效果很烂。

week4:

只分2块，一块为中心一块为周边，在layer2-4采用Unified Attention Fusion Module（mean和max在part1和part2中分开计算），然后将part1和part2特征contact在一起在输出加入bnn，
center loss权重越大，越关注在中心，lamda=0.001，recall1：73.29 AP：76.74,lamda=0.0005,recall1:70.74 AP:74.37
