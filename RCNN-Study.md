## RCNN（Region-based Convolutional Neural Networks）
### basic steps
- 1:一张图片生成1k-2k个候选区域
- 2：对于每个候选区域，使用深度网络提取其特征
- 3：特征送入每一类的SVM分类器，判断是否输入该类
- 4：使用回归器精修候选框位置


### 一、候选区域生成
使用Selective Search方法从图片中生成2k-3k个候选区域
基本思路：

- 1：使用一种过分分割的手段，将图像分割成小区域
- 2：查看小的区域，合并可能性最高的两个区域，重复至整张图像合并到一个区域位置
- 3：输出所有曾经存在过的区域，即候选区域


合并规则(四个区域)：

* 颜色（颜色直方图）相近
* 纹理（纹理直方图）相近
* 合并总面积小的
* 合并后，总面积在其BBOX所占比例大的    

### 二、特征提取
#### 1、预处理数据
在使用深度网络提取特征之前，首先把候选区域归一化成同一尺寸为227*227

*** 
怎么得到227*227尺寸的图像： 
 
    （1）各向异性缩放：不管长宽比例，直接进行缩放

    （2）各向同性缩放：先扩充后裁剪或先裁剪后扩充

***

#### 2、网络结构
网络结构有两个可选方案：
- 1：经典的Alexnet
- 2：VGG16（卷积核较小，步长较小）
#### 3、训练数据、参数调优
* 1、使用迁移学习进行初始化参数，使用识别库进行预训练
* 2、使用检测库进行参数调优
* 3、在检测库中进行评测

### 三、类别判断
将网络训练的特征向量使用一个线性SVM二分类器进行判别
该分类器的输入为深度网络输出的4096维特征，输出是否属于此类

### 四、位置精修
回归器对每一类目标都是用一个线性脊回归器来进行精修
