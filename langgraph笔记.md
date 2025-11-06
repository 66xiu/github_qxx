# 关于langgraph的一些使用


## LangGraph 的节点
LangGraph 的节点可以是三类对象：

1. 普通函数（def 或 async def）

2. `Runnable` 对象（如 Chain、Agent、LLM、RunnableSequence 等）

3. Graph / CompiledGraph（子图）

### 关于`Runnable` 对象
在 LangChain / LangGraph 体系中，`Runnable` 是一个统一的可执行对象抽象，定义在 `langchain.schema.runnable ` 模块里。

它的核心思想是：

> “任何可以接收输入、产生输出的东西，都可以被包装成一个 Runnable。”

这包括模型调用、工具调用、函数、管线、甚至 Graph 本身。

```
# RunnableLambda 包装自定义函数, 当 Graph 运行到该节点时，LangGraph 会自动调用该 Runnable 的 .invoke() 或 .ainvoke()。

from langchain.schema.runnable import RunnableLambda

graph.add_node("lambda_node", RunnableLambda(lambda x: {"msg": x["input"].upper()}))

```

