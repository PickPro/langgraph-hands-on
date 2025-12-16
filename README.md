````md

\# 🦜🕸️ LangGraph Hands-On: Agentic AI Projects



!\[Python](https://img.shields.io/badge/Python-3.10%2B-blue)

!\[LangGraph](https://img.shields.io/badge/LangGraph-0.2%2B-green)

!\[LangChain](https://img.shields.io/badge/LangChain-Google\_GenAI-orange)



This repository contains a progressive series of projects designed to master \*\*LangGraph\*\*, a framework for building stateful, multi-actor AI applications. The projects evolve from basic graph structures to advanced \*\*Human-in-the-Loop (HITL)\*\* autonomous agents.



> \*\*Based on the tutorial:\*\* \[Agentic AI Tutorial for Beginners | LangGraph Tutorial](https://www.youtube.com/watch?v=CnXdddeZ4tQ)



---



\## 🚀 Quick Start



\### 1) Clone the repository



```bash

git clone https://github.com/PickPro/langgraph-hands-on.git

cd langgraph-hands-on

````



\### 2) Install dependencies



You will need LangGraph, LangChain, and the Google Generative AI integration.



```bash

pip install langgraph langchain-google-genai langchain langchain-community

```



\### 3) Environment setup



Create a `.env` file in the root directory and add your Google Gemini API key:



```env

GOOGLE\_API\_KEY=your\_google\_api\_key\_here

```



---



\## 📂 Project Breakdown



\### Project 1: The "Hello World" Graph



\*\*Focus:\*\* Understanding State, Nodes, and Linear Edges.



This project builds a deterministic workflow to calculate the value of an investment portfolio. It introduces the concept of a shared \*\*State\*\* that passes through a chain of Python functions.



\* \*\*State Schema:\*\*



&nbsp; ```python

&nbsp; class PortfolioState(TypedDict):

&nbsp;     amount\_usd: float

&nbsp;     total\_usd: float

&nbsp;     total\_inr: float

&nbsp;     target\_currency: str

&nbsp; ```



\* \*\*Workflow:\*\* `Start` → `calculate\_total` → `convert\_currency` → `End`



\* \*\*Key Learning:\*\* How `StateGraph` manages data flow between isolated functions.



---



\### Project 2: Conditional Routing (Dynamic Flows)



\*\*Focus:\*\* Conditional Edges and Logic Branching.



We upgrade the portfolio calculator to handle multiple currencies. Instead of a straight line, the graph now makes decisions based on the state.



\* \*\*The Logic:\*\*

&nbsp; A helper function `choose\_conversion` inspects `state\["target\_currency"]`.



&nbsp; \* If `EUR` → route to `convert\_to\_eur` node

&nbsp; \* If `INR` → route to `convert\_to\_inr` node



\* \*\*Code Highlight:\*\*



&nbsp; ```python

&nbsp; graph.add\_conditional\_edges(

&nbsp;     "calculate\_total",

&nbsp;     choose\_conversion,  # Decision function

&nbsp;     {

&nbsp;         "convert\_to\_inr": "convert\_to\_inr",

&nbsp;         "convert\_to\_eur": "convert\_to\_eur"

&nbsp;     }

&nbsp; )

&nbsp; ```



---



\### Project 3: Context-Aware Chatbot



\*\*Focus:\*\* Memory Management with Reducers.



A chatbot that remembers conversation history. In standard Python, lists are overwritten on assignment. Here, we use LangGraph's \*\*Reducers\*\* to append data instead.



\* \*\*State Change:\*\*



&nbsp; ```python

&nbsp; from typing\_extensions import TypedDict

&nbsp; from typing import Annotated

&nbsp; from langgraph.graph.message import add\_messages



&nbsp; class ChatState(TypedDict):

&nbsp;     # 'add\_messages' ensures new messages are APPENDED, not overwritten

&nbsp;     messages: Annotated\[list, add\_messages]

&nbsp; ```



\* \*\*Outcome:\*\* The bot can answer follow-up questions like \*"Who is he?"\* because the history of "he" (from previous turns) is preserved in the state.



---



\### Project 4: The Agent (Tools \& Cycles)



\*\*Focus:\*\* Tool Binding and Cyclic Graphs.



This transforms the chatbot into an \*\*Agent\*\* that can fetch external data (simulated stock prices). Unlike previous linear graphs, this one introduces a \*\*Cycle\*\*.



\* \*\*Architecture:\*\*



&nbsp; 1. \*\*LLM Node:\*\* Decides if it needs data. If yes, it generates a "Tool Call".

&nbsp; 2. \*\*Tool Node:\*\* Executes the tool (e.g., `get\_stock\_price`) and returns raw data.

&nbsp; 3. \*\*Cycle:\*\* The edge points \*\*back\*\* to the LLM Node.

&nbsp; 4. \*\*LLM Node (Again):\*\* Reads the tool output and generates a final natural language answer.



\* \*\*Key Method:\*\* `.bind\_tools(\[...])` attaches Python functions to the Gemini model.



---



\### Project 5: Persistence (Long-Term Memory)



\*\*Focus:\*\* Checkpointers and Threads.



Standard scripts lose memory when they stop running. This project uses a \*\*Checkpointer\*\* to save the graph's state to a database (or in-memory store) after every step.



\* \*\*Implementation:\*\*



&nbsp; ```python

&nbsp; from langgraph.checkpoint.memory import MemorySaver



&nbsp; memory = MemorySaver()

&nbsp; graph = builder.compile(checkpointer=memory)

&nbsp; ```



\* \*\*Threading:\*\* By passing `config={"configurable": {"thread\_id": "1"}}`, we can resume specific conversations days later, or maintain separate histories for different users.



---



\### Project 6: Human-in-the-Loop (HITL) Trading Bot



\*\*Focus:\*\* `interrupt` and `Command` for Safety.



The most advanced project. An autonomous trading agent that can recommend trades but \*\*must wait for human approval\*\* before executing them.



\* \*\*The Workflow:\*\*



&nbsp; 1. Agent calls `buy\_stock` tool.

&nbsp; 2. \*\*INTERRUPT:\*\* The graph execution suspends immediately using `interrupt()`.

&nbsp; 3. \*\*Human Review:\*\* The user sees the request in the console and types "yes" or "no".

&nbsp; 4. \*\*Resume:\*\* The graph resumes execution with a `Command` object containing the human's decision.



\* \*\*Why it matters:\*\* Essential for high-stakes agents (financial, medical) where AI autonomy must be bounded by human oversight.



---



\## 📚 Resources



\* \[LangGraph Documentation](https://langchain-ai.github.io/langgraph/)

\* \[LangChain Google GenAI](https://python.langchain.com/docs/integrations/chat/google\_generative\_ai/)



```

