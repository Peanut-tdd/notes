# 提交github报错问题汇总

## 1、Git提示Connection was reset, errno 10054解决方法

**通过:http.sslVerify false全局来设置**

```
git config --global http.sslVerify "false"
```





## 2、解决git 中Failed to connect to github.com port 443: Timed out

原因：是因为设置了代理

解决：取消全局代理



```
取消全局代理：
git config --global --unset http.proxy
 
git config --global --unset https.proxy
```

