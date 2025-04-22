# ChatGroq Agent for Action-Oriented Conversations

This project demonstrates a custom conversational agent built with LangChain and ChatGroq. The agent uses a chain-of-thought design where it alternates between generating thoughts, taking actions (via function calls), and processing observations until it arrives at a final answer. The available actions include querying Wikipedia, searching Simon Willison’s blog, and running mathematical calculations.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Setup and Configuration](#setup-and-configuration)
- [Workflow](#workflow)
- [Usage](#usage)

## Overview

The agent is designed to process natural language queries by:
- Generating a chain-of-thought reasoning.
- Iteratively calling tools for calculations, web searches, or blog queries.
- Incorporating observations from tool results to eventually produce a concise final answer.

Using a prompt template, the agent instructs the underlying LLM (ChatGroq using the `llama-3.3-70b-versatile` model) how to think, act, and observe. The agent loop terminates when the final answer is generated. 

## Features

- **Environment Configuration**: Uses a `.env` file to load API keys for TAVILY, Google, LangChain, and GROQ.
- **ChatGroq Integration**: Leverages ChatGroq for LLM interactions.
- **Custom Chatbot Class**: Implements a chatbot that stores conversation history and uses ChatGroq to process messages.
- **Action Parsing and Execution**: Uses regular expressions to parse actions (e.g., `calculate:`, `wikipedia:`, `simon_blog_search:`) from the agent's output and executes the corresponding function.
- **Custom Tools**:
  - `wikipedia`: Returns a Wikipedia snippet based on the search query.
  - `simon_blog_search`: Searches Simon Willison’s blog for relevant posts.
  - `calculate`: Evaluates simple mathematical expressions.

## Setup and Configuration

1. **Clone the Repository**

   ```bash
   git clone https://github.com/mybarefootstory/ReAct-Agent-from-scratch.git
   cd ReAct-Agent-from-scratch
   ```

2. **Set Up a Virtual Environment**

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use: venv\Scripts\activate
   ```

3. **Install Dependencies**

   Ensure you have the required Python packages installed. For example:

   ```bash
   pip install httpx python-dotenv langchain_groq
   ```

   (Include any additional dependencies mentioned in your `requirements.txt`.)

4. **Configure Environment Variables**

   Create a `.env` file in the project root with the following keys:

   ```env
   LANGCHAIN_API_KEY=your_langchain_api_key
   GROQ_API_KEY=your_groq_api_key
   ```

## Workflow

The agent follows a multi-step process:

1. **Environment Load**:  
   The API keys are loaded using Python’s `dotenv` library.

2. **LLM Initialization**:  
   Using the ChatGroq model (`llama-3.3-70b-versatile`), the agent invokes the LLM by sending a prompt that includes the initial system message and conversation history.

3. **Chatbot Class**:  
   A `Chatbot` class is defined to encapsulate the behavior of the agent:
   - It appends system, user, and assistant messages to a conversation history.
   - When the bot is called with a new message, it builds a prompt from the entire message history and invokes the ChatGroq LLM to generate a response.

4. **Prompt Design**:  
   The prompt instructs the agent to use a loop of "Thought", "Action", "PAUSE", and "Observation" phases. It also explains how available actions should be formatted within the response.

5. **Action Parsing and Execution**:  
   A regular expression (`action_re`) is used to parse the action and its parameter from the agent's response. Supported actions include:
   - **calculate**: Evaluates mathematical expressions.
   - **wikipedia**: Queries Wikipedia and returns a snippet.
   - **simon_blog_search**: Searches Simon’s blog for the given term.
  
6. **Query Execution**:  
   The `query` function simulates an interactive session:
   - It initiates a chatbot with the crafted prompt.
   - It loops until the agent produces an answer or reaches the maximum number of turns.
   - It prints intermediate results and observations.

## Usage

Run the notebook or script to start the agent and test queries. For example, running:

```python
print(query("Tell me what simon blog has written about AI."))
```

The agent will process the query by:
- Generating a thought (e.g., deciding to search Simon's blog),
- Executing the `simon_blog_search` action,
- Processing the observation,
- And finally returning an answer summarizing the blog post content.

### Example Output

```
Thought: To find out what Simon's blog has written about AI, I should search his blog for the term "AI".
Action: simon_blog_search: AI
PAUSE
 -- running simon_blog_search AI
Observation: It's OK to call it Artificial Intelligence: Update 9th January 2024: ...
Answer: Simon's blog has written about the importance of having conversations about AI, its risks and benefits, and the need for an agreed-upon term...
```



