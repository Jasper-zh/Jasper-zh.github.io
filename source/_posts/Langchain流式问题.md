---
title: Langchain流式问题
date: 2025-03-25 17:56:47
tags: LLM
categories: 
- LLM
---

## 前言

构建好chain，正常run或者invoke没问题但是流式一直是连接后等半天单次就输出完毕了

```python
async for chunk in qa_llm_chain.astream({
    "query": query,
    "chat_history": chat_history,
    "context": context,
    "tone": tone
}):
    # 打印 chunk 的内容和类型
    logger.info(f"chunk type: {type(chunk)}")
    logger.info(f"chunk content: {chunk}")
```



## 解决

挺奇怪的，按理来说应该是可以的。一直好像解决不了，最终是选择直接通过llm调用，而不是chain的方式

```java
# 手动构造 prompt 文本
prompt_str = prompt_template.format(
    query=query,
    chat_history=chat_history,
    context=context,
    tone=tone
)


 async for chunk in llm.astream(prompt_str):
     # 打印 chunk 的内容和类型
     logger.info(f"chunk type: {type(chunk)}")
     logger.info(f"chunk content: {chunk}")
```



## 总结

等有时间了这块地方再回来看看，看看是不是chain的astream是假流式，还是说使用方式上有什么问题。目前解决是直接通过llm调用就ok了。