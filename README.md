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
1.每层之间的特征不一定能配上对

![image](https://user-images.githubusercontent.com/61531491/161378292-5d0458d8-7024-4571-94e3-a1e3b3f2be33.png)

2.采用avgpooling 能够解决角度旋转的问题
