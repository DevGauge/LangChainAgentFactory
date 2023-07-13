# LangChainAgentFactory
LangChainAgentFactory is a Python package that provides a wrapper for LangChain, facilitating the creation of AI Agents with existing Large Language Models (LLMs).

## Installation
Install LangChainAgentFactory via pip:

```bash
pip install LangChainAgentFactory
```

## Usage
To create a basic chatbot with LangChainAgentFactory:
1. set the environment variable OPENAI_API_KEY='YOUR_OPENAI_API_KEY'

### Windows
powershell: $env:OPENAI_API_KEY = 'YOUR_OPENAI_API_KEY'

command prompt: set OPENAI_API_KEY=YOUR_OPENAI_API_KEY

### MacOS
OPENAI_API_KEY=YOUR_OPENAI_API_KEY

2. Create a summarizer, chatbot, summarize a conversation, and count tokens
```python
from LangChainAgentFactory import LangChainAgentFactory
# a separate agent used to summarize conversations
    # tracks all prompts and responses and continuously summarizes
summarizer = ConversationSummarizer()
while True:
    prompt = input('How can I help you today?')
    # count tokens, summarize the conversation up until now, and respond to a user's query:
    summarizer.summarize(prompt)

```

Detailed usage instructions coming soon:
- use ConversationSummaryBufferMemory to better manage token usage and conversation summarization
- create your own custom agents with few shot examples to handle queries
before summarization
  - limit the length of example prompts based on current conversation token usage

Upcoming features:
- function calling (give ChatGPT access to call methods in order to perform tasks such as web searches and sending emails)

## License
LangChainAgentFactory is released under the [MIT License](./LICENSE.txt).