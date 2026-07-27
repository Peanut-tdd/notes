## 三、中文场景推荐模型



| 模型                                    | 维度 | 说明                   |
| :-------------------------------------- | ---- | ---------------------- |
| `shibing624/text2vec-base-chinese`      | 768  | 中文语义匹配首选       |
| `BAAI/bge-small-zh-v1.5`                | 512  | 检索效果好，体积小     |
| `BAAI/bge-large-zh-v1.5`                | 1024 | 精度最高，速度慢些     |
| `all-MiniLM-L6-v2`                      | 384  | 中英通用，偏英文，轻量 |
| `paraphrase-multilingual-MiniLM-L12-v2` | 384  | 跨语言                 |



```
embeddings = model.encode(
    sentences,
    batch_size=64,			//每批处理多少个句子,batch_size 越大越快，但显存占用越高，GPU OOM
    normalize_embeddings=True,	//把向量 L2归一化（模长=1）,归一化后，余弦相似度 = 点积，算相似度更快
    convert_to_numpy=True,
    show_progress_bar=True,
    device="cuda"  # 有GPU就用
)
```



问题：语义匹配不准

1、embedding模型选的不对

2、对向量做归一化

3、用余弦相似度（不用欧式距离）