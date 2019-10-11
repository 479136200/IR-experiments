# 信息检索实验一
## 201700150214 王玲雅

### 实验目的
* 在 tweets 数据集上构建 inverted index; 
* 实现 Boolean Retrieval Model，支持 and, or ,not，使用 TREC 2014 test topics 进行测试
### 实验过程
#### 数据预处理
   给的文档里的数据有userName、clusterNo、text、timeStr、tweetId、errorCode、textCleaned、relevance多个属性，对每一行数据也就是每一条tweets需要从中提取userName、tweetId、text这三个有用的属性。在建立倒排索引前先对tweets数据做预处理：把每一条推文的句子分割成单词，大小写统一，还原动词的形式及单复数，去掉无用属性。然后把每个term作为key,以tweetid列表作为value来构建倒排索引。
