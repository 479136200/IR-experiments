# 信息检索实验二
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
目标是给定查询，从文档集合中返回得分最高的k篇文档。根据向量的相似度来选择得分最高的k篇文档。使用的权重计算机制是lnc.ltc,查询向量采用对数tf计算方法和idf因子，文档向量采用对数tf计算方法，不采用idf因子，因为文档较长，查询语句较短，对文档采用idf会降低效率。
首先对query中的每个term计算出它在query中的权重（用tf和idf的乘积表示）Wtq=(1+log(tf))*log(N/df),然后对postinglists里的每篇文档更新得分score+=wtq*wtd,其中wtd=(1+log(tf))/cos,(对于文档采用余弦归一化）
#### 实现
   这次实验是在上次的基础上改进，所以和上次实验一样，给的文档里的数据有userName、clusterNo、text、timeStr、tweetId、errorCode、textCleaned、relevance多个属性，对每一行数据也就是每一条tweets需要从中提取userName、tweetId、text这三个有用的属性。先对tweets和query做预处理：把每个句子分割成单词，大小写统一，还原动词的形式及单复数，去掉无用属性。然后把每个term作为key,以tweetid列表作为value来构建倒排索引。
   
   对推文的处理
   ` result = [] 
    for word in terms:
        expected_str = Word(word)
        expected_str = expected_str.lemmatize("v")
        if expected_str not in usefulTerm:     #推文的处理还要去掉无用项中的word
            result.append(expected_str)
    return result `
    
  对query的处理
    `    result = []
    for word in terms:
        expected_str = Word(word)
        expected_str = expected_str.lemmatize("v")
        result.append(expected_str)
    return result `

然后构建postings,由tweetid,tf_wght_doc对组成。tf_wght_doc=1+log（tf）
在构建postings的同时完成余弦归一化的cos值的计算
 `for te in line:
            tf_raw_doc = line.count(te)  # 记录tf_raw (document)
            tf_wght_doc = 1+math.log(tf_raw_doc,10)
            if te in postings.keys():
                postings[te].append([tweetid,tf_wght_doc])
                document_frequency[te]=document_frequency[te]+1#文档的idf
                cosine[tweetid]=cosine[tweetid]+pow(tf_wght_doc,2) #文档的词频平方和
            else:
                postings[te] = [[tweetid,tf_wght_doc]]
                document_frequency[te] = 1
                cosine[tweetid] = cosine[tweetid]+tf_wght_doc*tf_wght_doc`

计算得分
query的tf:  tf_wght_query = 1 + math.log(query.count(term), 10)
documents的tf:  tf_wght_doc = 1+math.log(tf_raw_doc,10)
score+= tf_wght_query * document_frequency[term] * tf_wght_doc / 余弦值

       for te in postings[term]:
            tweetid = te[0]
            score[tweetid] += tf_wght_query * document_frequency[term] * te[1] / math.sqrt(cosine[te[0]])

![查询结果](https://github.com/479136200/IR-experiments/blob/master/images/6_I%40I%605%7DW%5BJ1V35FAS9)ZDG.png)


### 实验体会
本次实验通过lnc.ltc机制实现了对文档权重的得分的计算，在posting list中存储term在每个doc中的TF with pairs (docID, tf)我认为实验需要注意的地方是计算文档、query的df、tf、归一化用到的cos值是每一篇doc的权重的平方和再开平方。

