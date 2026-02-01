---
name: aws-agentcore
description: Build AI agents with AWS Bedrock AgentCore. Use when developing agents on AWS infrastructure, creating tool-use patterns, implementing agent orchestration, or integrating with Bedrock models.
license: MIT
source: hoodini/ai-agents-skills
---

# AWS Bedrock AgentCore

Build production-grade AI agents on AWS infrastructure.

## Quick Start

```python
import boto3
from agentcore import Agent, Tool

@Tool(name="search_database", description="Search the product database")
def search_database(query: str, limit: int = 10) -> dict:
    return {"results": [...]}

agent = Agent(
    model_id="anthropic.claude-3-sonnet",
    tools=[search_database],
    instructions="You are a helpful product search assistant."
)

response = agent.invoke("Find laptops under $1000")
```

## AgentCore Components

| Component | Purpose |
|-----------|---------|
| **Runtime** | Serverless agent execution |
| **Gateway** | Convert APIs/Lambda to MCP-compatible tools |
| **Memory** | Multi-strategy memory (semantic, user preference) |
| **Identity** | Auth with Cognito, Okta, Google, EntraID |
| **Tools** | Code Interpreter, Browser Tool |

## Lambda Tool Integration

```python
def lambda_handler(event, context):
    action = event.get('actionGroup')
    function = event.get('function')
    parameters = event.get('parameters', [])
    
    params = {p['name']: p['value'] for p in parameters}
    
    if function == 'get_weather':
        result = get_weather(params['city'])
    
    return {
        'response': {
            'actionGroup': action,
            'function': function,
            'functionResponse': {
                'responseBody': {'TEXT': {'body': json.dumps(result)}}
            }
        }
    }
```

## Agent Orchestration

```python
from agentcore import SupervisorAgent, SubAgent

research_agent = SubAgent(
    name="researcher",
    model_id="anthropic.claude-3-sonnet",
    instructions="You research and gather information."
)

supervisor = SupervisorAgent(
    model_id="anthropic.claude-3-opus",
    sub_agents=[research_agent],
    routing_strategy="supervisor"
)
```

## Resources

- **Official Samples**: https://github.com/awslabs/amazon-bedrock-agentcore-samples
