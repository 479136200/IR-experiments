# 信息检索实验三
## 201700150214 王玲雅

### 实验目的
实现以下指标评价，并对HW1.2检索结果进行评价
• Mean Average Precision (MAP) 
• Mean Reciprocal Rank (MRR) 
• Normalized Discounted Cumulative Gain (NDCG)

### 原理
Precision 和Recall：

P：对于一个查询，返回了一系列的results，正确率指的是返回的结果中相关的文档占的比例，定义为：precision=返回结果中相关文档的数目/返回结果的数目

R：召回率则是返回结果中相关文档占所有相关文档的比例，定义为：Recall=返回结果中相关文档的数目/所有相关文档的数目


#### MAP：
平均精度均值。

AP：正确率只是考虑了返回结果中相关文档的个数，没有考虑文档之间的序。对一个搜索引擎或推荐系统而言返回的结果必然是有序的，而且越相关的文档排的越靠前越好，于是有了AP的概念。对一个有序的列表，计算AP的时候要先求出每个位置上的precision，然后对所有的位置的precision再做个average。如果该位置的文档是不相关的则该位置 precision=0.即：若该位置返回的结果相关，计算该位置的正确率，若不相关，正确率置为0。相关文档排序的位置越靠前，检出的相关文档越多，AP值越大。

MAP：对所有查询的AP值求平均

例如，假设有两个查询，查询1有4个相关文档，查询2有5个相关文档。某系统对查询1检索出4个相关文档，其rank分别为1,2,4,7；对于查询2检索出3个相关文档，其rank分别为1,3,5。

对于查询1，平均正确率AP为:(1/1+2/2+3/4+4/7)/4=0.83

对于查询2，平均正确率AP为:(1/1+2/3+3/5)/5=0.45

则平均正确率均值MAP为:(0.83+0.45)/2=0.64


#### MRR：
倒数排名均值。
返回的结果集的优劣，跟第一个正确答案的位置有关，第一个正确答案越靠前，结果越好。

RR：（reciprocal rank）倒数排名，指检索结果中第一个相关文档的排名的倒数。对于一个query，若第一个正确答案排在第n位，则RR得分就是1/n。

MRR：对所有查询的RR值求平均

例如，假设测试集有4个query，他们的结果中，前三个query的第一个正确答案分别被排在第3，1，5位，而第四个query没有找到正确答案。
则MRR=1/4(1/3+1/1+1/5+0)=0.383


#### NDCG:
在MAP中，结果文档和query要么相关，要么不相关，也就是相关度非0即1。NDCG中，相关度分成从0到r的r+1的等级(r可设定)。

CG：当假设用户的选择与排序结果无关(即每一级都等概率被选中)，则生成的累计增益值

DCG：考虑到一般情况下用户会优先点选排在前面的搜索结果，所以应该引入一个折算因子(discounting factor): log(2)/log(1+rank)。这时将获得DCG值(Discounted Cumulative Gain)

NDCG：为了使不同等级上的搜索结果的得分值容易比较，需要将DCG值归一化得到NDCG值

MaxDCG是理想返回结果List的DCG值

DCG/MaxDCG就得到NDCG值

### 实现

MAP：先计算P_result:序号/排名，然后对于找到相关结果的查询计算AP值，对没有找到true value的查询把AP值置为0，计算所有查询的AP值的均值即为MAP

        for doc_id in test_result[0: length_use]:
            i += 1
            if doc_id in true_list:
                i_retrieval_true += 1
                P_result.append(i_retrieval_true / i)

        if P_result:
            AP = np.sum(P_result) / len(true_list)
            print('query:', query, ',AP:', AP)
            AP_result.append(AP)
        else:
            print('query:', query, ' not found a true value')
            AP_result.append(0)
    return np.mean(AP_result)
![查询结果](https://github.com/479136200/IR-experiments/blob/master/images/D47U~JN%7BCO3ZT9I%24%5BFG2SD7.png)

MRR：找第一个相关文档的排名的倒数，若第一个正确答案排在第n位，则RR得分就是1/n，然后对所有查询的RR值求平均就是MRR

        for doc_id in test_result[0: length_use]:
            i += 1
            if doc_id in true_list:
                i_retrieval_true = 1
                P_result.append(i_retrieval_true / i)
                break

        if P_result:
            RR = np.sum(P_result)/1.0
            print('query:', query, ',RR:', RR)
            RR_result.append(RR)
        else:
            print('query:', query, ' not found a true value')
            RR_result.append(0)
    return np.mean(RR_result)


NDCG：

        length_use = min(k, len(test_result), len(true_list))
        if length_use <= 0:
            print('query ', query, ' not found test list')
            return []
        for doc_id in test_result[0: length_use]:
            i += 1
            rel = qrels_dict[query].get(doc_id, 0)
            DCG += (pow(2, rel) - 1) / math.log(i, 2)
            IDCG += (pow(2, true_list[i - 2]) - 1) / math.log(i, 2)
        NDCG = DCG / IDCG
        print('query', query, ', NDCG: ', NDCG)
        NDCG_result.append(NDCG)
    return np.mean(NDCG_result)
