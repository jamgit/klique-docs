# Guardrails

> How Klique secures deployed models with authenticated endpoints, role-based model access, and NeMo Guardrails content safety rails.

Klique protects access to deployed models and applications at several levels: authenticated endpoints, role-based access
to models, and content guardrails through NeMo Guardrails.

## Endpoint authentication

Every endpoint is protected by a token management system with an interface for issuing, viewing, and revoking access
tokens. A request without a valid token is rejected, so only holders of a valid token can reach an endpoint.

## Model access control

[Role-based access control](rbac.md) governs models, so only specific users or groups can access a given model. Access
to a model is granted the same way as access to other Klique resources.

## NeMo Guardrails

NVIDIA NeMo Guardrails is an open-source toolkit for adding programmable guardrails, or "rails", to large language model
applications. Klique provides a NeMo Guardrails application that offers an intuitive user interface and orchestrates the
NeMo Guardrails framework for you. It integrates with Klique's model deployment capabilities to deploy guardrail models,
placing a programmable safety layer between users and the model.

<!-- For configuration and usage, see the [Guardrails application documentation](#). -->
<!-- TODO: replace the "#" placeholder above with the Guardrails application documentation link once it is published. -->

NeMo Guardrails supports several types of rails:

- **Input rails** filter or modify a user's message before it reaches the model.
- **Dialog rails** influence how the model is prompted and shape the conversation flow.
- **Retrieval rails** process retrieved chunks in Retrieval-Augmented Generation (RAG) scenarios.
- **Execution rails** control the inputs and outputs of custom actions or tools the model calls.
- **Output rails** filter or modify the model's response before it is returned.

Through these rails, it provides content and topic safety, jailbreak and prompt-injection detection, fact-checking and
hallucination detection (including for RAG), and input and output moderation.
