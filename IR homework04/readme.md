# 信息检索实验四
## 实验目的：

测试sklearn中的几种聚类算法在两个数据集sklearn.datasets.load_digits和sklearn.datasets.fetch_20newsgroups上的聚类效果

## 实验内容
1.库的调用： 直接从sklearn的库加载数据集。手写数字数据集包括1797个0-9的手写数字数据，每个数字由8X8大小的矩阵构成，矩阵中值的范围是0-16，代表颜色的深度，使用sklearn.datasets.load_digits即可加载数据集。使用sklearn.datasets import fetch_20newsgroups即可加载新闻数据集。
调用matplotlib做可视化，调用sklearn.cluster中的各种聚类方法

2.载入数据（以digits数据集为例）：
np.random.seed(42)
digits = load_digits()
data = scale(digits.data)
n_samples, n_features = data.shape
n_digits = len(np.unique(digits.target))
labels = digits.target
sample_size = 300

3.evaluation:
机器学习评价指标：
同质性homogeneity：每个群集只包含单个类的成员。 
完整性completeness：给定类的所有成员都分配给同一个群集。
V-measure：以上两者的调和平均
metrics.v_measure_score(labels, estimator.labels_),
metrics.homogeneity_score(labels, estimator.labels_),
metrics.completeness_score(labels, estimator.labels_)

4.调用库中的各种方法实现聚类，并给出这些方法的评价指标值：
例如：
DBSCAN： bench_k_means(DBSCAN(),name="DBSCAN()",data=data)
k-means++:  bench_k_means(KMeans(init='k-means++', n_clusters=n_digits, n_init=10),name="k-means++", data=data)
MeanShift:
bandwidth = estimate_bandwidth(data, quantile=0.1)
bench_k_means(MeanShift(bandwidth=bandwidth, bin_seeding=True),name="MeanShift",data=data)

5.PCA降维后可视化
先把数据降至二维，便于在二维坐标轴上显示：reduced_data = PCA(n_components=2).fit_transform(data)
对降到二维的数据做聚类：
kmeans = KMeans(init='k-means++', n_clusters=n_digits, n_init=10)
kmeans.fit(reduced_data)  # 对降维后的数据进行kmeans
result = kmeans.labels_
plt.figure(2)
plt.clf()
plt.scatter(reduced_data[:, 0], reduced_data[:, 1],c=result)
centroids = kmeans.cluster_centers_
plt.scatter(centroids[:, 0], centroids[:, 1],
            marker='x', s=169, linewidths=3,
            color='w', zorder=10)
plt.show()

6.可视化效果展示
![查询结果](https://github.com/479136200/IR-experiments/blob/master/images/picturemap.png)


