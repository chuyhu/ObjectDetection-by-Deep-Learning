# mask

- ML: K-means 聚类算法
左图是kmeans的具体算法，用来生成合适的先验框，
实际上，聚类的目的是先验框和真实框有更大的IOU值，IOU指的是先验框和真实框之间的交并比
最理想的情况下是IOU为1时，即先验框和真实框完全重叠，此时两者之间距离最小，聚类效果最好
k-means拿到数据里所有的目标框，得到所有的宽和高，在这里面随机取得9个随机中心，之后以9个点为中心得到9个族，不断计算其他点到中点的距离调整每个点所归属的族和中心，直到9个中心不再变即可。这9个中心的x，y就是整个数据的9个合适的anchors框的宽和高。


- DL: YOLO-V3