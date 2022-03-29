# the-record-on-Cross-view-Geo-localization


week 1:
基于LPN，将最外层特征的pooling方式从avg pooling改为Top2 maxpooling，然后将其分为2份拼接在一起，同时分类器的维度也变为4096
recall1:37.8 AP:43.12
说明最外层的，并不是都能找到2个可参考的特征，同时反而导致分类器的难以训练？

week 2:
考虑到不同part的特征之前应该存在相互联系而不是直接割舍来看，由于每个part的宽度是2，用5x5dwconv正好可以包含到相邻part,block采用沙漏结构。


the idea record:
use decentralize attention to replace the structure of LPN.
