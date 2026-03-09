---
title: Security Boundaries in Agentic Architectures
description: Designing security boundaries in agentic architectures—from secret injection to full application sandboxing
author: Malte Ubl, Harpreet Arora
source: https://vercel.com/blog/security-boundaries-in-agentic-architectures
date: '2026-03-07'
category: 05-security-compliance
tags:
  - Vercel
  - Security
  - Agentic Architecture
  - Sandboxing
  - Access Control
---

# Security Boundaries in Agentic Architectures

> Source: [Vercel Blog](https://vercel.com/blog/security-boundaries-in-agentic-architectures)

## The Fundamental Tension

AI agents are useful precisely because they can *do things*—call APIs, query databases, execute code, write files. But every capability you grant an agent is also a capability an attacker can exploit. The central design challenge in agentic architectures is enabling agents to act effectively while ensuring they cannot act destructively.

This isn't a new problem in software engineering. Sandboxing, access control, and least privilege have been core security principles for decades. What's new is the attack surface: agents receive natural language inputs that are inherently ambiguous, making traditional input validation insufficient on its own. You can't write a regex to catch every possible prompt injection.

## Boundary Models

Malte Ubl and Harpreet Arora from Vercel outline a progression of security boundary models, each adding a layer of protection.

### Secret Injection

The first and most fundamental boundary: **never expose credentials to the agent itself**. Instead of giving an agent an API key and asking it to make authenticated requests, inject the credentials at the infrastructure level. The agent constructs the request; the runtime environment adds authentication before the request leaves the boundary.

This pattern is familiar from serverless architectures where environment variables hold secrets, but the application code doesn't pass them around freely. For agents, the principle is stricter—the LLM should never see, process, or have the opportunity to leak a credential. Secrets exist in a layer the agent cannot access.

Practical implementation:
- Use a proxy layer that intercepts agent tool calls and attaches credentials
- Store secrets in a vault accessible only to the execution runtime, not the agent process
- Rotate credentials frequently since the attack window should be as narrow as possible

### Tool-Level Permissions

Even with credentials secured, agents need guardrails on *what they can do*. Tool-level permissions define an explicit whitelist of allowed operations.

Rather than giving an agent generic "database access," you define: this agent can execute `SELECT` queries on the `products` table with a maximum of 100 rows returned. No `DELETE`, no `UPDATE`, no access to the `users` table. The tool interface enforces these constraints regardless of what the LLM requests.

This maps to the principle of least privilege but applied at the tool definition layer. Each tool exposes only the operations relevant to the agent's task, with hard limits on scope, volume, and side effects.

### Application Sandboxing

For agents that execute code—and increasingly, that's a core capability—sandboxing is non-negotiable. The execution environment must be fully isolated from the host system and from other agents' environments.

Key properties of a well-designed sandbox:
- **Ephemeral**: the environment is created for each execution and destroyed afterward
- **Resource-limited**: CPU, memory, and execution time are capped
- **Network-restricted**: only explicitly allowed outbound connections are permitted
- **Filesystem-scoped**: the agent sees only the files relevant to its task

Vercel's approach with their Sandbox product provides these guarantees at the platform level, removing the burden from individual developers to implement isolation correctly.

### Network Isolation

Agents should not have unrestricted network access. A compromised agent with internet access can exfiltrate data to arbitrary endpoints, download malicious payloads, or attack internal services.

Network boundaries for agents should follow a strict allowlist model: define exactly which hosts and ports the agent can reach, and block everything else. This is particularly critical for agents that process user-supplied content, where indirect prompt injection could attempt to trigger outbound connections.

## Vercel's Implementation

Vercel's approach demonstrates how these principles translate into platform features:

**Scoped API Keys**: Each agent operation receives a key with the minimum permissions required for that specific action. Keys are short-lived and automatically revoked after use.

**Sandbox Execution**: Code generated or modified by agents runs in isolated containers with predefined resource limits. The sandbox provides a full execution environment without exposing the host system.

**Audit Trails**: Every agent action—tool calls, data access, code execution—is logged with full context. This enables both real-time anomaly detection and post-incident forensics.

**Request-Level Isolation**: Each user request to an agent runs in its own security context. One user's interaction cannot influence another's, and a compromised session is contained to that single request.

## Tradeoffs Between Security and Capability

Every security boundary comes at a cost to agent capability:

- **Secret injection** means agents can't dynamically choose which services to authenticate with
- **Tool permissions** mean agents can't creatively combine tools in ways you didn't anticipate
- **Sandboxing** adds latency and limits access to system resources
- **Network isolation** prevents agents from fetching arbitrary external resources

The art is in finding the right balance for your specific use case. A code-generation agent for internal tooling has different security requirements than a customer-facing agent handling financial data. The framework should enable you to dial security up or down deliberately, not accidentally.

## Implementation Guidance

For developers building agentic systems today:

1. **Start with secret injection**—it's the highest-impact, lowest-effort boundary. If your agent can see credentials, you have a data exfiltration vulnerability waiting to happen.

2. **Define tool permissions explicitly**—don't rely on the LLM's "judgment" to avoid dangerous operations. Hard constraints at the tool layer are the only reliable guardrail.

3. **Sandbox code execution from day one**—retrofitting isolation into a running system is orders of magnitude harder than building it in from the start.

4. **Log everything**—you cannot detect attacks against agents without comprehensive observability. The cost of logging is trivial compared to the cost of flying blind.

5. **Assume the agent will be compromised**—design your boundaries so that a fully compromised agent still can't cause catastrophic damage. This is the same principle as designing buildings to survive fires: not "if" but "when."

Security in agentic architectures isn't about preventing agents from being useful. It's about ensuring that their usefulness doesn't become a liability.
