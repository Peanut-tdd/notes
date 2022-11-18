# protobuf了解

Protocol Buffers 与平台无关, 与语言无关, 实现数据序列化的一种手段。提供了多种语言的实现：java、c#、c++、go 和 python，每一种实现都包含了相应语言的[编译器](https://so.csdn.net/so/search?q=编译器&spm=1001.2101.3001.7020)以及库文件。由于它是一种二进制的格式，比使用 xml 进行数据交换快许多。





最终对这些个人思考做一些小小的总结：

1. XML、JSON、ProtoBuf 都具有**数据结构化**和**数据序列化**的能力
2. XML、JSON 更注重**数据结构化**，关注人类可读性和语义表达能力。ProtoBuf 更注重**数据序列化**，关注效率、空间、速度，人类可读性差，语义表达能力不足（为保证极致的效率，会舍弃一部分元信息）
3. ProtoBuf 的应用场景更为明确，XML、JSON 的应用场景更为丰富。





[protobuf中文文档](https://github.com/lixiangyun/protobuf_doc_ZH_CN)

[参考链接](https://blog.csdn.net/xia15000506007/article/details/127236468)

