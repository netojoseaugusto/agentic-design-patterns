# Chapter 2: Agent Routing Patterns

This chapter demonstrates two different approaches to implementing agent routing patterns - a fundamental concept in multi-agent systems where a coordinator agent intelligently delegates tasks to specialized sub-agents.

## Overview

Both implementations solve the same problem: routing user requests to appropriate specialist agents based on the request content. However, they use different frameworks and approaches:

- **`routing-adk.py`**: Uses Google's Agent Development Kit (ADK) with Auto-Flow delegation
- **`routing-langchain.py`**: Uses LangChain with explicit routing logic and RunnableBranch

## Architecture

### Core Components

Both implementations share a similar architecture:

1. **Coordinator Agent**: Main agent that analyzes incoming requests and decides which specialist to delegate to
2. **Specialist Agents**:
   - **Booking Agent**: Handles flight and hotel booking requests
   - **Info Agent**: Handles general information and question-answering
3. **Tool Functions**: Simulated handlers that represent the actual work done by specialist agents

### Request Flow

```
User Request → Coordinator Agent → Decision Logic → Specialist Agent → Tool Function → Response
```

## Implementation Details

### routing-adk.py (Google ADK Approach)

**Key Features:**

- Uses Google's Agent Development Kit (ADK) framework
- Leverages Auto-Flow delegation through `sub_agents` parameter
- Built-in session management with `InMemoryRunner`
- Asynchronous execution model

**Architecture:**

```python
# Coordinator with sub-agents for automatic delegation
coordinator = Agent(
    name="Coordinator",
    model="gemini-2.0-flash",
    instruction="""You are the main coordinator. Your only task is to analyze
       incoming user requests and delegate them to the appropriate specialist agent.""",
    sub_agents=[booking_agent, info_agent]  # Auto-Flow delegation
)
```

**Delegation Logic:**

- The coordinator uses natural language instructions to determine routing
- ADK automatically handles the delegation based on the coordinator's decision
- No explicit routing code needed - the framework manages the flow

**Session Management:**

- Uses `InMemoryRunner` for session handling
- Creates unique session IDs for each request
- Manages user context and conversation state

### routing-langchain.py (LangChain Approach)

**Key Features:**

- Uses LangChain's `RunnableBranch` for explicit routing
- Custom prompt-based decision making
- Synchronous execution model
- More granular control over routing logic

**Architecture:**

```python
# Explicit routing chain with RunnableBranch
coordinator_agent = {
    "decision": coordinator_router_chain,
    "request": RunnablePassthrough()
} | delegation_branch | (lambda x: x['output'])
```

**Delegation Logic:**

- Uses a structured prompt to classify requests into categories
- `RunnableBranch` routes based on the classification result
- Explicit mapping between decision outputs and handler functions

**Routing Categories:**

- `booker`: Flight and hotel booking requests
- `info`: General information and question-answering
- `unclear`: Requests that don't fit either category

## Key Differences

| Aspect                 | ADK Approach                  | LangChain Approach              |
| ---------------------- | ----------------------------- | ------------------------------- |
| **Delegation**         | Auto-Flow (implicit)          | Explicit routing logic          |
| **Execution**          | Asynchronous                  | Synchronous                     |
| **Session Management** | Built-in with InMemoryRunner  | Manual handling                 |
| **Control**            | High-level, framework-managed | Low-level, developer-controlled |
| **Complexity**         | Simpler setup                 | More explicit configuration     |
| **Flexibility**        | Limited to ADK patterns       | Highly customizable             |

## Usage Examples

### ADK Example

```python
# The coordinator automatically delegates based on its analysis
result = await run_coordinator(runner, "Book me a hotel in Paris.")
# → Routes to booking_agent → booking_handler()
```

### LangChain Example

```python
# Explicit routing through RunnableBranch
result = coordinator_agent.invoke({"request": "What is the capital of Italy?"})
# → Routes through decision chain → info_handler()
```

## Request Classification

Both implementations classify requests into the same categories:

### Booking Requests

- "Book me a hotel in Paris"
- "Find flights to Tokyo next month"
- "Reserve a room for 2 nights"

### Information Requests

- "What is the highest mountain in the world?"
- "Tell me about Paris attractions"
- "What's the weather like in London?"

### Unclear Requests

- "Tell me about quantum physics"
- "Help me with my homework"
- Vague or unrelated requests

## Dependencies

### ADK Implementation

```toml
[project]
dependencies = [
    "google-adk",
    "google-genai",
    "nest-asyncio"
]
```

### LangChain Implementation

```toml
[project]
dependencies = [
    "langchain-openai",
    "langchain-core"
]
```

## Running the Examples

### ADK Version

```bash
python routing-adk.py
```

**Note**: Requires Google ADK authentication and API access.

### LangChain Version

```bash
python routing-langchain.py
```

**Note**: Requires OpenAI API key for the LLM.

## Benefits of Each Approach

### ADK Benefits

- **Simplicity**: Less code required for basic routing
- **Built-in Features**: Session management, error handling
- **Auto-Flow**: Framework handles delegation automatically
- **Production Ready**: Designed for enterprise use cases

### LangChain Benefits

- **Flexibility**: Complete control over routing logic
- **Transparency**: Explicit flow that's easy to debug
- **Customization**: Easy to add complex routing rules
- **Framework Agnostic**: Can work with different LLM providers

## When to Use Each Approach

### Use ADK When:

- You need quick prototyping of agent systems
- You want built-in session management
- You're working within Google's ecosystem
- You prefer high-level abstractions

### Use LangChain When:

- You need fine-grained control over routing
- You're building complex multi-step workflows
- You want to integrate with multiple LLM providers
- You need custom business logic in routing decisions

## Conclusion

Both implementations demonstrate effective agent routing patterns, but they serve different use cases. The ADK approach is ideal for rapid development and production systems, while the LangChain approach provides more control and flexibility for complex routing scenarios. The choice between them depends on your specific requirements, existing infrastructure, and desired level of control over the routing logic.
