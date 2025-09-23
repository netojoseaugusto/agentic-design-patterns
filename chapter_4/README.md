# Chapter 4: Reflection Pattern

## Overview

This chapter demonstrates the **Reflection Pattern** in agentic AI systems, where an AI agent iteratively improves its own output through self-critique and refinement. The reflection pattern is a powerful technique that enables AI systems to achieve higher quality results by having them act as both generator and critic.

## The Reflection Pattern

The reflection pattern consists of three main stages that repeat in a loop:

1. **Generate/Refine Stage**: The AI generates initial code or refines existing code based on previous critiques
2. **Reflect Stage**: A separate "reflector" agent critically evaluates the generated code
3. **Stopping Condition**: The loop continues until the code is deemed perfect or a maximum iteration limit is reached

## Implementation: `reflection.py`

### Core Components

#### 1. Configuration and Setup

```python
# Load environment variables and initialize the LLM
load_dotenv()
llm = ChatOpenAI(model="gpt-4o", temperature=0.1)
```

The implementation uses GPT-4o with a low temperature (0.1) for more deterministic and consistent outputs during the reflection process.

#### 2. The Reflection Loop Function

The `run_reflection_loop()` function demonstrates a complete reflection cycle:

**Task Definition**: Create a Python function `calculate_factorial` that:

- Accepts a single integer `n` as input
- Calculates its factorial (n!)
- Includes proper documentation
- Handles edge cases (factorial of 0 is 1)
- Validates input (raises ValueError for negative numbers)

**Iteration Process**:

- **Maximum 3 iterations** to prevent infinite loops
- **Message history tracking** to maintain context across iterations
- **Progressive refinement** based on critiques

#### 3. Three-Stage Process

**Stage 1: Generate/Refine**

- First iteration: Generates initial code from the task prompt
- Subsequent iterations: Refines code based on previous critiques
- Maintains conversation history for context

**Stage 2: Reflect**

- Uses a specialized "reflector" prompt that positions the AI as a senior software engineer
- Performs meticulous code review focusing on:
  - Bugs and logical errors
  - Code style and best practices
  - Missing edge cases
  - Areas for improvement
- Returns either detailed critiques or "CODE_IS_PERFECT" if satisfactory

**Stage 3: Stopping Condition**

- Checks if the critique contains "CODE_IS_PERFECT"
- If perfect, exits the loop
- Otherwise, adds critique to message history for next iteration

### Key Features

#### 1. **Dual Agent Architecture**

- **Generator Agent**: Creates and refines code
- **Reflector Agent**: Critically evaluates the code
- Both agents use the same LLM but with different prompts and roles

#### 2. **Context Preservation**

- Maintains complete conversation history across iterations
- Each iteration builds upon previous critiques and refinements
- Enables progressive improvement rather than starting from scratch

#### 3. **Structured Critique System**

- Uses a specialized system prompt for the reflector
- Provides clear stopping criteria ("CODE_IS_PERFECT")
- Ensures consistent and thorough evaluation

#### 4. **Iteration Control**

- Maximum iteration limit prevents infinite loops
- Early termination when code quality is satisfactory
- Clear progress tracking with iteration numbers

### Example Output

The reflection loop typically produces output like:

```
========================= REFLECTION LOOP: ITERATION 1 =========================

>>> STAGE 1: GENERATING initial code...

--- Generated Code (v1) ---
def calculate_factorial(n):
    # ... initial implementation ...

>>> STAGE 2: REFLECTING on the generated code...

--- Critique ---
• Missing input validation for negative numbers
• No docstring provided
• Edge case for n=0 not explicitly handled
...

========================= REFLECTION LOOP: ITERATION 2 =========================

>>> STAGE 1: REFINING code based on previous critique...

--- Generated Code (v2) ---
def calculate_factorial(n):
    """
    Calculate the factorial of a non-negative integer.
    ...
    """
    # ... improved implementation ...
```

### Benefits of the Reflection Pattern

1. **Self-Improvement**: AI systems can iteratively improve their own outputs
2. **Quality Assurance**: Built-in critique mechanism ensures higher quality results
3. **Error Reduction**: Multiple iterations catch and fix issues that might be missed in single-pass generation
4. **Adaptability**: Can be applied to various tasks beyond code generation
5. **Transparency**: Clear visibility into the improvement process

### Use Cases

- **Code Generation and Review**: Automated code creation with quality assurance
- **Content Creation**: Iterative improvement of written content
- **Problem Solving**: Multi-step reasoning and solution refinement
- **Creative Tasks**: Iterative enhancement of creative outputs
- **Quality Control**: Automated quality assessment and improvement

### Running the Example

1. Ensure you have a `.env` file with your `OPENAI_API_KEY`
2. Install dependencies: `uv sync`
3. Run the reflection loop: `python reflection.py`

The script will demonstrate the complete reflection process, showing how the AI agent progressively improves the factorial function through self-critique and refinement.

## Key Takeaways

The reflection pattern represents a significant advancement in AI capabilities, enabling systems to not just generate content but to critically evaluate and improve it. This pattern is particularly valuable for tasks requiring high quality, accuracy, and attention to detail, as it combines the generative capabilities of AI with the critical thinking necessary for quality assurance.
