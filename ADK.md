# Architecture: Multi-Agent AI System on Google Cloud

**Overview:** This architecture demonstrates how to build a robust, scalable multi-agent system where a "Coordinator" agent manages specialized sub-agents to solve complex user requests. It integrates **Agent Patterns** (Sequence, Iterative Refinement) with **Cloud Infrastructure** (Runtime, Security, Observability).

## **1\. The Workflow (The Journey of a Prompt)**

Follow the green numbered circles in the diagram to understand the flow:

* **Step 1 (The Trigger):** The **Application User** sends a prompt via a **Frontend** (hosted on Cloud Run). This is the chat interface.  
    
* **Step 2 (The Manager):** The request goes to the **Coordinator Agent**.  
  * *Role:* It analyzes the request using the AI Model and decides how to split the work.

* **Step 3 (The Delegation):** The Coordinator invokes specialized **Subagents**. The diagram shows two distinct paths happening here:  
  * **Path A (Left \- Sequence):** A linear workflow. Task-A finishes and passes data to Task-A.1.  
  * **Path B (Right \- Iterative Refinement):** A quality loop. Task-B creates output \-\> Quality Evaluator checks it \-\> If bad, Prompt Enhancer fixes it \-\> Loops back to Task-B.  
* **Step 4 (The Synthesis):** Once both paths are done, the results are sent to the **Response Generator**.  
* **Step 5 (The Output):** The final answer is sent back to the Coordinator, then to the Frontend, and finally to the User.

---

## **2\. Core Components**

### **🧠 The Agents (The Logic Layer)**

* **Coordinator Agent:** The central brain (using the **Coordinator Pattern**). It uses **A2A (Agent2Agent) Protocol** to talk to other agents, regardless of what language they are written in.  
* **Subagents:** Specialized workers (Task-A, Task-B).  
* **ADK (Agent Development Kit):** The framework used by developers to write the code for these agents, abstracting away the complex logic.

### **⚙️ The Runtimes (Where it Runs)**

You have three options for where to actually host/run these agents:

1. **Cloud Run:** Serverless. Good for easy deployment and scaling (starts from zero).  
2. **GKE (Google Kubernetes Engine):** For complex, containerized deployments that need deep control.  
3. **Vertex AI Agent Engine:** A managed platform specifically built for hosting agents.

### **🛡️ Security & Intelligence**

* **AI Model (The Brain):** The agents send "Inference requests" to a model (like **Gemini**) hosted on **Vertex AI**.  
* **Model Armor (The Shield):** A security layer that sits between the Agent and the Model. It inspects inputs and outputs to sanitize data (preventing injection attacks or toxic responses).

### **🔌 Connectivity (Tools)**

* **MCP (Model Context Protocol):** This is the industry standard for connecting agents to tools.  
  * **MCP Clients:** The agent uses this client to ask for data.  
  * **MCP Servers:** These sit in front of your Database or API. They translate the agent's request into a query the database understands.

---

## **3\. Design Patterns in Action**

This architecture is powerful because it mixes two different patterns in one system:

1. **Sequential Pattern (Left Side):** Task-A \-\> Task-A.1. Used for the predictable part of the workflow.  
2. **Iterative Refinement Pattern (Right Side):** The loop between Task-B, Quality Evaluator, and Prompt Enhancer. Used for the complex part of the workflow that needs high quality (like generating code or writing a report).

# 🏗️ Understanding the AI Agent Technology Stack

---

## **✅ 1\. Google Kubernetes Engine (GKE)**

**What it is:**  
 Google’s fully managed Kubernetes platform for running containerized systems.

**Key Points:**

* Best for **large, complex agent systems** with multiple microservices (Coordinator, Workers, Vector DB, API services).

* Designed for **high scalability**, heavy workloads, and enterprise environments.

* Two modes:

  * **Autopilot:** Google manages nodes (recommended).

  * **Standard:** You manage the nodes (more control, more DevOps).

* Ideal when your agents need **orchestration, load balancing, autoscaling, and resilience**.

**When to use:**

* Multi-agent systems

* High traffic (thousands of users)

* Systems needing custom scaling rules

* Distributed workflows

---

## **✅ 2\. Model Armor**

**What it is:**  
 Security “firewall” for LLMs.  
 Protects both **input → model** and **model → output**.

**What it protects against:**

* Prompt Injection

* Jailbreak attempts

* Sensitive Data Exposure (PII filtering, tokenization)

* Malicious URLs

* Unsafe content (Hate, Violence, Adult, Self-harm)

* Policy violations

**How it works:**  
 Model Armor sits between the user request and the LLM, inspecting both the prompt and the generated response.

**Why it is critical:**  
 Enterprise agents cannot run without security layers. Model Armor ensures the agent behaves safely and compliant with organization policies.

---

## **✅ 3\. Cloud Run**

**What it is:**  
 A fully serverless container runtime.

**Key Advantages:**

* Simple to deploy

* Auto-scales instantly

* **Scale-to-zero** → costs nothing when unused

* Perfect for **single-agent** or **lightweight agent apps**

* Very low operational overhead

**When to use:**

* Small/medium agent projects

* API-based LLM services

* MVPs and prototypes

* When you want simplicity with good scalability

---

## **✔️ Cloud Run vs GKE (Clear Difference)**

| Feature | Cloud Run | GKE |
| ----- | ----- | ----- |
| Complexity | Low | High |
| Cost | Pay only when used | Always running if you have nodes (except Autopilot) |
| Architecture | Single container / few services | Multi-service, distributed |
| Control | Limited | Full Kubernetes control |
| Scaling | Very fast, serverless | Advanced autoscaling options |

**Truth:**  
 Neither is “better.”  
 They solve **different classes** of problems.

---

## **✅ 4\. Vertex AI (The ML \+ GenAI Platform)**

**What it is:**  
 Google’s unified platform for ML, LLMs, MLOps, and Generative AI.

**Core Capabilities:**

* **Train models** (AutoML, custom training)

* **Fine-tune Gemini** and import open-source LLMs

* **Model Garden** (Gemini, Llama, Mistral, Gemma)

* **Feature Store** for ML data

* **Model Monitoring** to detect drift or failure

* **Pipelines** to automate training → evaluation → deployment

* **GenAI tools** for text, images, speech, embeddings

* **RAG evaluators** and system-level metrics

**Role in Agents:**

* Acts as the **brain factory**

* All LLM operations, fine-tuning, serving, and monitoring happen here

* Integrates with Cloud Run or GKE for deployment

---

# **🎯 Final Summary**

* **GKE** → Best for large, distributed, multi-agent systems requiring full orchestration.

* **Cloud Run** → Best for simpler, single-agent or small-container systems; serverless and cost-efficient.

* **Model Armor** → Mandatory enterprise-grade LLM security (input/output protection).

* **Vertex AI** → Core platform for models, training, tuning, deployments, and MLOps.

Choose a design pattern for your agentic AI system

## **🧱 What is an Agent Design Pattern?**

An **Agent Design Pattern** is a common architectural approach used to build agentic applications. It provides a distinct framework for:

1. **Organizing Components:** Structuring the agent's Model, Tools, and Memory.  
2. **Integrating the Model:** Defining the role of the LLM (e.g., is it the sole planner or just a tool?).  
3. **Orchestration:** Governing the flow and collaboration among single or multiple agents to accomplish a workflow.

### **Why Agents Need Patterns**

AI agents are best suited for:

* **Open-ended problems** requiring autonomous decision-making.  
* **Complex multi-step workflow** management.  
* **Knowledge-intensive tasks** that require real-time external data (grounding).

The patterns provide the structure needed to manage this complexity and autonomy safely and efficiently.

---

## **🗺️ The Design Process: Choosing a Pattern**

Choosing the right design pattern is not a one-time decision but a process guided by your specific needs. It involves three high-level steps:

### **1\. Define Your Requirements (Assess the Workload)**

This initial step involves closely examining the characteristics of the task you want the agent to solve:

* **Task Complexity:** Is it a simple lookup (Level 1\) or a multi-step planning and collaboration task (Level 3)?  
* **Latency and Performance:** How quickly must the agent respond? (e.g., real-time customer service vs. nightly reporting).  
* **Cost Budget:** More complex patterns involving multiple agent calls and large models cost more.  
* **Need for Human Involvement (Safety):** Does the task involve high-stakes actions that require approval (e.g., financial transactions)?

### **2\. Review the Common Agent Design Patterns**

Once you know your requirements, you learn the capabilities of the available patterns (which include both **single-agent systems** and **multi-agent systems**).

### **3\. Select a Pattern**

You match the pattern's architecture and strengths to the workload characteristics defined in Step 1\.

---

**Crucial Note:** This guide assumes you understand that agent architecture is fundamentally different from simpler AI applications like:

* **Direct Model Reasoning:** Calling the LLM once without tools or memory.  
* **RAG (Retrieval-Augmented Generation):** Calling the LLM once with external documents (but usually without multi-step planning).

This foundational understanding ensures you choose an agent pattern only when the task truly requires the **autonomous decision-making** and **complex workflow management** that agents provide.

Here are your complete, structured notes on **Agentic AI Design Patterns**. These are formatted specifically for you to copy and paste directly into a Google Doc for your portfolio or study guide.

---

# **📘Agentic AI Design Patterns**

**Definition:** Agent design patterns are architectural frameworks used to organize an AI system's components (Model, Tools, Memory) and orchestrate how they solve problems. Choosing the right pattern depends on the trade-off between 

**Autonomy** (flexibility) and **Control** (reliability).

---

## **1\. Foundational Architectures**

### **👤 Single-Agent System**

The fundamental building block. A single AI model uses a defined set of tools and a comprehensive system prompt to handle a request from start to finish.

* **How it works:** The user sends a prompt → The Agent reasons → Calls Tools → Returns Answer.  
* **Best For:** Prototypes, MVPs, and tasks requiring simple tool use (e.g., "Search for the weather and convert to Celsius").  
* **Pros:** Simple to build, low cost, easy to debug.  
* **Cons:** Fails as complexity grows; context window gets overcrowded; single point of failure.

### **🤝 Multi-Agent System (General Concept)**

A system that orchestrates multiple **specialized agents** to solve a complex problem.

* **Core Principle:** **Decomposition.** Breaking a massive goal into smaller sub-tasks (e.g., Researcher, Writer, Coder).  
* **Why Use It:** When a single prompt is too complex for one context window, or when different sub-tasks require different tools/personas.  
* **Key Component:** **Context Engineering** (managing what information is passed between agents).

---

## **2\. Deterministic Workflows (Predictable)**

*Use when the workflow path is known in advance and does not change.*

### **➡️ Sequential Pattern (The Assembly Line)**

Executes agents in a fixed, linear order.

* **Structure:** Input → \[Agent A\] → Output A/Input B → \[Agent B\] → Output.  
* **Best For:** Highly structured pipelines (e.g., Data Extraction → Data Cleaning → Database Insertion).  
* **Trade-off:** Low latency and cost, but very rigid. If one step fails or is slow, the whole chain stops.

### **🔀 Parallel Pattern (Concurrent Processing)**

Executes multiple sub-agents at the same time, then synthesizes the results.

* **Structure:** Input is sent simultaneously to Agent A, Agent B, and Agent C. Their outputs are aggregated by a final step.  
* **Best For:** Tasks needing diverse perspectives or speed (e.g., Analyzing a stock symbol with Sentiment Agent, Technical Agent, and Fundamental Agent simultaneously).  
* **Trade-off:** Reduces latency (speed), but increases cost (multiple calls at once) and complexity in merging conflicting results.

### **🔄 Loop Pattern (The Monitor)**

Repeatedly executes a task until a specific condition is met.

* **Structure:** Run Agent → Check Exit Condition. If "No", run again. If "Yes", stop.  
* **Best For:** Monitoring tasks or retries (e.g., "Check server status every 5 minutes until it returns 200 OK").  
* **Trade-off:** Risk of infinite loops (high cost) if the exit condition is never met.

---

## **3\. Iterative & Quality Workflows (The "Quality" Loop)**

*Use when the first draft is likely imperfect and needs refinement.*

### **🧠 ReAct Pattern (Reason \+ Act)**

The standard operating system for modern agents. It forces the model to "think" before it "acts."

* **Structure:**  
  1. **Thought:** Model analyzes the request.  
  2. **Action:** Model chooses a tool.  
  3. **Observation:** Model reads the tool output.  
  4. **Repeat:** Updates thought based on observation until done.  
* **Best For:** Dynamic tasks requiring continuous planning (e.g., "Find the phone number of the CEO of the company that makes the iPhone").  
* **Visual:**

### **🧐 Review and Critique Pattern (Generator & Critic)**

A specific quality assurance workflow using two distinct agents.

* **Structure:**  
  1. **Generator Agent:** Creates the content (e.g., Code).  
  2. **Critic Agent:** Evaluates it against hard criteria (e.g., "Does it have security bugs?").  
  3. **Feedback:** If rejected, sends feedback back to Generator.  
* **Best For:** High-stakes content generation (Code generation, Legal drafting) where accuracy is paramount.  
* **Trade-off:** Higher cost (paying for two agents per turn).

### **💎 Iterative Refinement Pattern (The Polisher)**

The general strategy of progressively improving an output through cycles.

* **Structure:** Generate Draft → Evaluate → Enhance Prompt/Context → Regenerate Draft.  
* **Advanced Feature:** Often uses a **Prompt Enhancer** subagent to rewrite the instructions for the next loop based on previous failures.  
* **Best For:** Complex creative or reasoning tasks where the "perfect" answer requires polishing (e.g., writing a novel chapter).  
* **Visual:**

---

## **4\. Dynamic Orchestration (The "Manager" Models)**

*Use when the path is unknown and requires AI intelligence to navigate.*

### **🧭 Coordinator Pattern (The Router)**

Uses a central "Manager" agent to dynamically route tasks.

* **Structure:** User → **Coordinator Agent** (Decides "Who handles this?") → Routes to **Specialist Subagent**.  
* **Best For:** Adaptive workflows like Customer Support (Classifying "Refund" vs. "Tech Support" vs. "Sales" and routing accordingly).  
* **Trade-off:** Flexible and smart, but adds latency (the Manager must "think" before anyone works).  
* **Visual:**

### **🌳 Hierarchical Task Decomposition (The Organization Chart)**

A multi-level hierarchy of agents handling massive planning.

* **Structure:** **Root Agent** (CEO) breaks goal into chunks → **Middle Agents** (Managers) break chunks into tasks → **Leaf Agents** (Workers) execute tasks.  
* **Best For:** Extremely ambiguous, large-scale goals (e.g., "Plan a 3-day conference," "Write a software application from scratch").  
* **Trade-off:** Most powerful, but highest latency and hardest to debug.  
* **Visual:**

### **🐝 Swarm Pattern (The Roundtable)**

Collaborative, all-to-all communication without a central boss.

* **Structure:** Agents act as peers. Agent A can talk to B, who talks to C, who talks back to A. They debate and hand off tasks dynamically.  
* **Best For:** Brainstorming, creative problem solving, and tasks requiring diverse expert viewpoints without a rigid process.  
* **Trade-off:** High creativity, but high risk of "looping" (arguing forever) and very expensive.

---

## **5\. Special Patterns (Safety)**

### **✋ Human-in-the-Loop (HITL)**

Integrates a mandatory checkpoint for human approval.

* **Structure:** Agent works → Reaches Checkpoint → **Pauses State** → Sends Notification (Email/UI) → **Waits for Human Click** → Resumes.  
* **Best For:** Sensitive actions (Refunds \> $100, deploying code to production, deleting database records).  
* **Visual:**

---

## **📊 Quick Decision Matrix (Cheatsheet)**

| If your workflow is... | Use this Pattern |
| :---- | :---- |
| **Predictable & Linear** | **Sequential** |
| **Needs Speed / Independent Tasks** | **Parallel** |
| **Requires Monitoring/Retries** | **Loop** |
| **Requires Quality/Safety** | **Review & Critique** or **HITL** |
| **Needs Dynamic Routing** | **Coordinator** |
| **Huge, Ambiguous Project** | **Hierarchical Decomposition** |
| **Creative Brainstorming** | **Swarm** |

