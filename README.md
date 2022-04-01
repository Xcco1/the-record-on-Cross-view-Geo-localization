# the-record-on-Cross-view-Geo-localization
Origin LPN:
recall1:75.93 AP:79.14
week1：
在resnet50的layer3后加入一个局部分支，由于feature map是16*16采用5x5dwconv作为局部注意力
recall1:76.2 AP:79.41
用7x7卷积正好可以包含相邻的part：
recall1:76.15 AP:79.41

