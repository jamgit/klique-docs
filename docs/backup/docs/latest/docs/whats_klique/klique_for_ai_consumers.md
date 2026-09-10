# Klique for AI Consumers

> Klique overview for AI Consumers, covering deploying models, launching agentic environments, and using published applications.

AI Consumers use AI that others have set up, without building it themselves. They are often tech-savvy but not
developers: people across the organization who want to get value from AI on enterprise compute and data, without
managing infrastructure, API keys, or model providers. Working in the AI Consumer view of the WebApp, an AI Consumer
picks what they need and launches it in a few clicks. Everything runs on enterprise compute, under the consumer's own
identity and the permissions their role carries, inside the boundaries AI Admins set.

![GenAI engine](../img/gif/ai_consumer.gif#light-mode-only)
![GenAI engine](../img/gif/ai_consumer_dark.gif#dark-mode-only)

## Deploy AI Models

AI Consumers can deploy LLMs on managed compute and use it through a chat interface or via APIs.
These can be used running local chat interfaces, powering local agentic workflows or run local transcription models
See [One-click Inference Service](../deploying_models.md).

## Launch agentic environments

AI Consumers can run the agentic tools they already use, such as Claude Desktop, Claude Code, Codex, and ChatGPT,
connected to governed enterprise endpoints instead of personal keys, so their work stays on approved models and inside
organizational boundaries. See [Desktop Agentic Applications](../ai_engine/desktop_agentic_applications.md).

![Claude code](../img/apps_claude_code.png)

## Use custom-built applications

AI Consumers can use organizational applications that AI Builders have published.
Each application runs on enterprise compute with access to the data or configurations the consumer's role permits. See
[Agentic Generated Applications](../ai_engine/agentic_generated_applications.md).

## How access works

An AI Consumer does not manage infrastructure, credentials, or model providers. Access is granted through the
organization's identity provider and governed by role-based access control and the LLM Gateway, so a consumer reaches only the models,
resources, and applications their role permits, and their usage is tracked and attributed like everyone else's.

## What's next

- Want to build an application with agentic frameworks? See
  [Agentic Generated Applications](../ai_engine/agentic_generated_applications.md).
- Want to deploy models on your organization's resources? See the
  [One-click Inference Service](../deploying_models.md).
