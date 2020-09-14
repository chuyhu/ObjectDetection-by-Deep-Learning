## FasterRCNN
### 基础步骤：
- 1、特征提取网络（Backbone）
- 2、RPN模块
- 3、ROI模块
- 4、RCNN模块

### 特征提取网络（Backbone）
- 1、首先将输入图像缩放至固定大小M * N,然后将M * N图像送入网络，最后得到map feature（特征图）
- 2、该Backbone网络 一般是VGG16，包含13个conv层+13个relu层+4个pooling层(其实就是VGG16去掉FC层)

### RPN模块（Region Proposal Networks）
![RPN模块](/home/changao/图片/RPN.jpg)

该RPN网络实际分为两条线：<br> 
上面一条是通过softmax分类anchors来获得positive和negative分类；<br>
下面一条则是用于计算对于anchors的bounding box regression偏移量，以获得精准的proposal。<br>
而最后的Proposal层则负责综合positive anchors和对应的bounding box refression偏移量获取proposal，同事剔除太小和超出边界的proposals。<br>
[一文读懂Faster RCNN](https://zhuanlan.zhihu.com/p/31426458)<br>
[精读Faster RCNN](https://zhuanlan.zhihu.com/p/82185598)
<br>
[RPN详解](https://blog.csdn.net/w437684664/article/details/104238521?utm_medium=distribute.pc_relevant.none-task-blog-blogcommendfrommachinelearnpai2-3.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-blogcommendfrommachinelearnpai2-3.nonecase)

### RoI模块
![RoI模块](/home/changao/图片/RoI Pooling.jpg)
RoI Pooling层则负责收集proposal，并计算proposal feature maps，送入后续网络。<br>
改层有两个输入：
- 1、原始的feature maps
- 2、RPN输出的proposal boxes

### RCNN模块
![RCNN模块](/home/changao/图片/Classification.jpg)<br>
从RoI Pooling获取到7 * 7 大小的proposal feature maps后，送入后续的网络，后面的网络就是两个FC层
- 1、通过全连接和softmax对proposals进行分类，这是识别
- 2、再次对proposals进行bounding box regression，获取更高进度的rect box

### Faster RCNN训练
其训练实际上就是在已经训练好的model（如VGG,ZF）的基础上继续进行训练，实际步骤：
- 1、在已经训练好的model上，训练RPN网络,stage_1_rpn_train.pt
- 2、利用训练好的RPN网络，收集proposals,rpn_test.pt
- 3、第一次训练Faster RCNN网络,stage1_fast_rcnn_train.pt
- 4、第二次训练RPN网络,stage_2_rpn_train.pt
- 5、再次利用步骤4中训练好的RPN网络，收集proposals，对应rpn_test.pt
- 6、第二次训练Faster RCNN，对应stage2_fast_rcnn_train.pt
一般只循环两次，之后的再循环没有提升

*** 训练过程流程图 ***<br>
![训练过程](/home/changao/图片/train.jpg)