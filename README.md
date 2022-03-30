# the-record-on-Cross-view-Geo-localization


week 1:
基于LPN，将最外层特征的pooling方式从avg pooling改为Top2 maxpooling，然后将其分为2份拼接在一起，同时分类器的维度也变为4096
recall1:37.8 AP:43.12
说明最外层的，并不是都能找到2个可参考的特征，同时反而导致分类器的难以训练？

week 2:
考虑到不同part的特征之前应该存在相互联系而不是直接割舍来看，由于每个part的宽度是2，用5x5dwconv正好可以包含到相邻part,block采用沙漏结构。
在layer3和layer4的地方都加入局部block
Recall：24.96 AP：31.3
只在layer3加入，然后用layer4精炼,最后的激活函数Relu：recall1：31.67 AP：37.68
sigmoid:recall1:56.06 AP:60.77
去除了沙漏结构，改为5x5conv加1x1conv：recall1：54.92 AP:69.74

the idea record:
use decentralize attention to replace the structure of LPN.
