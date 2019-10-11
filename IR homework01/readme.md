# 信息检索实验一
## 201700150214 王玲雅

### 实验目的
* 在 tweets 数据集上构建 inverted index; 
* 实现 Boolean Retrieval Model，支持 and, or ,not，使用 TREC 2014 test topics 进行测试
### 实验过程
#### 预处理和倒排索引
   给的文档里的数据有userName、clusterNo、text、timeStr、tweetId、errorCode、textCleaned、relevance多个属性，对每一行数据也就是每一条tweets需要从中提取userName、tweetId、text这三个有用的属性。在建立倒排索引前先对tweets数据做预处理：把每一条推文的句子分割成单词，大小写统一，还原动词的形式及单复数，去掉无用属性。然后把每个term作为key,以tweetid列表作为value来构建倒排索引。
   
   注意，对query也要做同样的预处理
   
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
query的长度为3：A and B、A or B、A not B
query的长度为5：A and B and C、A or B or C、(A and B) or C、(A or B) and C 
例如，A and B的查询是对A和B同时遍历对应的索引，只有A，B在同一个索引里才把tweetid加到返回的结果中
`answer = []
    if (term1 not in postings) or (term2 not in postings):
        return answer
    else:
        i = len(postings[term1])
        j = len(postings[term2])
        x = 0
        y = 0
        while x < i and y < j:
            if postings[term1][x] == postings[term2][y]:
                answer.append(postings[term1][x])
                x += 1
                y += 1
            elif postings[term1][x] < postings[term2][y]:
                x += 1
            else:
                y += 1
        return answer `

A or B的查询是A在B不在则返回所有A的索引，B在A不在则返回所有B的索引，AB都在的情况下先把A的索引作为answer，遍历B的所有索引如果有的不在answer中就加进来。
`def merge2_or(term1, term2):
    answer = []
    if (term1 not in postings) and (term2 not in postings):
        answer = []
    elif term2 not in postings:
        answer = postings[term1]
    elif term1 not in postings:
        answer = postings[term2]
    else:
        answer = postings[term1]
        for item in postings[term2]:
            if item not in answer:
                answer.append(item)
    return answer`

![处理前的初始推文格式](https://github.com/479136200/IR-experiments/blob/master/images/L%60HCF2RHTR_QO%40%60HG56AM%7D5.png)

![处理后的推文格式](https://github.com/479136200/IR-experiments/blob/master/images/picture3.png)

![query长度为3的查询结果](https://github.com/479136200/IR-experiments/blob/master/images/picture1.png)

![query长度为5的查询结果](https://github.com/479136200/IR-experiments/blob/master/images/picture2.png)

### 实验体会
本次实验通过倒排索引的模型实现了简单的布尔查询和嵌套的布尔查询，我认为实验需要注意的地方是对推文和查询语句做同样的预处理，以及实现布尔查询的函数，需要理清逻辑。

