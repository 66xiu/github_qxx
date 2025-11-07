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

## LangGraph子图
在 LangGraph 中，子图（Subgraph） 是一种将图（Graph）作为节点（Node）嵌入到另一个图中的机制，体现了封装性与模块化的设计思想。

子图的核心问题在于**父图与子图之间的状态通信机制**。根据状态模式是否共享，LangGraph 提供了两种通信范式：

1. **共享状态模式**（Shared State Schema）

2. **不同状态模式**（Distinct State Schema）



### 共享状态模式
当父图与子图的状态类型（TypedDict）包含至少一个**共享键**（Shared Key） 时，子图可直接作为父图的一个节点加入，**无需显式状态转换**。LangGraph 会自动将父图状态中与子图共享的键传递给子图，并将子图返回的更新合并回父图状态。

关键点：共享键必须在两个图的状态类型中具有相同名称与兼容类型。

详细见：https://zhuanlan.zhihu.com/p/1960080664624894947  中`2. 共享状态模式`



### 不同状态模式
当父图与子图的状态类型无任何共享键时，无法直接嵌入子图。此时需通过一个**中间调用函数**（Wrapper Node） 显式完成状态映射。

详细见：https://zhuanlan.zhihu.com/p/1960080664624894947  中`3. 不同状态模式`



