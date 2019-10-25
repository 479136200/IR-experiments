# 信息检索实验一
## 201700150214 王玲雅

### 实验目的
在Homework1.1的基础上实现最基本的Ranked retrieval model

• Input：a query (like Ron Weasley birthday)

• Output: Return the top K (e.g., K = 10) relevant tweets.
• Use SMART notation: lnc.ltc
• Document: logarithmic tf (l as first character), no idf and cosine 
normalization
• Query: logarithmic tf (l in leftmost column), idf (t in second column), no 
normalization
• 改进Inverted index
• 在Dictionary中存储每个term的DF
• 在posting list中存储term在每个doc中的TF with pairs (docID, tf) 
### 实验过程
#### 原理
tf:词项频率
idf:逆文档频率
目标是给定查询，从文档集合中返回得分最高的k篇文档。根据向量的相似度来选择得分最高的k篇文档。首先对query中的每个term计算出它在query中的权重（用tf和idf的乘积表示）Wtq=(1+log(tf))*log(N/df)
#### 实现
   这次实验是在上次的基础上改进，所以和上次实验一样，给的文档里的数据有userName、clusterNo、text、timeStr、tweetId、errorCode、textCleaned、relevance多个属性，对每一行数据也就是每一条tweets需要从中提取userName、tweetId、text这三个有用的属性。先对tweets和query做预处理：把每个句子分割成单词，大小写统一，还原动词的形式及单复数，去掉无用属性。然后把每个term作为key,以tweetid列表作为value来构建倒排索引。
   
   对推文的处理
   ` result = [] 
    for word in terms:
        expected_str = Word(word)
        expected_str = expected_str.lemmatize("v")
        if expected_str not in usefulTerm:     #去掉无用项
            result.append(expected_str)
    return result `
    
  对query的处理
    `    result = []
    for word in terms:
        expected_str = Word(word)
        expected_str = expected_str.lemmatize("v")
        result.append(expected_str)
    return result `
推文的处理还要去掉无用项中的word

#### 布尔查询


![处理前的初始推文格式](https://github.com/479136200/IR-experiments/blob/master/images/L%60HCF2RHTR_QO%40%60HG56AM%7D5.png)

![处理后的推文格式](https://github.com/479136200/IR-experiments/blob/master/images/picture3.png)

![query长度为3的查询结果](https://github.com/479136200/IR-experiments/blob/master/images/picture1.png)

![query长度为5的查询结果](https://github.com/479136200/IR-experiments/blob/master/images/picture2.png)

### 实验体会
本次实验通过倒排索引的模型实现了简单的布尔查询和嵌套的布尔查询，我认为实验需要注意的地方是对推文和查询语句做同样的预处理，以及实现布尔查询的函数，需要理清逻辑。

