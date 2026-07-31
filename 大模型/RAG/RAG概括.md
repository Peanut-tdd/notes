RAG概念

```
Retrieval:检索
Augmented:增强
Generation:生成
```



数据准备阶段（业务线）

分片和索引

```
过程：数据清洗（去除html标签,页眉页脚，乱码等噪音）-->数据分片chunking--> Embedding模型-->向量-->向量数据库（indexing）
```



回答生成阶段（用户线）

召回+合成

```
过程：用户提问-->Embedding模型-->向量-->计算相似度（余弦/欧式距离）-->查询向量数据库获得context-->用户问题+context组合成prompt提示词-->LLM大模型-->->rerank重排-->用户
```





痛点一：文档解析

解决方案：专业版面分析模型+OCR+人工复合反馈（RLHF）



痛点二：颗粒度（分片策略）

切太大=噪音大；切太小=语义丢失，需根据业务场景大量测试分片策略



痛点三：检索准确率

解决方案：混合检索+重排模型（Rerank）



痛点四：用户提问的模糊性

解决方案：大模型结合用户的上下文执行查询重写（query rewrite）



![image-20260723162720398](/Users/tdd/Library/Application Support/typora-user-images/image-20260723162720398.png)





数据清洗

分片策略

多路召回

工程优化

