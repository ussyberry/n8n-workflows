# Agent Guidelines for n8n Workflow Development

This document provides instructions for AI agents working on n8n workflows in this repository. The goal is to ensure consistency, quality, and adherence to best practices.

## 1. Use Native n8n Nodes

**Always use native n8n nodes.** Do not create custom nodes. The n8n ecosystem is extensive, and workflows should be built using the standard, community-tested components.

When building AI-powered workflows, prioritize the use of the official LangChain nodes provided by n8n. These are typically found under the `@n8n/n8n-nodes-langchain` namespace.

-   **Chat Trigger:** `@n8n/n8n-nodes-langchain.chatTrigger`
-   **AI Agent:** `@n8n/n8n-nodes-langchain.agent`
-   **Language Models (LLMs):** Use the appropriate native node for the desired service (e.g., `@n8n/n8n-nodes-langchain.lmChatOpenRouter` for OpenRouter).
-   **Memory:** Use a native memory node like `@n8n/n8n-nodes-langchain.memoryBufferWindow`.

## 2. Correctly Structure AI Agent Connections

AI Agent workflows require a specific connection structure in the workflow's JSON definition. Unlike standard nodes, which are connected via the `main` output, AI components (like language models and memory) are connected *to* the AI Agent node via specialized inputs.

-   The language model node must be connected to the AI Agent node with a connection type of `ai_languageModel`.
-   The memory node must be connected to the AI Agent node with a connection type of `ai_memory`.

**Example `connections` block:**

```json
"connections": {
  "When chat message received": {
    "main": [
      [
        {
          "node": "AI Agent",
          "type": "main",
          "index": 0
        }
      ]
    ]
  },
  "OpenRouter Chat Model": {
    "ai_languageModel": [
      [
        {
          "node": "AI Agent",
          "type": "ai_languageModel",
          "index": 0
        }
      ]
    ]
  },
  "Simple Memory": {
    "ai_memory": [
      [
        {
          "node": "AI Agent",
          "type": "ai_memory",
          "index": 0
        }
      ]
    ]
  }
}
```

## 3. Optimize Workflows for Performance and Clarity

Creating a functional workflow is the first step. Optimizing it is the next.

### System Messages

The effectiveness of an AI Agent is heavily dependent on its system message. Do not use simple, generic placeholders. A high-quality system message should:

-   **Define a Clear Persona:** Describe who the AI is and what its role is.
-   **State the Purpose:** Clearly outline the AI's primary goal.
-   **Provide Core Directives:** Give a structured list of rules and guidelines for the AI's behavior, tone, and response format.
-   **Admit Limitations:** Instruct the AI to be honest when it cannot fulfill a request.

### Expose Model Parameters

When configuring language model nodes (e.g., OpenRouter), expose key parameters in the `options` object to allow for easy tuning. This makes the workflow more flexible and adaptable. Key parameters to include are:

-   `temperature`
-   `topP`
-   `maxTokens`
-   `frequencyPenalty`
-   `presencePenalty`

By following these guidelines, you will create n8n workflows that are robust, maintainable, and effective.
