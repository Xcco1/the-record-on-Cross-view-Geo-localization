# the-record-on-Cross-view-Geo-localization


week 1:
base on the LPN,modify the the outsize feature use Top2 maxpooling,and concat the top2 feature of the outsize,and the input dim of the outsize feature is 4096(2048*2)
recall1:37.8 AP:43.12

which means the outsize feature is important for this task.

week 2:
考虑到不同part的特征之前应该存在相互联系而不是直接割舍来看，由于每个part的宽度是2，用5x5dwconv正好可以包含到相邻part,block采用沙漏结构。


the idea record:
use decentralize attention to replace the structure of LPN.
