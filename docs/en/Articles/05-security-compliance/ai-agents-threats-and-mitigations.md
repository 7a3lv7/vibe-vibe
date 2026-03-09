---
title: "AI Agents Are Here—and So Are the Threats"
description: In-depth security research from Palo Alto Networks Unit 42, mapping 9 categories of attack scenarios and defense strategies for AI agent applications
author: Jay Chen, Royce Lu
source: https://unit42.paloaltonetworks.com/agentic-ai-threats/
date: '2025-05-01'
category: 05-security-compliance
tags:
  - AI Security
  - Prompt Injection
  - Agent Security
  - Vulnerability Research
  - Palo Alto Networks
---

# AI Agents Are Here—and So Are the Threats

> Source: [Palo Alto Networks Unit 42](https://unit42.paloaltonetworks.com/agentic-ai-threats/)

## The Shift from Theoretical to Concrete

For years, the conversation around AI security lived in the abstract—"AI might have risks" was the standard disclaimer appended to every product launch. Palo Alto Networks' Unit 42 research team has moved the conversation forward by mapping out nine concrete, enumerable categories of attack vectors targeting AI agent applications. This is no longer about hypotheticals. These are real, reproducible threats that developers building agentic systems need to understand today.

The fundamental insight: AI agents are different from traditional AI chatbots because they *act*. They call APIs, read databases, execute code, and interact with external services. Every one of those capabilities is also an attack surface.

## The Nine Attack Categories

### 1. Prompt Injection

The most widely discussed attack vector remains one of the most dangerous. Direct prompt injection manipulates the agent's instructions through user input, while indirect prompt injection hides malicious instructions in data sources the agent consumes—documents, web pages, database records. An agent that summarizes emails could be hijacked by a single crafted message buried in the inbox.

### 2. Tool Abuse

Agents interact with the world through tools. When an agent has access to a code execution environment, a file system, or an API, attackers can craft inputs that cause the agent to use those tools in unintended ways. A code-generation agent could be tricked into writing and executing malicious scripts. A database agent could be led to run destructive queries.

### 3. Privilege Escalation

Agents often operate with broad permissions to be effective. An attacker who gains limited access can exploit the agent's elevated privileges to reach resources they shouldn't touch. If an agent has admin-level database access to "be helpful," a clever prompt can leverage that access for data manipulation far beyond the user's actual authorization level.

### 4. Data Exfiltration

Agents that process sensitive information can be manipulated into leaking it. This might happen through crafted outputs that encode data in seemingly innocent responses, through tool calls that send information to external endpoints, or through conversation histories that accumulate sensitive context over time.

### 5. Memory Poisoning

Agents with persistent memory—those that remember past interactions to improve future ones—can have that memory corrupted. An attacker plants false information in early interactions that influences the agent's behavior in later, seemingly unrelated conversations. This is particularly insidious because the attack and its effect are separated in time.

### 6. Cascading Failures in Multi-Agent Systems

Modern architectures increasingly involve multiple agents collaborating. A compromised agent in a pipeline can propagate malicious instructions to downstream agents. If Agent A summarizes data and Agent B acts on those summaries, poisoning Agent A's output effectively controls Agent B's actions—without ever directly attacking Agent B.

### 7. Supply Chain Attacks via MCP and Tool Ecosystems

The Model Context Protocol (MCP) and similar tool-integration frameworks create a new supply chain surface. Malicious or compromised tool servers can feed poisoned data to agents. A compromised MCP server providing "weather data" could inject instructions alongside legitimate responses, hijacking the agent's subsequent behavior.

### 8. Credential Theft

Agents that manage authentication tokens, API keys, or user sessions become high-value targets. Attackers may manipulate agents into exposing stored credentials through conversation outputs, logging mechanisms, or error messages that inadvertently reveal secrets.

### 9. Denial of Service

Agents can be driven into infinite loops, excessive resource consumption, or repeated expensive API calls. An attacker might craft inputs that cause recursive tool invocations, overwhelming both the agent and the services it depends on.

## Defense Architecture

Unit 42's research doesn't just enumerate threats—it provides a layered defense framework.

### Input Validation

Every input to an agent—user messages, tool responses, retrieved documents—must be treated as potentially adversarial. This means structured input schemas, content filtering before the LLM processes data, and separation between instructions and user-supplied content. The key insight is that validation must happen at every boundary, not just at the initial user input.

### Output Filtering

Agent outputs must be inspected before they reach tools or users. This includes checking for data leakage patterns, validating that tool calls match expected patterns, and ensuring responses don't contain encoded exfiltration attempts. Output guardrails are the last line of defense before an agent's decisions become real-world actions.

### Least-Privilege Design

Every agent should have the minimum permissions necessary for its task. This applies to tool access (only the tools needed for the current task), data access (scoped to relevant records), and network access (restricted to required endpoints). Broad permissions "for convenience" are a recurring source of exploitable vulnerabilities.

### Sandboxing

Agents that execute code or interact with file systems should operate in isolated environments. Container-based sandboxing, restricted file system views, and network segmentation limit the blast radius of any successful attack. The cost of isolation is far less than the cost of a compromised production environment.

### Audit Logging

Comprehensive logging of agent decisions, tool invocations, and data access creates both a detection mechanism and a forensic trail. Anomaly detection on these logs can identify attacks in progress—unusual tool call patterns, unexpected data access, or conversations that diverge from normal behavior.

### Rate Limiting

Resource-based rate limiting prevents denial-of-service attacks and constrains the damage from compromised agents. Limits should apply to tool invocations, token consumption, external API calls, and conversation length.

## From Awareness to Action

The Unit 42 research represents a maturation in how the industry thinks about AI security. The nine categories provide a shared vocabulary for threat modeling agentic systems. For developers building with AI agents, the practical takeaway is clear: threat model your agents the same way you'd threat model any application that handles sensitive data and performs privileged operations—because that's exactly what they are.

The agents are already deployed. The threats are already real. The question is whether your defenses are keeping pace.
