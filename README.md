
# 🦜🕸️ LangGraph Hands-On: Agentic AI Projects

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![LangGraph](https://img.shields.io/badge/LangGraph-0.2%2B-green)
![LangChain](https://img.shields.io/badge/LangChain-Google_GenAI-orange)

This repository contains a progressive series of projects designed to master **LangGraph**, a framework for building stateful, multi-actor AI applications. The projects evolve from basic graph structures to advanced **Human-in-the-Loop (HITL)** autonomous agents.
---

## 🚀 Quick Start

### 1) Clone the repository
```bash
git clone https://github.com/PickPro/langgraph-hands-on.git
cd langgraph-hands-on
````

### 2) Install dependencies

You will need LangGraph, LangChain, and the Google Generative AI integration.

```bash
pip install langgraph langchain-google-genai langchain langchain-community
```

### 3) Environment setup

Create a `.env` file in the root directory and add your Google Gemini API key:

```env
GOOGLE_API_KEY=your_google_api_key_here
```

---

## 📂 Project Breakdown

### Project 1: The "Hello World" Graph

**Focus:** Understanding State, Nodes, and Linear Edges.

This project builds a deterministic workflow to calculate the value of an investment portfolio. It introduces the concept of a shared **State** that passes through a chain of Python functions.

* **State Schema:**

  ```python
  class PortfolioState(TypedDict):
      amount_usd: float
      total_usd: float
      total_inr: float
      target_currency: str
  ```

* **Workflow:** `Start` → `calculate_total` → `convert_currency` → `End`

* **Key Learning:** How `StateGraph` manages data flow between isolated functions.

---

### Project 2: Conditional Routing (Dynamic Flows)

**Focus:** Conditional Edges and Logic Branching.

We upgrade the portfolio calculator to handle multiple currencies. Instead of a straight line, the graph now makes decisions based on the state.

* **The Logic:** A helper function `choose_conversion` inspects `state["target_currency"]`.

  * If `EUR` → route to `convert_to_eur` node
  * If `INR` → route to `convert_to_inr` node

* **Code Highlight:**

  ```python
  graph.add_conditional_edges(
      "calculate_total",
      choose_conversion,  # Decision function
      {
          "convert_to_inr": "convert_to_inr",
          "convert_to_eur": "convert_to_eur"
      }
  )
  ```

---

### Project 3: Context-Aware Chatbot

**Focus:** Memory Management with Reducers.

A chatbot that remembers conversation history. In standard Python, lists are overwritten on assignment. Here, we use LangGraph's **Reducers** to append data instead.

* **State Change:**

  ```python
  from typing_extensions import TypedDict
  from typing import Annotated
  from langgraph.graph.message import add_messages

  class ChatState(TypedDict):
      # 'add_messages' ensures new messages are APPENDED, not overwritten
      messages: Annotated[list, add_messages]
  ```

* **Outcome:** The bot can answer follow-up questions like *"Who is he?"* because the history is preserved in the state.

---

### Project 4: The Agent (Tools & Cycles)

**Focus:** Tool Binding and Cyclic Graphs.

This transforms the chatbot into an **Agent** that can fetch external data (simulated stock prices). Unlike previous linear graphs, this one introduces a **Cycle**.

* **Architecture:**

  1. **LLM Node:** decides if it needs data; if yes, it generates a tool call.
  2. **Tool Node:** executes the tool (e.g., `get_stock_price`) and returns raw data.
  3. **Cycle:** the edge points back to the LLM node.
  4. **LLM Node (Again):** reads the tool output and generates the final answer.

* **Key Method:** `.bind_tools([...])` attaches Python functions to the Gemini model.

---

### Project 5: Persistence (Long-Term Memory)

**Focus:** Checkpointers and Threads.

Standard scripts lose memory when they stop running. This project uses a **Checkpointer** to save the graph's state after every step.

* **Implementation:**

  ```python
  from langgraph.checkpoint.memory import MemorySaver

  memory = MemorySaver()
  graph = builder.compile(checkpointer=memory)
  ```

* **Threading:** With `config={"configurable": {"thread_id": "1"}}`, you can resume specific conversations later or keep separate histories per user.

---

### Project 6: Human-in-the-Loop (HITL) Trading Bot

**Focus:** `interrupt` and `Command` for Safety.

An autonomous trading agent that can recommend trades but must wait for human approval before executing them.

* **Workflow:**

  1. Agent calls `buy_stock`.
  2. Execution suspends with `interrupt()`.
  3. Human reviews and approves/denies.
  4. Graph resumes with a `Command` containing the decision.

* **Why it matters:** Critical for high-stakes agents (finance, medical) where AI autonomy must be bounded by human oversight.

---

## 📚 Resources

* [LangGraph Documentation](https://langchain-ai.github.io/langgraph/)
* [LangChain Google GenAI](https://python.langchain.com/docs/integrations/chat/google_generative_ai/)


