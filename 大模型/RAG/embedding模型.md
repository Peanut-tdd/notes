## 三、中文场景推荐模型



| 模型                                    | 维度 | 说明                   |
| :-------------------------------------- | ---- | ---------------------- |
| `shibing624/text2vec-base-chinese`      | 768  | 中文语义匹配首选       |
| `BAAI/bge-small-zh-v1.5`                | 512  | 检索效果好，体积小     |
| `BAAI/bge-large-zh-v1.5`                | 1024 | 精度最高，速度慢些     |
| `all-MiniLM-L6-v2`                      | 384  | 中英通用，偏英文，轻量 |
| `paraphrase-multilingual-MiniLM-L12-v2` | 384  | 跨语言                 |



**BGE-M3 embedding模型使用 **

```
pip install FlagEmbedding 
```



 BAAI 是北京智源，但它的模型托管在 HuggingFace（海外服务器），需要配置一下环境变量，不然下载不了

```
export HF_ENDPOINT=https://hf-mirror.com    
```





后续使用

```python
import os
os.environ["HF_HUB_OFFLINE"] = "1"                                                                                  
os.environ["TRANSFORMERS_OFFLINE"] = "1"                                                                            
os.environ["HF_DATASETS_OFFLINE"] = "1"   

from FlagEmbedding import BGEM3FlagModel


class BgeM3Encoder(object):
    def __init__(self,model_name='BAAI/bge-m3') -> None:
        self.model = BGEM3FlagModel(model_name_or_path=model_name,use_fb16=False,local_files_only=True)


    def encode_docs(self, texts: list[str]) :

        output= self.model.encode(texts,return_dense=True,return_sparse=True,return_colbert_vecs=False,batch_size=32)
        print(f"Output keys:{output}")
        dense=output['dense_vecs']
        sparse=[]
        for i in range(len(dense)):
            row=output["lexical_weights"][i]
            sparse.append(row)
        return dense,sparse



```











问题：语义匹配不准

1、embedding模型选的不对

2、对向量做归一化

3、用余弦相似度（不用欧式距离）