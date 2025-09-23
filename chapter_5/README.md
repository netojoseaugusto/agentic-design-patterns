# Chapter 5: Tool Use in Agentic Design Patterns

This chapter demonstrates how to implement tool use patterns in agentic AI systems using two popular frameworks: CrewAI and LangChain. Tool use allows AI agents to interact with external systems, perform specific functions, and access real-time data to enhance their capabilities.

## Overview

Tool use is a fundamental pattern in agentic AI where agents can:

- Execute specific functions with defined inputs and outputs
- Access external APIs and data sources
- Perform complex operations that require specialized tools
- Handle errors and edge cases gracefully
- Chain multiple tool calls together for complex workflows

## Files in this Chapter

### 1. `tool_use_crewai.py` - CrewAI Implementation

This file demonstrates tool use using the CrewAI framework, which provides a structured approach to building multi-agent systems with specialized tools.

#### Key Components:

**Tool Definition:**

```python
@tool("Stock Price Lookup Tool")
def get_stock_price(ticker: str) -> float:
    """
    Fetches the latest simulated stock price for a given stock ticker symbol.
    Returns the price as a float. Raises a ValueError if the ticker is not found.
    """
```

**Features:**

- **Clean Data Return**: Returns raw data (float) instead of formatted strings
- **Error Handling**: Raises specific exceptions for invalid inputs
- **Logging**: Comprehensive logging for debugging and monitoring
- **Type Safety**: Proper type hints for better code maintainability

**Agent Configuration:**

```python
financial_analyst_agent = Agent(
    role='Senior Financial Analyst',
    goal='Analyze stock data using provided tools and report key prices.',
    backstory="You are an experienced financial analyst...",
    verbose=True,
    tools=[get_stock_price],
    allow_delegation=False,
)
```

**Task Definition:**

- Clear, specific instructions for the agent
- Expected output format specification
- Error handling guidance
- Agent assignment

**Crew Orchestration:**

- Combines agents and tasks
- Manages execution flow
- Provides verbose logging
- Handles API key validation

#### Best Practices Demonstrated:

1. **Environment Variable Management**: Secure API key handling
2. **Error Handling**: Proper exception raising and handling
3. **Logging**: Comprehensive logging for debugging
4. **Type Safety**: Type hints throughout the code
5. **Main Function Pattern**: Using `if __name__ == "__main__"` for proper execution
6. **Documentation**: Clear docstrings and comments

### 2. `tool_use_langchain.py` - LangChain Implementation

This file demonstrates tool use using the LangChain framework, which provides a more flexible and modular approach to building AI applications.

#### Key Components:

**Tool Definition:**

```python
@langchain_tool
def search_information(query: str) -> str:
    """
    Provides factual information on a given topic. Use this tool to find answers to phrases
    like 'capital of France' or 'weather in London?'.
    """
```

**Features:**

- **Simulated Data Source**: Dictionary-based simulated search results
- **Flexible Query Handling**: Supports various types of information queries
- **Default Fallback**: Graceful handling of unknown queries
- **Async Support**: Built-in async/await pattern for concurrent execution

**Agent Creation:**

```python
agent = create_tool_calling_agent(llm, tools, agent_prompt)
agent_executor = AgentExecutor(agent=agent, verbose=True, tools=tools)
```

**Concurrent Execution:**

- Multiple queries run simultaneously using `asyncio.gather()`
- Efficient resource utilization
- Parallel processing capabilities

#### Best Practices Demonstrated:

1. **Async Programming**: Proper async/await implementation
2. **Error Handling**: Try-catch blocks for robust error management
3. **Environment Management**: Using `dotenv` for configuration
4. **Concurrent Processing**: Running multiple operations in parallel
5. **Modular Design**: Separating concerns into distinct functions
6. **Logging**: Clear output formatting for debugging

## Key Differences Between Approaches

| Aspect               | CrewAI                        | LangChain                          |
| -------------------- | ----------------------------- | ---------------------------------- |
| **Architecture**     | Multi-agent crew-based        | Single agent with tools            |
| **Execution Model**  | Sequential task execution     | Concurrent execution               |
| **Tool Integration** | Built-in tool decorator       | LangChain tool decorator           |
| **Error Handling**   | Exception-based               | Try-catch blocks                   |
| **Logging**          | Built-in verbose mode         | Custom logging implementation      |
| **Concurrency**      | Sequential by default         | Native async support               |
| **Use Case**         | Complex multi-agent workflows | Flexible single-agent applications |

## Common Patterns

### 1. Tool Definition Pattern

Both implementations follow a similar pattern for defining tools:

- Decorator-based tool registration
- Clear input/output type hints
- Comprehensive docstrings
- Error handling within tools

### 2. Agent Configuration Pattern

- Role-based agent definition
- Tool assignment
- Clear goals and backstories
- Verbose logging for debugging

### 3. Error Handling Pattern

- Graceful error handling
- Specific exception types
- User-friendly error messages
- Logging for debugging

### 4. Execution Pattern

- Main function for entry point
- Environment validation
- Clear output formatting
- Proper resource cleanup

## Usage Examples

### Running CrewAI Implementation:

```bash
# Set your API key
export OPENAI_API_KEY="your-api-key-here"

# Run the script
python tool_use_crewai.py
```

### Running LangChain Implementation:

```bash
# Set your API key in .env file
echo "OPENAI_API_KEY=your-api-key-here" > .env

# Run the script
python tool_use_langchain.py
```

## Dependencies

Both implementations require:

- Python 3.8+
- OpenAI API key
- Framework-specific dependencies (CrewAI or LangChain)

See `pyproject.toml` for specific version requirements.

## Best Practices for Tool Use

1. **Tool Design**: Keep tools focused and single-purpose
2. **Error Handling**: Always handle errors gracefully
3. **Type Safety**: Use type hints for better code quality
4. **Documentation**: Provide clear docstrings for tools
5. **Testing**: Test tools independently before integration
6. **Logging**: Implement comprehensive logging for debugging
7. **Security**: Handle sensitive data securely
8. **Performance**: Consider caching and optimization for expensive operations

## Conclusion

Tool use is a powerful pattern that enables AI agents to interact with external systems and perform complex operations. The choice between CrewAI and LangChain depends on your specific requirements:

- **Choose CrewAI** for complex multi-agent workflows with clear role separation
- **Choose LangChain** for flexible single-agent applications with concurrent execution needs

Both frameworks provide robust tool use capabilities, and the patterns demonstrated here can be adapted to various use cases and requirements.
