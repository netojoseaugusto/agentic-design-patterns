# Prompt Chaining with LangChain

This example demonstrates the **Prompt Chaining** pattern using LangChain, where multiple prompts are connected in sequence to create a sophisticated data processing pipeline.

## Overview

The `prompt-chaining.py` file implements a two-stage prompt chain that:

1. **Extracts** technical specifications from unstructured text
2. **Transforms** the extracted specifications into a structured JSON format

## Architecture

The system uses LangChain's chain composition to create a pipeline where the output of one prompt becomes the input for the next.

## Code Breakdown

### Dependencies

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
```

### Components

#### 1. Language Model

```python
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
```

- Uses OpenAI's GPT-4o-mini model
- Temperature set to 0 for deterministic outputs

#### 2. First Prompt (Extraction)

```python
prompt_extract = ChatPromptTemplate.from_template(
   "Extract the technical specifications from the following text:\n\n{text_input}"
)
```

- **Purpose**: Identifies and extracts technical specifications from unstructured text
- **Input**: Raw text containing technical details
- **Output**: Extracted specifications in natural language

#### 3. Second Prompt (Transformation)

```python
prompt_transform = ChatPromptTemplate.from_template(
   "Transform the following specifications into a JSON object with 'cpu', 'memory', and 'storage' as keys:\n\n{specifications}"
)
```

- **Purpose**: Converts extracted specifications into structured JSON
- **Input**: Specifications from the first prompt
- **Output**: JSON object with specific keys

#### 4. Output Parser

```python
StrOutputParser()
```

- **Purpose**: Converts LangChain's `AIMessage` objects to plain strings
- **Why needed**: Makes output chainable and human-readable

## Chain Construction

### Extraction Chain

```python
extraction_chain = prompt_extract | llm | StrOutputParser()
```

This creates a simple chain that:

1. Takes text input
2. Extracts specifications using the first prompt
3. Converts output to string format

### Full Chain

```python
full_chain = (
   {"specifications": extraction_chain}
   | prompt_transform
   | llm
   | StrOutputParser()
)
```

This creates a complex chain that:

1. Uses the extraction chain as input for specifications
2. Transforms specifications to JSON
3. Converts final output to string

## Data Flow Diagram

```mermaid
graph TD
    A[Input Text: "3.5 GHz octa-core processor, 16GB RAM, 1TB NVMe SSD"] --> B[Prompt 1: Extract Specifications]
    B --> C[LLM Processing]
    C --> D[StrOutputParser]
    D --> E[Extracted Specifications: "CPU: 3.5 GHz octa-core, Memory: 16GB, Storage: 1TB NVMe SSD"]
    E --> F[Prompt 2: Transform to JSON]
    F --> G[LLM Processing]
    G --> H[StrOutputParser]
    H --> I[Final JSON Output: {"cpu": "3.5 GHz octa-core", "memory": "16GB", "storage": "1TB NVMe SSD"}]
```

## Chain Architecture Diagram

```mermaid
graph LR
    subgraph "Extraction Chain"
        A[prompt_extract] --> B[llm] --> C[StrOutputParser]
    end

    subgraph "Full Chain"
        D[{"specifications": extraction_chain}] --> E[prompt_transform] --> F[llm] --> G[StrOutputParser]
    end

    A -.-> D
    C -.-> D
```

## Execution Flow

1. **Input Processing**: Raw text is passed to the full chain
2. **First Stage**:
   - Text goes through `prompt_extract`
   - LLM extracts technical specifications
   - `StrOutputParser` converts to string
3. **Second Stage**:
   - Extracted specifications become input for `prompt_transform`
   - LLM transforms to JSON format
   - `StrOutputParser` converts final output to string
4. **Result**: Structured JSON with CPU, memory, and storage specifications

## Example Output

**Input:**

```
"The new laptop model features a 3.5 GHz octa-core processor, 16GB of RAM, and a 1TB NVMe SSD."
```

**Output:**

```json
{
  "cpu": "3.5 GHz octa-core",
  "memory": "16GB",
  "storage": "1TB NVMe SSD"
}
```

## Key Benefits

1. **Modularity**: Each prompt has a single, clear responsibility
2. **Reusability**: The extraction chain can be used independently
3. **Composability**: Chains can be combined in different ways
4. **Maintainability**: Easy to modify individual prompts without affecting others
5. **Type Safety**: Output parsers ensure consistent data types

## Use Cases

This pattern is ideal for:

- **Data Extraction**: Pulling structured data from unstructured text
- **Data Transformation**: Converting between different formats
- **Multi-step Processing**: Breaking complex tasks into manageable steps
- **Pipeline Construction**: Building reusable data processing workflows
