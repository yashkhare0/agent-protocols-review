# Agentic Protocols & Platforms Deep Dive

*Research compiled: February 17, 2026*
*For: Founder building "Shopify for agentic applications"*

---

## Table of Contents

1. [ChatGPT Apps/Plugins/GPTs Ecosystem](#1-chatgpt-appspluginsgpts-ecosystem)
2. [ACP — Agentic Commerce Protocol](#2-acp--agentic-commerce-protocol)
3. [UCP — Universal Commerce Protocol](#3-ucp--universal-commerce-protocol)
4. [MCP — Model Context Protocol](#4-mcp--model-context-protocol)
5. [A2A — Agent-to-Agent Protocol (Google)](#5-a2a--agent-to-agent-protocol-google)
6. [How These Protocols Relate](#6-how-these-protocols-relate)
7. [The "Shopify for Agents" Opportunity](#7-the-shopify-for-agents-opportunity)
8. [OpenAI's Agent Platform Strategy](#8-openais-agent-platform-strategy)

---

## 1. ChatGPT Apps/Plugins/GPTs Ecosystem

### Evolution Timeline

| Phase | When | What |
|-------|------|------|
| **ChatGPT Plugins** | March 2023 | First attempt at extending ChatGPT with third-party APIs. Used OpenAPI specs. **Discontinued.** |
| **GPTs (Custom GPTs)** | November 2023 | No-code custom ChatGPT versions with instructions, knowledge files, and Actions |
| **GPT Store** | January 2024 | Marketplace for discovering/sharing GPTs. Categories: DALL·E, writing, research, programming, education, lifestyle |
| **Revenue Program** | Q1 2024 (announced) | US builders paid based on user engagement. Details remain sparse on actual payouts. |
| **Responses API** | March 2025 | New API primitive replacing both Chat Completions (for agentic use) and Assistants API |
| **Agents SDK** | March 2025 | Open-source SDK for multi-agent orchestration |
| **Agent Builder** | 2025 | Visual canvas for creating agent workflows in OpenAI dashboard |
| **ChatKit** | 2025 | Embeddable UI component for deploying agents in products |
| **Operator → ChatGPT Agent** | Jan 2025 → Jul 2025 | Browser-using agent, initially standalone, then integrated as "agent mode" in ChatGPT |

### GPT Actions (Current State)

GPT Actions are the core extension mechanism for Custom GPTs. They allow ChatGPT to call external RESTful APIs:

- **How it works**: Developer provides an OpenAPI schema describing API endpoints. ChatGPT uses function calling to (1) decide which API is relevant, (2) generate JSON input, (3) execute the call.
- **Authentication**: Supports OAuth, API keys, custom auth headers — configured per Action.
- **Capabilities**: Data retrieval (query databases, fetch info) and taking actions (file JIRA tickets, send emails, etc.)
- **Limitations**:
  - No real SDK — just OpenAPI spec definition
  - Limited to REST APIs (no WebSocket, GraphQL native support)
  - No server-push / real-time communication
  - Rate limits and execution timeouts
  - No local/on-device execution
  - Discovery is only via GPT Store (no programmatic marketplace API)
  - Revenue sharing program has been underwhelming for most builders

### Developer Building Blocks (2025)

**Responses API** — The new unified API replacing Assistants API (sunset mid-2026):
- Built-in tools: Web Search, File Search, Computer Use
- Item-based design with intuitive streaming
- Supports combining multiple tools in a single call
- Pricing: Standard token rates + tool-specific pricing (Web search: $25-30/1K queries, File search: $2.50/1K queries)

**Agents SDK** (open-source):
- Orchestrates single-agent and multi-agent workflows
- Successor to "Swarm" framework
- Built-in tracing and observability
- Handoffs between agents, guardrails, tool use

**Agent Builder** — Visual no-code canvas:
- Connects models, tools, knowledge, logic nodes
- MCP support for third-party tool connectors
- Guardrails configuration
- Vector stores for knowledge/memory

**ChatKit** — Embeddable chat UI:
- Paste workflow ID to embed agent in your product
- Advanced version: run on own infrastructure with custom widgets

### Monetization for Developers

- **GPT Store revenue sharing**: Announced Q1 2024 for US builders. Based on engagement. Actual payouts have been minimal/unclear.
- **API usage**: Developers build apps using OpenAI APIs and charge their own customers
- **No app store transaction fees** (unlike Apple's 30%)
- **No direct monetization layer** for GPT Actions — you can't charge users for your GPT directly through OpenAI

### Key Limitations / Gaps

1. **No real marketplace economy** — GPT Store lacks pricing, subscriptions, or transaction infrastructure
2. **No agent hosting** — OpenAI provides the model, but developers host their own APIs
3. **No composability** — GPTs can't call other GPTs programmatically
4. **Limited state management** — No built-in persistent memory across conversations (beyond conversation history)
5. **Vendor lock-in** — GPT Actions only work within ChatGPT ecosystem

---

## 2. ACP — Agentic Commerce Protocol

### Overview

| Attribute | Detail |
|-----------|--------|
| **Created by** | **OpenAI + Stripe** |
| **Announced** | 2025 |
| **Current status** | Early stage, actively developing |
| **Focus** | Commerce and payments in the agentic stack |

### What ACP Solves

ACP is the **commerce layer** for AI agents. While MCP connects agents to tools/data and A2A handles agent-to-agent collaboration, ACP answers: **how does an agent handle money?**

The core problem: when an AI agent is acting on behalf of a user — booking a hotel, translating a document, purchasing a subscription — there's no standard way for the agent to:
1. **Discover pricing** for a service
2. **Initiate a transaction** on behalf of the user
3. **Handle checkout** within the agent flow (not redirecting to a website)
4. **Process payments** securely with user consent
5. **Manage subscriptions/metering** for ongoing agent services

### Why OpenAI + Stripe

- **Stripe** = the payments backbone of the internet (processing for millions of businesses)
- **OpenAI** = the largest AI platform with ChatGPT's user base
- Together they're building the standard for how agents transact — Stripe handles the money movement, OpenAI provides the agent orchestration context

### The Gap ACP Fills (Real-World Example)

Consider noll.to's ChatGPT translation app (noll-mcp):
- MCP server exposes `start-translation` and `check-status` tools ✅
- Skybridge widget renders upload/download UI inside ChatGPT ✅
- OAuth authenticates users against noll.to ✅
- **But if a translation is paywalled** — the agent cannot trigger a checkout, charge per-use, or let the user pay within the conversation ❌

The user has to leave ChatGPT → go to noll.to → upgrade → come back. Flow broken.

With ACP, the agent could say "This translation costs €2. Confirm?" → user confirms → Stripe processes → translation delivers. All in-flow.

### Key Capabilities

| Capability | Description |
|------------|-------------|
| **In-flow checkout** | Payment UI rendered within the agent conversation (no redirect) |
| **Agent-initiated purchases** | Agent can propose and execute transactions on user's behalf |
| **Per-use billing** | Metered charges for individual actions (translate a doc, generate an image) |
| **Subscription management** | Create/upgrade/cancel subscriptions through agent interaction |
| **Payment confirmation** | Human-in-the-loop approval before charges |
| **Receipt/invoicing** | Transaction records accessible to both agent and user |

### Relationship to Other Protocols

- **MCP** = what the agent *can do* (tools, data)
- **ACP** = how the agent *gets paid* for doing it (commerce, transactions)
- **UCP** = Google+Shopify's commerce protocol (retail/shopping focused)
- **A2A** = how agents *coordinate* with each other

ACP and UCP both address commerce but from different angles — ACP is payment-infrastructure-first (Stripe DNA), UCP is retail-catalog-first (Google Shopping DNA).

> **Note on naming**: IBM previously used "ACP" for their "Agent Communication Protocol" (agent-to-agent communication). That protocol has been **merged into Google's A2A** under the Linux Foundation and is no longer active as a separate standard. In the current landscape, ACP refers to the **Agentic Commerce Protocol** by OpenAI + Stripe.

---

## 3. UCP — Universal Commerce Protocol

### Overview

| Attribute | Detail |
|-----------|--------|
| **Created by** | **Google + Shopify** |
| **Announced** | 2025 |
| **Current status** | Early stage, actively developing |
| **Focus** | Retail and shopping commerce for agents |

### What UCP Solves

UCP standardizes how AI agents interact with **retail and commerce systems** — product catalogs, inventory, shopping carts, order management. While ACP (OpenAI + Stripe) focuses on the payment rail itself, UCP focuses on the **shopping experience**: browsing products, comparing options, managing carts, and completing purchases through agent interfaces.

### Why Google + Shopify

- **Google** = Search, Shopping, product knowledge graph, Google Pay
- **Shopify** = powers millions of online stores, owns the merchant relationship
- Together they're defining how agents browse, compare, and buy from online stores — the retail-specific commerce standard

### Key Capabilities

| Capability | Description |
|------------|-------------|
| **Product discovery** | Agents can search and browse product catalogs programmatically |
| **Inventory awareness** | Real-time stock/availability information |
| **Cart management** | Add/remove items, apply coupons, calculate totals |
| **Order lifecycle** | Place orders, track shipping, handle returns |
| **Merchant integration** | Standardized way for any Shopify/online store to be agent-accessible |

### ACP vs UCP

| | ACP (OpenAI + Stripe) | UCP (Google + Shopify) |
|---|---|---|
| **DNA** | Payment infrastructure | Retail/catalog infrastructure |
| **Strength** | Any transaction type (subscriptions, per-use, tips) | Shopping-specific (products, carts, orders) |
| **Merchant side** | Stripe integration | Shopify/Google Shopping integration |
| **Agent side** | ChatGPT ecosystem | Google/Gemini ecosystem |

They're complementary — a full agent commerce stack likely uses both. UCP for browsing and selecting products, ACP for processing the actual payment.

---

## 4. MCP — Model Context Protocol

### Overview

| Attribute | Detail |
|-----------|--------|
| **Created by** | Anthropic |
| **Announced** | November 25, 2024 |
| **Current governance** | Linux Foundation (AAIF) as of December 2025 |
| **License** | Open source |
| **Website** | modelcontextprotocol.io |
| **GitHub** | github.com/modelcontextprotocol |
| **Analogy** | "USB-C for AI" — universal connector between AI apps and external systems |
| **Inspiration** | Language Server Protocol (LSP) from VS Code |

### What MCP Does

MCP standardizes how AI applications connect to **external tools, data sources, and workflows**. Before MCP, every AI app needed custom connectors for each integration — an N×M problem. MCP reduces this to N+M.

### Architecture

**Client-Server model** with three participants:

```
┌─────────────────────────────────┐
│  MCP Host (AI Application)      │
│  e.g. Claude Desktop, VS Code   │
│                                  │
│  ┌──────────┐  ┌──────────┐    │
│  │MCP Client│  │MCP Client│    │
│  │    1     │  │    2     │    │
│  └────┬─────┘  └────┬─────┘    │
└───────┼──────────────┼──────────┘
        │              │
   ┌────▼─────┐  ┌────▼─────┐
   │MCP Server│  │MCP Server│
   │(Filesys) │  │(Sentry)  │
   └──────────┘  └──────────┘
```

- **MCP Host**: AI application (Claude Desktop, Claude Code, VS Code, Cursor, etc.)
- **MCP Client**: Component within the host that maintains a connection to one MCP server
- **MCP Server**: Program that provides context (tools, resources, prompts) to clients

### Two-Layer Architecture

**1. Transport Layer** (outer):
- **Stdio transport**: Standard input/output for local processes. Zero network overhead. Single client per server.
- **Streamable HTTP transport**: HTTP POST for client→server + Server-Sent Events for streaming. Supports remote servers, multiple clients. OAuth recommended for auth.

**2. Data Layer** (inner) — JSON-RPC 2.0:

**Server Primitives** (what servers expose):

| Primitive | Purpose | Methods |
|-----------|---------|---------|
| **Tools** | Executable functions (file ops, API calls, DB queries) | `tools/list`, `tools/call` |
| **Resources** | Data sources for context (file contents, DB records) | `resources/list`, `resources/read` |
| **Prompts** | Reusable interaction templates (system prompts, few-shot examples) | `prompts/list`, `prompts/get` |

**Client Primitives** (what clients expose to servers):

| Primitive | Purpose |
|-----------|---------|
| **Sampling** | Server requests LLM completions from the host (model-agnostic) |
| **Elicitation** | Server requests user input/confirmation |
| **Logging** | Server sends log messages to client |

**Experimental**:
- **Tasks**: Durable execution wrappers for deferred results and status tracking

### Lifecycle Management

1. Client sends `initialize` request with protocol version + capabilities
2. Server responds with its capabilities
3. Client sends `initialized` notification
4. Normal operation (tool calls, resource reads, etc.)
5. Connection termination

### SDKs (Official)

| Language | Status |
|----------|--------|
| TypeScript | ✅ Official |
| Python | ✅ Official |
| Java | ✅ Official |
| Kotlin | ✅ Official |
| C# | ✅ Official |
| Go | ✅ Official |
| PHP | ✅ Community |
| Perl | ✅ Community |
| Ruby | ✅ Community |
| Rust | ✅ Community |
| Swift | ✅ Community |

### Adoption (Major)

| Adopter | How |
|---------|-----|
| **Claude Desktop** | Native MCP host |
| **Claude Code** | Native MCP host |
| **OpenAI / ChatGPT** | Adopted March 2025. MCP support in ChatGPT desktop app |
| **Google DeepMind** | Adopted April 2025 |
| **VS Code / Copilot** | MCP host support |
| **Cursor** | MCP host support |
| **Windsurf (Codeium)** | MCP host support |
| **Zed** | MCP host support |
| **Replit** | MCP support for AI coding |
| **Sourcegraph** | MCP for code intelligence |
| **Cloudflare** | Remote MCP server deployment platform |
| **Microsoft** | Semantic Kernel + Azure OpenAI integration |
| **Block (Square)** | Co-founded AAIF, active MCP contributor |

### MCP Server Ecosystem

Hundreds of MCP servers exist for:
- **Databases**: PostgreSQL, MySQL, SQLite, MongoDB
- **Dev tools**: GitHub, GitLab, Jira, Linear, Sentry
- **Productivity**: Google Drive, Notion, Slack, Gmail
- **Cloud**: AWS, GCP, Azure
- **File systems**: Local filesystem access
- **Search**: Brave Search, Google Search
- **Design**: Figma
- **3D**: Blender

Reference implementations maintained at github.com/modelcontextprotocol/servers

### Security Concerns (April 2025)

Researchers identified:
1. **Prompt injection** — Malicious MCP servers can inject prompts
2. **Tool poisoning** — Combining tools to exfiltrate data
3. **Lookalike tools** — Silently replacing trusted tools with malicious ones
4. **Data exfiltration** — Cross-tool permission escalation

### Key Strengths

- **Massive adoption** — The clear winner in the protocol wars
- **Vendor-neutral governance** (Linux Foundation as of Dec 2025)
- **Simple mental model** — Tools, Resources, Prompts
- **Broad SDK support** (11+ languages)
- **Growing ecosystem** of pre-built servers

### Key Limitations

- **Not designed for agent-to-agent communication** — It's agent-to-tool/data
- **Security model still maturing**
- **Local-first bias** — Stdio transport is great for local, but remote MCP (Streamable HTTP) is newer
- **No built-in marketplace/discovery** — Finding MCP servers is fragmented (GitHub, npm, etc.)
- **No monetization layer** — No way to charge for MCP server access

---

## 5. A2A — Agent-to-Agent Protocol (Google)

### Overview

| Attribute | Detail |
|-----------|--------|
| **Created by** | Google |
| **Announced** | April 2025 |
| **Current governance** | Linux Foundation (alongside MCP in AAIF) |
| **Purpose** | Agent-to-agent communication (complements MCP) |

### What A2A Does

While MCP connects agents to tools/data, A2A enables **agents to communicate with other agents**. It defines how one agent discovers, delegates tasks to, and receives results from another agent.

### Key Concepts

- **Agent Cards** — JSON metadata describing agent capabilities (like a business card)
- **Task lifecycle** — Submit, monitor, complete/fail tasks between agents
- **Streaming** — Real-time updates during task execution
- **Push notifications** — Agents notify each other of status changes
- **Multimodal** — Text, files, structured data between agents

### Relationship to ACP

ACP (IBM's protocol) was **merged into A2A** under the Linux Foundation. ACP's REST-based approach and concepts (Agent Manifest, Runs, Messages) influenced A2A's design. Migration guides exist for ACP→A2A.

### Adoption

- Google's own AI tools
- Part of the Linux Foundation AAIF alongside MCP
- Growing enterprise interest for multi-agent orchestration scenarios

---

## 6. How These Protocols Relate

### The Protocol Landscape Map

```
                    ┌─────────────────────────┐
                    │    AGENTIC AI FOUNDATION │
                    │    (Linux Foundation)     │
                    │                          │
                    │  ┌─────┐    ┌─────┐     │
                    │  │ MCP │    │ A2A │     │
                    │  │     │    │(+ACP)│     │
                    │  └──┬──┘    └──┬──┘     │
                    └─────┼─────────┼─────────┘
                          │         │
              ┌───────────▼───┐ ┌───▼───────────┐
              │ Agent ↔ Tools │ │ Agent ↔ Agent  │
              │ Agent ↔ Data  │ │ Orchestration  │
              └───────────────┘ └────────────────┘
```

### Comparison Matrix

| Dimension | MCP | A2A/ACP | OpenAI GPT Actions |
|-----------|-----|---------|-------------------|
| **Purpose** | Connect AI to tools/data | Connect agents to agents | Connect ChatGPT to APIs |
| **Protocol** | JSON-RPC 2.0 | REST/HTTP | REST (OpenAPI) |
| **Transport** | Stdio + Streamable HTTP | HTTP + SSE | HTTPS |
| **Discovery** | Manual config | Agent Cards/Manifests | GPT Store |
| **State** | Stateful (sessions) | Stateful + Stateless | Conversation-scoped |
| **Multimodal** | Text + structured data | Full multimodal (MIME) | Text + files |
| **Governance** | Linux Foundation (AAIF) | Linux Foundation (AAIF) | OpenAI proprietary |
| **Vendor lock-in** | Low (open standard) | Low (open standard) | High (ChatGPT only) |
| **Maturity** | High (widely adopted) | Medium (growing) | High (but limited) |

### How They Complement Each Other

1. **MCP** = The plumbing. Connects any AI app to any tool/data source. Like USB-C.
2. **A2A** = The networking. Enables agents to discover and delegate to each other. Like TCP/IP.
3. **GPT Actions** = Vendor-specific. OpenAI's walled garden approach (now also supporting MCP).

**The winning stack for an agentic application**:
- Use **MCP** to connect your agent to tools and data
- Use **A2A** to let your agent collaborate with other agents
- Optionally publish as a **GPT** for ChatGPT distribution

---

## 7. The "Shopify for Agents" Opportunity

### What Exists Today

| Platform | What It Does | Limitations |
|----------|-------------|-------------|
| **OpenAI GPT Store** | Marketplace for Custom GPTs | No real monetization, no hosting, ChatGPT-only |
| **OpenAI Agent Builder** | Visual canvas for agent workflows | Locked to OpenAI ecosystem |
| **LangSmith Deployment** (née LangGraph Platform) | Deploy LangGraph agents with 1-click. Scaling, memory, checkpointing | Primarily for LangGraph framework users |
| **BeeAI Platform** | Discover, run, share ACP agents | Early stage, IBM-backed |
| **CrewAI** | Multi-agent orchestration framework | Framework, not a deployment platform |
| **AutoGen (Microsoft)** | Multi-agent conversation framework | Framework, not a marketplace |
| **Relevance AI** | No-code agent builder + deployment | Limited customization |
| **Voiceflow** | Voice/chat agent builder | Focused on conversational AI |
| **Botpress** | Chatbot builder platform | Traditional chatbot focus |
| **Stack AI** | No-code AI workflow builder | Workflow-focused, not agent marketplace |
| **Fixie.ai** | Agent hosting platform | Smaller scale |
| **Cloudflare** | MCP server hosting | Infrastructure, not marketplace |
| **Vercel AI SDK** | Framework for AI apps | SDK, not a platform |

### What's Missing — The Opportunity

**Nobody has built the full "Shopify for agents" stack:**

1. **Agent Marketplace with Real Economics**
   - List, discover, purchase/subscribe to agents
   - Revenue sharing / transaction fees (like Shopify's app store)
   - Ratings, reviews, usage analytics
   - Currently: GPT Store has no monetization. No other marketplace exists.

2. **One-Click Agent Deployment**
   - Deploy any agent (built with any framework) to production
   - Auto-scaling, monitoring, logging
   - Currently: LangSmith Deployment does this but only for LangGraph. No universal deployer.

3. **Agent Hosting with Protocol Support**
   - Host agents that speak MCP (as servers) AND A2A (as peers)
   - Currently: You self-host everything. Cloudflare offers MCP hosting but no agent runtime.

4. **Identity & Auth for Agents**
   - Agent identity, API keys, OAuth flows
   - User auth delegation (agent acts on behalf of user)
   - Currently: Each platform rolls its own.

5. **Billing & Metering**
   - Per-call, per-token, subscription billing for agent usage
   - Currently: Doesn't exist as a platform feature anywhere.

6. **Agent Composability**
   - Build agents that use other agents as building blocks
   - A2A makes this protocol-possible, but no platform makes it easy
   - Currently: Manual integration.

7. **Cross-Platform Distribution**
   - Publish once, distribute to ChatGPT, Claude, Gemini, custom apps
   - Currently: Build separate integrations for each.

### The Shopify Analogy Unpacked

| Shopify Feature | Agent Platform Equivalent | Exists? |
|----------------|--------------------------|---------|
| Store builder | Agent builder (visual/code) | Partial (OpenAI, Relevance AI) |
| Hosting | Agent runtime hosting | Partial (LangSmith) |
| Themes | Agent UI templates / ChatKit | Nascent |
| App store | Agent marketplace | GPT Store (weak) |
| Payments | Agent billing/metering | ❌ No |
| Analytics | Agent performance monitoring | Partial (LangSmith, OpenAI evals) |
| Shipping | Multi-platform distribution | ❌ No |
| Custom domains | Custom agent endpoints | ❌ No |
| Shopify API | Agent management API | ❌ No |

### Key Competitors / Adjacent Players to Watch

1. **OpenAI** — Agent Builder + ChatKit + Responses API. Most complete but walled garden.
2. **LangChain/LangSmith** — Best dev tools but framework-coupled.
3. **Anthropic** — MCP ecosystem leader. Claude as host. But no marketplace.
4. **Google** — A2A protocol + Vertex AI Agent Builder. Enterprise-focused.
5. **Microsoft** — Copilot Studio + Semantic Kernel + Azure. Enterprise.
6. **Salesforce** — Agentforce. CRM-specific agents.
7. **Amazon** — Bedrock Agents. AWS ecosystem.

### Strategic Insights for a Founder

**The wedge**: Start with **MCP server hosting + marketplace**. Reasons:
- MCP is the clear winner protocol (adopted by OpenAI, Google, Anthropic)
- Hundreds of MCP servers exist but have no hosted/managed option beyond self-hosting
- Developers want to *monetize* their MCP servers — nobody enables this today
- Natural expansion: MCP servers → full agents → agent marketplace → A2A orchestration

**Timing**: The December 2025 Linux Foundation move validates that MCP/A2A are THE standards. The market is ready for infrastructure.

**Moat potential**:
- Network effects (more agents → more users → more agents)
- Billing/metering infrastructure is hard to build
- Trust/identity layer for agents becomes critical as agents transact on users' behalf

---

## 8. OpenAI's Agent Platform Strategy

### The Full Picture

OpenAI is building an end-to-end agent platform. Here's every piece:

#### Models & Capabilities

| Model | Capability |
|-------|-----------|
| **GPT-4o** | Multimodal reasoning (text, vision, audio) |
| **o1 / o3** | Deep reasoning models |
| **CUA (Computer-Using Agent)** | GUI interaction via screenshots + mouse/keyboard. Powers Operator. |
| **GPT-4o Search** | Real-time web search integrated into model |

#### APIs

| API | Purpose | Status |
|-----|---------|--------|
| **Chat Completions** | Core LLM API | Stable, ongoing support |
| **Responses API** | New unified API for agentic apps with built-in tools | Production (replacing Assistants) |
| **Assistants API** | Managed conversations with tools | Deprecated mid-2026 |
| **Realtime API** | Voice + audio streaming | Production |

#### Built-in Tools (Responses API)

| Tool | What It Does | Pricing |
|------|-------------|---------|
| **Web Search** | Real-time web queries with citations | $25-30/1K queries |
| **File Search** | RAG over uploaded documents with vector stores | $2.50/1K queries |
| **Computer Use** | CUA model controls mouse/keyboard on screenshots | Per-token |
| **Code Interpreter** | Execute Python code | Coming to Responses API |
| **MCP Connectors** | Connect to third-party MCP servers | Standard rates |

#### Development Tools

| Tool | What It Does |
|------|-------------|
| **Agents SDK** (open-source) | Python framework for multi-agent orchestration. Agents, handoffs, guardrails, tracing. |
| **Agent Builder** | Visual canvas in OpenAI dashboard. Drag-and-drop agent design. |
| **ChatKit** | Embeddable chat UI for deploying agents in products |
| **MCP Registry** | Discover and connect MCP servers |

#### Evaluation & Monitoring

| Feature | Purpose |
|---------|---------|
| **Tracing** | Full execution traces for agent workflows |
| **Evals** | Automated evaluation of agent performance |
| **Trace Grading** | Automated trace-level quality scoring |
| **Prompt Optimizer** | Auto-improve prompts based on eval results |
| **Datasets** | Test data management for agent evals |

#### Consumer Products

| Product | Status |
|---------|--------|
| **ChatGPT** | Core consumer app |
| **Custom GPTs** | User-created ChatGPT variants |
| **GPT Store** | Marketplace for GPTs |
| **ChatGPT Agent Mode** (formerly Operator) | Browser-using agent integrated into ChatGPT. Uses CUA model. |

### Operator → ChatGPT Agent Deep Dive

**Operator** launched January 2025 as a standalone product at operator.chatgpt.com:
- Powered by **CUA (Computer-Using Agent)** model
- Combines GPT-4o vision + reinforcement learning for GUI interaction
- Sees the screen via screenshots, acts via mouse/keyboard
- Self-corrects using reasoning capabilities
- Hands control to user for sensitive tasks (login, payment, CAPTCHAs)

**Key safety features**:
- Takeover mode (user enters sensitive info directly)
- User confirmations before significant actions
- Task limitations (no banking, no high-stakes decisions)
- Watch mode on sensitive sites
- Monitor model for suspicious behavior
- Prompt injection detection

**Benchmarks**: WebArena 58.1% (SOTA), WebVoyager 87% (SOTA), OSWorld 38.1% (SOTA)

**July 2025**: Operator integrated into ChatGPT as "Agent Mode." Standalone site sunsetting.

**Partnerships**: DoorDash, Instacart, OpenTable, Priceline, StubHub, Thumbtack, Uber

### OpenAI's Strategic Vision

1. **Vertical integration**: Models → APIs → SDKs → Builder tools → Consumer app → Marketplace
2. **Platform play**: Agent Builder + ChatKit = "Build once, deploy everywhere (within OpenAI)"
3. **MCP adoption**: OpenAI adopted MCP (March 2025) + co-founded AAIF (Dec 2025) = embracing open standards while building proprietary layer on top
4. **Computer use as moat**: CUA model gives agents ability to use ANY software via GUI — no API needed
5. **ChatGPT as the OS**: Agent Mode turns ChatGPT into an "operating system" for digital tasks

### Implications for "Shopify for Agents" Builder

**OpenAI is your biggest competitor AND your biggest distribution channel.**

- They have the platform (Agent Builder, ChatKit) but it's **locked to their ecosystem**
- They adopted MCP — meaning **MCP servers you host can be used by OpenAI customers**
- Their agent marketplace (GPT Store) is **weak** — no real monetization, no cross-platform
- CUA/computer use can't be self-hosted — developers need OpenAI's API

**The gap**: OpenAI builds for developers who use *only* OpenAI. The "Shopify for agents" opportunity is for developers who want to **deploy agents that work across ALL platforms** (Claude, ChatGPT, Gemini, custom apps) with real business models (billing, analytics, distribution).

---

## Key Takeaways for a Founder

### 1. The Protocol Stack is Consolidating
- **MCP** (Anthropic → Linux Foundation) = agent↔tools/data. The clear winner.
- **A2A** (Google + IBM's ACP → Linux Foundation) = agent↔agent. Emerging standard.
- Both now under **AAIF (Linux Foundation)** with Anthropic, OpenAI, Block as co-founders.
- These are THE standards. Build on them.

### 2. The Infrastructure Gap is Massive
Nobody provides:
- Managed MCP server hosting with billing
- Agent marketplace with real economics
- Cross-platform agent distribution
- Agent identity/auth infrastructure
- Usage metering and analytics

### 3. Timing is Perfect
- MCP adopted by every major player (2025)
- Linux Foundation governance = industry consensus (Dec 2025)
- Agent Builder tools proliferating but no universal deployment platform
- Enterprise agent spending accelerating

### 4. Start with MCP Server Hosting
- Lowest barrier to entry
- Immediate value (developers have MCP servers, nowhere to host them)
- Natural expansion to full agent hosting and marketplace
- Protocol compliance = instant compatibility with Claude, ChatGPT, Gemini, Cursor, etc.

### 5. The "Shopify Moment" for Agents
Just as Shopify didn't compete with Amazon but enabled merchants to build their own stores, the opportunity is to enable developers to build, host, monetize, and distribute their own agents — without being locked into any single AI provider's ecosystem.

---

## 9. ANP — Agent Network Protocol

### Overview

| Attribute | Detail |
|-----------|--------|
| **Created by** | Open-source community (chgaowei), W3C Community Group submission |
| **Website** | [agent-network-protocol.com](https://agent-network-protocol.com/) |
| **GitHub** | [github.com/agent-network-protocol/AgentNetworkProtocol](https://github.com/agent-network-protocol/AgentNetworkProtocol) (1.7K+ stars) |
| **Goal** | "The HTTP of the Agentic Web era" — define how agents connect with each other |
| **ArXiv** | 2508.00007 (July 2025) |
| **Status** | Identity layer and meta-protocol substantially complete. Application layer in progress |

### Three-Layer Architecture

| Layer | Function | Details |
|-------|----------|---------|
| **Identity Layer** | Decentralized authentication | Based on W3C DID (Decentralized Identifiers). Provides end-to-end encrypted communication between agents |
| **Meta-Protocol Layer** | Dynamic protocol negotiation | Agents self-organize and self-negotiate communication protocols at runtime — no pre-agreed schemas needed |
| **Application Layer** | Capability description | Semantic-based capability description built on semantic web specifications |

### Key Differentiator

ANP is **fully decentralized** — no central platform dependency. Agents from ANY platform can authenticate each other using DIDs. This is a fundamentally different philosophy from MCP/A2A, which still assume some level of centralized discovery or orchestration.

### Vision

A shift from **platform-centric to protocol-centric** internet. In ANP's model, each agent is both a consumer and a service provider. Instead of agents being locked into platforms (OpenAI, Google, etc.), they communicate peer-to-peer through a shared protocol layer — much like how HTTP enabled the open web regardless of which server software you ran.

### Relevance to firo

If building "Shopify for agents," ANP could be the **networking layer** that enables agents deployed on firo to discover and communicate with agents on other platforms. While MCP handles tool integration and A2A handles orchestration, ANP addresses the fundamental question of *how agents find and authenticate each other across platform boundaries* — critical for a truly open agent marketplace.

---

## 10. AG-UI — Agent–User Interaction Protocol

### Overview

| Attribute | Detail |
|-----------|--------|
| **Created by** | CopilotKit (open source) |
| **Website** | [docs.ag-ui.com](https://docs.ag-ui.com/) |
| **What it is** | Open, lightweight, event-based protocol that standardizes how AI agents connect to user-facing applications |
| **Status** | Active development |

### The "Missing Link"

While **MCP** handles Agent↔Tools and **A2A** handles Agent↔Agent, **AG-UI** handles **Agent↔User** — the frontend connection that was previously undefined by any protocol. This completes what AG-UI's docs call the **three-protocol stack**:

| Protocol | Connection | Direction |
|----------|-----------|-----------|
| **AG-UI** | Agent ↔ User Interaction | Frontend connection |
| **MCP** | Agent ↔ Tools & Data | Backend connection |
| **A2A** | Agent ↔ Agent | Coordination |

### Building Blocks

- **Streaming chat** — Real-time bidirectional event streaming between agent and user interface
- **State management** — Agent state synchronization with frontend applications
- **Tool calls with human-in-the-loop** — Users can approve, modify, or reject agent tool calls in real-time
- **Generative UI** — Dynamic UI generation based on agent responses
- **Reasoning visibility** — Exposing agent reasoning/thinking to the user
- **Middleware** — Interceptors for logging, transformation, routing
- **Serialization** — Standardized event format across frameworks

### Framework Support

AG-UI supports multiple agent frameworks out of the box: **LangGraph, CrewAI, Mastra, AG2**, and others — meaning any agent built with these frameworks can connect to any AG-UI-compatible frontend.

### Important Distinction

**A2UI** (generative UI spec) is **different** from AG-UI:
- **A2UI** lets agents deliver UI widgets (the *what* of the interface)
- **AG-UI** is the transport protocol (the *how* of the connection)


## 12. OVON — Open Voice Network

### Overview

| Attribute | Detail |
|-----------|--------|
| **Created by** | Linux Foundation (Open Voice Network project) |
| **Focus** | Interoperability between voice-based AI agents and assistants |
| **Status** | Active within Linux Foundation, specs in development |

### Key Specifications

- **Universal API** for voice agent interoperability — standardizing how voice agents expose capabilities
- **Agent-to-agent delegation** for voice — defining how one voice agent hands off a conversation to another (e.g., Alexa delegating to a restaurant's booking agent)

### Why It Matters

As agents increasingly have voice interfaces (Alexa, Siri, Google Assistant, custom voice agents), OVON defines how voice agents **hand off between each other**. Without this, a user talking to Siri who needs a service from a different voice agent would hit a dead end — OVON enables seamless cross-platform voice delegation.

### Relevance to firo

If clients want **voice-based agents** (restaurant ordering, customer service hotlines, healthcare appointment scheduling), OVON compatibility could be a differentiator. A firo-hosted agent that speaks OVON can be delegated to by Alexa, Google Assistant, or any other OVON-compatible voice platform — expanding distribution beyond text/web interfaces.

---

## 13. Updated Protocol Landscape — Comprehensive Comparison

| Protocol | Layer | Creator | Focus | Status | Key Strength |
|----------|-------|---------|-------|--------|-------------|
| **MCP** | Agent ↔ Tools/Data | Anthropic → Linux Foundation | Connect agents to external systems | Production, massive adoption | De facto standard for tool integration |
| **A2A** | Agent ↔ Agent | Google → Linux Foundation | Agent collaboration | Production | Multi-agent orchestration |
| **ACP** | Commerce | OpenAI + Stripe | Agent payments/transactions | Early stage | Commerce-native, major backers |
| **UCP** | Commerce/Retail | Google + Shopify | Shopping/retail transactions | Early stage | Retail-specific, major backers |
| **AG-UI** | Agent ↔ User | CopilotKit | Frontend connection | Active development | Only protocol for agent-to-user |
| **ANP** | Networking | Open source community | Agent discovery & connection | Alpha | Decentralized, "HTTP for agents" |
| **OVON** | Voice | Linux Foundation | Voice agent interop | Specs in dev | Voice-specific handoffs |
| **ChatGPT Apps SDK** | Distribution | OpenAI | Apps inside ChatGPT | Production | Largest user base |
| **GPT Actions** | Integration | OpenAI | ChatGPT ↔ REST APIs | Production | Simple OpenAPI-based |

### The Complete Agentic Stack (Updated)

```
┌─────────────────────────────────────────────────────────────┐
│                    USER / FRONTEND                           │
│                      AG-UI (CopilotKit)                     │
│              Agent ↔ User Interaction Protocol               │
├─────────────────────────────────────────────────────────────┤
│                   DISTRIBUTION LAYER                         │
│    ChatGPT Apps SDK  │  GPT Actions  │  Voice (OVON)        │
├─────────────────────────────────────────────────────────────┤
│                  COLLABORATION LAYER                         │
│              A2A (Google → Linux Foundation)                 │
│              Agent ↔ Agent Communication                     │
├─────────────────────────────────────────────────────────────┤
│                    COMMERCE LAYER                            │
│         ACP (OpenAI + Stripe)  │  UCP (Google + Shopify)    │
│         Payments/Transactions  │  Retail/Shopping            │
├─────────────────────────────────────────────────────────────┤
│                   NETWORKING LAYER                           │
│                    ANP (Open Source)                         │
│        Decentralized Discovery & Authentication              │
├─────────────────────────────────────────────────────────────┤
│                     BASE LAYER                               │
│              MCP (Anthropic → Linux Foundation)              │
│              Agent ↔ Tools & Data (Universal)                │
└─────────────────────────────────────────────────────────────┘
```

### What This Means for firo

Firo's deployment/management platform sits **across all these layers**. A business deploying an agent through firo needs:
- **MCP** for connecting to their tools and data
- **ANP** for being discoverable by agents on other platforms
- **A2A** for collaborating with partner agents
- **ACP/UCP** for handling transactions
- **AG-UI** for presenting the agent to end-users
- **OVON** (optionally) for voice interfaces
- **ChatGPT Apps SDK / GPT Actions** for distribution through ChatGPT

The platform that makes all of this **simple to deploy and manage** wins.

*End of research document. Last updated: February 18, 2026.*
