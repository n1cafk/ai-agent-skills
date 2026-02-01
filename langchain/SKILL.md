---
name: langchain
description: Build LLM applications with LangChain and LangGraph. Use when creating RAG pipelines, agent workflows, chains, or complex LLM orchestration. Triggers on LangChain, LangGraph, LCEL, RAG, retrieval, agent chain.
license: MIT
source: hoodini/ai-agents-skills
---

# LangChain & LangGraph

Build sophisticated LLM applications with composable chains and agent graphs.

## Quick Start

```bash
pip install langchain langchain-openai langchain-anthropic langgraph
```

```python
from langchain_anthropic import ChatAnthropic
from langchain_core.prompts import ChatPromptTemplate

llm = ChatAnthropic(model="claude-3-sonnet-20240229")
prompt = ChatPromptTemplate.from_template("Explain {topic} in simple terms.")
chain = prompt | llm

response = chain.invoke({"topic": "quantum computing"})
```

## LCEL (LangChain Expression Language)

```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

chain = (
    {"topic": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

result = chain.invoke("machine learning")
```

## RAG Pipeline

```python
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import Chroma

embeddings = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(documents, embeddings)
retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

prompt = ChatPromptTemplate.from_template("""
Answer based on the following context:
{context}

Question: {question}
""")

rag_chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)
```

## LangGraph Agent

```python
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode
from langchain_core.tools import tool
from typing import TypedDict, Annotated
import operator

class AgentState(TypedDict):
    messages: Annotated[list, operator.add]

@tool
def search(query: str) -> str:
    """Search the web."""
    return f"Results for: {query}"

tools = [search]

graph = StateGraph(AgentState)
graph.add_node("agent", call_model)
graph.add_node("tools", ToolNode(tools))
graph.set_entry_point("agent")
graph.add_conditional_edges("agent", should_continue, {"continue": "tools", "end": END})
graph.add_edge("tools", "agent")

app = graph.compile()
```

## Structured Output

```python
from langchain_core.pydantic_v1 import BaseModel, Field

class Person(BaseModel):
    name: str = Field(description="Person's name")
    age: int = Field(description="Person's age")

structured_llm = llm.with_structured_output(Person)
result = structured_llm.invoke("John is a 30 year old engineer")
```

## Resources

- **LangChain Docs**: https://python.langchain.com/docs/introduction/
- **LangGraph Docs**: https://langchain-ai.github.io/langgraph/
- **LangSmith**: https://smith.langchain.com/
