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
