---
title: Langchain接入LlamaIndex Retriever
date: 2025-03-26 17:56:47
tags: LLM
categories: 
- LLM
---



## 前言

Langchain和LlamaIndex都是在 构建LLM应用当中最常用的两个库，但是他们的侧重点不一样：

* Langchain侧重于构建更复杂的LLM应用流程，因为足够自定义，帮你解决了围绕大模型应用开发的基础的脏活。

* LlamaIndex是可以更快速的基于文档进行问答，封装度更高，侧重点在于快速集成数据和大模型，可以快速构建一个RAG系统。

为什么要采用Langchain和LlamaIndex相结合的方式，因为Langchain更偏基础层可以很方便扩展或者对接其他服务，因此整个大模型的编排交互依然由Langchain进行，但是LlamaIndex擅长什么？没错，就是文档的加载和检索，除了官方提供的一些基础加载库或者检索器，背后还有一个LlamaHub的社区库。提供了更多的解析器或者检索器，可以方便接入LlamaIndex使用。



那么现在思路很明确，整个大模型应用的链路流程由Langchain构建，包括提示词、包括对大模型进行交互、包括后续各种各样的功能，而链路中如果有需要查找知识库（或者构建知识库），由LlamaIndex进行处理。因此可能需要把LlamaIndex的检索器包括检索过程，对接到Langchain的检索器类型当中。



## 实现

首先我们依然是通过LlamaIndex构建索引，然后生成检索器。

第二我们需要实现一个自定义类来继承Langchain的检索器基类，通过我们的LlamaIndex来实现它的方法，并且以Langchain的文档类型返回

### 自定义Langchain检索器

```
from typing import Any, List
from pydantic import Field
from langchain_core.retrievers import BaseRetriever
from langchain_core.documents import Document as LangchainDocument
from config.config import SIMILARITY_TOP_K

from logger import get_logger
logger = get_logger(__name__)


class LlamaIndexRetriever(BaseRetriever):
    index_retriever: Any = Field(default=None, description="LlamaIndex retriever instance")
    
    def __init__(self, index_retriever: Any, **kwargs):
        super().__init__(**kwargs)
        self.index_retriever = index_retriever
        
    def _get_relevant_documents(self, query: str) -> List[LangchainDocument]:
        # 打印查询时的维度
        logger.info(f"查询时的维度: {self.index_retriever._vector_store._faiss_index.d}")
        logger.info(f"查询向量: {self.index_retriever._vector_store._faiss_index.ntotal}")
        
        nodes = self.index_retriever.retrieve(query)
        logger.info(f"检索到的节点数量: {len(nodes)}")
        return [
            LangchainDocument(
                page_content=node.text,
                metadata=node.metadata
            )
            for node in nodes
        ]
        
    async def _aget_relevant_documents(self, query: str) -> List[LangchainDocument]:
        return self._get_relevant_documents(query)

def get_retriever(index):
    """创建检索器"""
    index_retriever = index.as_retriever(similarity_top_k=SIMILARITY_TOP_K)
    return LlamaIndexRetriever(index_retriever=index_retriever) 
```



### 构建检索&问答链

#### 简单构建

通过前面获取到了我们自定义的检索器，可以通过from_chain_type()的方式构建chain

```
你是一个智能中文助手

参考资料：
{context}

请根据参考资料，准确自然的回答用户当前的问题：
{query}
```

```python
# 单query交互
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    chain_type="stuff",
    chain_type_kwargs={"prompt": prompt_template},
    return_source_documents=True
) 
```

```python
qa_chain.invoke({"query":"你好"})
```

这样的方式就已经可以使用了效果还挺好，同时通过 return_source_documents=True ， 能够返回检索过程的文档列表。

这样的方式局限在于只能单次query交互，在这样的方式情况下你的提示词模板只能有两个参数，一个是context 一个随便叫啥（question）

context是内定的，他会把检索器的资料放到context中，不需要你传。另外一个参数就是你传入的叫啥名都可以。他会拿去进行检索，然后再进行LLM问答

当使用这种方式时我们无法进行多轮对话的输入，因为只能输入一个query.  它既进行检索也进行问答.  



#### 浓缩上下文构建

```python
# 多query但是浓缩上下文
# 构建聊天 Prompt
chat_prompt = ChatPromptTemplate.from_messages([
    SystemMessage(content='''
    ## 你的角色
    你是一智能中文助手

    ## 你的任务
    以亲切专业的语气完成以下职责：xxx

    ## 知识库
    以下是查询所得到的知识库内容：\n
    {conntent}
    '''),
    HumanMessage(content="{question}")
])

# 构建链
qa_chain = ConversationalRetrievalChain.from_llm(
    llm=llm,
    retriever=retriever,
    condense_question_prompt=chat_prompt,
    return_source_documents=True
)
```

通过ConversationalRetrievalChain的方式，虽然可以累计消息列表，也就更新chat_prompt消息列表的方式，可以累计多条消息。但是这种方式是一种浓缩的方式，所以虽然支持消息历史而不是只能单条，但是效果很差。



#### 自定义构建

上面都是曲线救国，正常来说应该不会用，通过自定义的方式才是最简单的，也是满足大多需要的

```python
# 从检索器中拿上下文
docs = retriever.get_relevant_documents(query)
context = "\n\n".join([doc.page_content for doc in docs])

qa_llm_chain = LLMChain(
    llm=llm,
    prompt=prompt_template
)

result = qa_llm_chain.invoke({
    "query": query,
    "chat_history": chat_history,
    "context": context
})
```

通过这样的方式，不用走它的检索器chain，就一个正常chain，完全自定义构建提示词，自己调用检索得到文档、自己组装消息列表 。自定义拼接到提示词模板即可。



##  总结

正常都是使用最后的方式，也不需要自定义检索器类。他这种配合检索器的构建链可能是快速构建rag的方式吧，但是基本上想要自定义程度或者掌控程度越高就越偏原生一点去写。





