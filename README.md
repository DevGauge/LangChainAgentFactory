# README - LangchainAgentFactory

LangchainAgentFactory is a wrapper around the powerful LangChain library that provides a user-friendly interface to generate various types of agents based on Open AI's GPT models. This library leverages LangChain's robust features, providing an extra layer of abstraction that simplifies the process of creating conversational AI agents with powerful tools like google search and sending emails.

## Features

- Simplifies the use of the powerful LangChain framework.
- Generate various types of agents with custom toolsets.
- Supports conversation history tracking with the LangChain memory buffer.
- Allows for customizing the 'creativity' (temperature) of the chatbot and the maximum token limit.
  
## Prerequisites

Ensure Python 3.8+ is installed on your system.

## Installation

Install LangChainAgentFactory via pip:

```bash
pip install LangChainAgentFactory
```
Pip will automatically handle the installation of dependencies.

## Set Up Required Environment Variables

To use `LangChainAgentFactory`, you need to set up your OpenAI API Key in your environment variables:

#### Windows
- Powershell:

  ```powershell
  $env:OPENAI_API_KEY = 'YOUR_OPENAI_API_KEY'
  ```

- Command Prompt:

  ```cmd
  set OPENAI_API_KEY=YOUR_OPENAI_API_KEY
  ```

#### MacOS

- Terminal:

  ```bash
  export OPENAI_API_KEY=YOUR_OPENAI_API_KEY
  ```

## Usage

For all agents, import dependencies:
```python
from LangchainAgentFactory import AgentFactory
```

### Chat Agent With Memory

Create an instance of LangchainAgentFactory, then call the `agent` method to create an agent. You can specify the type of agent you want and whether you want verbose output:

*Note*: This will only create a chat bot with no access to tools
```python
factory = AgentFactory()
agent = factory.agent()

# converse with the agent
user_input = input()
agent.run(user_input)
```

### Chat Agent With Memory and Email Sending Function

The LangchainAgentFactory allows the creation of an AI agent that can use various tools, where a tool is essentially a function. These functions can either have a single text parameter or multiple parameters. If you have multiple tools, the agent can chain them together. Here's an example demonstrating how to create a tool and use it in your agent.

First, we define a function with a doc string that describes the parameters and what the function does. The function must return a 
string.

As an example, we'll implement `send_email` that sends an email using SMTP and ttls:

```python
import os
import smtplib
import ssl
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from LangChainAgentFactory import AgentFactory

def send_email(subject, message, to_email):
    """Send an email to the specified email address with the specified subject and message."""
    from_email = os.getenv("EMAIL_USER")
    password = os.getenv("EMAIL_PASSWORD")
    smtp_server = os.getenv("EMAIL_SMTP_SERVER")

    msg = MIMEMultipart()
    msg['From'] = from_email
    msg['To'] = to_email
    msg['Subject'] = subject
    msg.attach(MIMEText(message, 'plain'))    

    smtp_server = "smtp.hostinger.com"
    smtp_port = 587

    server = None
    context = ssl.create_default_context()
    try:
        server = smtplib.SMTP(smtp_server, smtp_port, timeout=60)
        server.starttls(context=context)
        server.login(from_email, password)
        text = msg.as_string()
        server.sendmail(from_email, to_email, text)
    except smtplib.SMTPException as e:
        return f"Failed to send email due to SMTP Exception: {str(e)}"
    except Exception as e:
        return f"An unexpected error occurred while sending the email: {str(e)}"
    finally:
        if server is not None:
            server.quit()

    return f"Success! The message was sent to {to_email}" # Note the return is in natural language. This message is what the LLM receives as an indicator of what happened, not necessarily what the LLM will respond with.
```

Now, we use `StructuredTool.from_function` to create a tool from the `send_email` function and create a tools array with it:

```python
from langchain.agents import StructuredTool # used for multi-parameter tools. You can use `Tool` for single-parameter tools

tools = [
    StructuredTool.from_function(send_email)
]
```

Finally, we pass this tools array to the LangchainAgentFactory and create an agent:

```python
agentFactory = AgentFactory(tools=tools)
agent = agentFactory.agent()
```

Now, your agent has the ability to send emails!

```python
user_input = "Send an email to mark.smith@example.com informing him of what you can do."
agent.run(user_input)
```
![Send Email using a natural language query](send_email.png)

If you add multiple tools and want to verify what the agent has access to, simply ask!

```python
user_input = "What tools do you have access to?"
agent.run(user_input)
```
![What Functions Can You Access?](what_functions.png)

## License

This project is licensed under the terms of the MIT license. See the [LICENSE](LICENSE.txt) file for details.

## Support

For support or any questions, raise a GitHub Issue.