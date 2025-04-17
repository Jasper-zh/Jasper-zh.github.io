---
title: LlamaIndex对于向量库的持久化与加载
date: 2025-03-19 17:56:47
tags: LLM
categories: 
- LLM
---



## 前言

之前使用LlamaIndex做RAG环节的文档加载和检索，使用Faiss作为向量库。知识库构建进行持久化存储，再次加载进行检索时出现维度不匹配的情况。

```shell
  query_result = self._vector_store.query(query, **self._kwargs)
                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\MySpace\dev\python\3.11\Lib\site-packages\llama_index\vector_stores\faiss\base.py", line 195, in query
    dists, indices = self._faiss_index.search(
                     ^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\MySpace\dev\python\3.11\Lib\site-packages\faiss\class_wrappers.py", line 329, in replacement_search
    assert d == self.d
           ^^^^^^^^^^^
```

这个是由于LlamaIndex进行VectorStoreIndex持久化时，里面的词嵌入模型是不会保存的，

当从持久化文件进行加载时，仍然需要显示的传入词嵌入模型，否则会采用默认的编码方式，会和之前存入的向量维度不一样



## 构建索引&持久化

```python
# 初始化 FAISS 向量存储
# 由于我这里使用BGE-M3作为词嵌入模型，所以维度设置为VECTOR_DIMENSION = 1024
faiss_index = IndexFlatL2(VECTOR_DIMENSION)  
faiss_vector_store = FaissVectorStore(faiss_index=faiss_index)


# 创建存储上下文 (构建索引时不要配置目录)
storage_context = StorageContext.from_defaults(vector_store=faiss_vector_store)


# 创建索引
index = VectorStoreIndex.from_documents(
    docs,
    storage_context=storage_context,
    embed_model=embed_model,
    show_progress=True
)


# 持久化索引(通过语法也可以看到，并不包含embed_model，只有storage_context)
index.storage_context.persist(persist_dir=str(VECTOR_STORE_PATH01))
```



## 本地存储加载索引

```python
# 从本地存储加载索引...  （如果是特定的向量库，可能需要先用对应的库去加载向量存储）
faiss_vector_store = FaissVectorStore.from_persist_dir(str(VECTOR_STORE_PATH01))
storage_context = StorageContext.from_defaults(vector_store=faiss_vector_store, persist_dir=str(VECTOR_STORE_PATH01))


# 加载index 除了本地的storage_context 还要传入 embed_model (否则检索时不知道用什么对query编码)
index = load_index_from_storage(storage_context=storage_context,embed_model=embed_model)

```

