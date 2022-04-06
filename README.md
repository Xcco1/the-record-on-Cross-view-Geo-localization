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



