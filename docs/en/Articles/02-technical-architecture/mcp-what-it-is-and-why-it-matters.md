---
title: "MCP: What It Is and Why It Matters"
description: The Model Context Protocol creates a universal standard for connecting AI models to external tools and data sources—the USB-C for AI integrations
author: Addy Osmani
source: https://addyo.substack.com/p/mcp-what-it-is-and-why-it-matters
date: '2026-03-07'
category: 02-technical-architecture
tags:
  - MCP
  - AI Integration
  - Model Context Protocol
  - Developer Tools
  - AI Agents
---

# MCP: What It Is and Why It Matters

**Author: Addy Osmani**

**Original: [Read the full article](https://addyo.substack.com/p/mcp-what-it-is-and-why-it-matters)**

<div class="article-meta">
</div>

> ## Summary
>
> The Model Context Protocol (MCP) is an open standard that creates a universal language between AI models and external tools. Think of it as USB-C for AI integrations—one protocol that replaces the chaos of custom connectors.

## The Integration Problem

Before MCP, connecting AI tools to external data sources was a mess. Every AI application needed custom integrations for every service it wanted to talk to. Want your AI assistant to work with GitHub? Build a custom integration. Need it to query your database? Another custom integration. Figma? Slack? Jira? Each one required bespoke code.

This creates what's known as the **N×M integration problem**: if you have N AI tools and M data sources, you need N×M custom integrations. With 10 AI tools and 20 data sources, that's 200 separate integrations to build and maintain. Every new tool or data source multiplies the burden.

### The USB-C Analogy

MCP solves this the same way USB-C solved the cable problem. Before USB-C, every device had its own proprietary connector—different cables for phones, cameras, laptops, and peripherals. USB-C replaced all of them with a single universal standard.

MCP does the same for AI integrations. Instead of building custom connectors between every AI tool and every data source, both sides implement one standard protocol. Any MCP-compatible AI tool can instantly work with any MCP-compatible data source. The N×M problem becomes N+M.

## How MCP Works

### Client/Server Architecture

MCP uses a straightforward client/server model:

- **MCP Clients** (also called "hosts"): These are AI applications—coding assistants, chat interfaces, IDE plugins—that need to access external tools and data
- **MCP Servers**: These are lightweight services that expose specific capabilities—a GitHub server, a database server, a file system server

The client discovers what the server can do, then invokes those capabilities as needed. This separation means you can mix and match freely: any client works with any server.

### The Protocol Specification

MCP defines several key components:

**Transports**: How clients and servers communicate. MCP supports multiple transport mechanisms:
- **stdio**: Communication through standard input/output—simple, works locally
- **HTTP with Server-Sent Events (SSE)**: For remote servers and web-based integrations
- **Streamable HTTP**: A newer transport optimized for streaming responses

**Messages**: The structured communication format between clients and servers. Messages follow a request/response pattern with well-defined schemas for:
- Discovering available tools and resources
- Invoking tools with specific parameters
- Returning results with appropriate typing
- Handling errors gracefully

**Capabilities**: What a server can expose:
- **Tools**: Actions the AI can take (create a file, run a query, send a message)
- **Resources**: Data the AI can read (file contents, database records, API responses)
- **Prompts**: Reusable prompt templates the server provides

### Discovery and Negotiation

When an MCP client connects to a server, they go through a capability negotiation:

1. Client connects and identifies itself
2. Server responds with its available capabilities
3. Client can query for detailed tool descriptions
4. Both sides agree on supported protocol features

This handshake ensures compatibility and allows servers to evolve their capabilities without breaking existing clients.

## Real-World Integrations

MCP's value becomes concrete when you look at the ecosystem of servers already available:

### Development Tools

- **GitHub**: Create repositories, manage pull requests, search code, handle issues
- **GitLab**: Similar repository management capabilities
- **Linear**: Project management and issue tracking
- **Sentry**: Error monitoring and debugging

### Design and Creative Tools

- **Figma**: Access design files, extract components, read design tokens
- **Blender**: 3D modeling operations, scene manipulation
- **Unity**: Game development integration

### Productivity and Automation

- **Zapier**: Connect to thousands of apps through Zapier's automation platform
- **Slack**: Read and send messages, manage channels
- **Google Drive**: Access and manage documents

### Data and Infrastructure

- **PostgreSQL/MySQL**: Direct database querying and management
- **Redis**: Cache operations
- **AWS**: Cloud infrastructure management
- **Kubernetes**: Container orchestration

Each of these servers implements the MCP specification, meaning any MCP client—whether it's Claude, a custom AI tool, or an IDE plugin—can immediately use them without custom integration code.

## Security and Access Control

MCP takes security seriously with multiple layers of protection:

### Permission Model

- **Granular permissions**: Servers expose specific capabilities, and clients can request only what they need
- **User approval**: Critical actions can require explicit user confirmation before execution
- **Scope limiting**: Servers can restrict access to specific resources or operations

### Authentication

- **OAuth 2.0 support**: Standard authentication flows for accessing protected resources
- **API key management**: Simple key-based auth for local servers
- **Token scoping**: Fine-grained access tokens that limit what operations are permitted

### Data Safety

- **No persistent data access**: MCP servers typically don't store conversation data
- **Request-level isolation**: Each request is independent; servers don't accumulate context
- **Audit logging**: Servers can log all operations for compliance and debugging

### Sandboxing Considerations

MCP servers run as separate processes, providing natural isolation. A misbehaving server can't affect the client or other servers. This architecture means:

- Servers can be written in any language
- Server crashes don't take down the AI tool
- Different servers can have different security policies

## Impact on the Developer Ecosystem

### Reduced Vendor Lock-In

Before MCP, if you built integrations for one AI tool, switching to another meant rebuilding everything. With MCP, your server implementations work with any MCP-compatible client. This creates healthy competition—AI tools compete on quality, not on integration count.

### Plug-and-Play Capability

The community-driven nature of MCP means new servers appear constantly. When a new service or tool becomes popular, someone builds an MCP server for it, and every MCP client immediately benefits. This network effect accelerates the entire ecosystem.

### Democratized Tool Building

MCP servers are relatively simple to build. A basic server that exposes a few tools can be written in an afternoon. This low barrier to entry means:

- Individual developers can contribute servers for niche tools
- Companies can expose their APIs as MCP servers for AI consumption
- Internal tools can be made AI-accessible without major engineering effort

### Composability

Because MCP servers are modular, complex workflows can be assembled from simple building blocks. An AI agent might use a GitHub server to read code, a database server to check production data, and a Slack server to notify the team—all through the same protocol, all composed naturally.

## Looking Ahead

MCP represents a fundamental shift in how AI tools interact with the world. By standardizing the integration layer, it:

- **Accelerates adoption**: New AI tools immediately have access to a rich ecosystem of data sources
- **Enables specialization**: AI tools can focus on being great at reasoning and interaction, while MCP servers handle the integration complexity
- **Future-proofs investments**: Today's MCP servers will work with tomorrow's AI tools

The protocol continues to evolve, with active development on features like:
- Improved streaming support for real-time data
- Better authentication and authorization patterns
- Enhanced discovery mechanisms for finding available servers
- Standardized testing and validation tools

For developers building AI-powered tools, MCP isn't optional—it's becoming the expected standard for how AI agents interact with the world. Understanding and adopting it early provides a significant advantage as the ecosystem matures.
