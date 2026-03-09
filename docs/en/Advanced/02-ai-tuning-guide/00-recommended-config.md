---
title: "2.0 Recommended Configuration"
description: "Reference for efficient Claude Code configuration"
chapter: "Chapter 2"
---

# 2.0 Recommended Configuration

This will help you quickly set up an efficient AI coding environment. Please make sure you have already installed Claude Code.

## 1. Install Plugins

> Advanced\02-ai-tuning-guide\03-mcp-and-skills.md This section provides a detailed introduction to plugin components.

Open Claude and enter `/plugin` to open the plugin store.

![image-20260203183832193](/images/Advanced/image-20260203183832193.png)

Use the up and down arrow keys on your keyboard to switch between plugins for installation, press Space to select, and press `i` to install. Some plugins will open a browser page during installation for sign-in; just follow the prompts.

![image-20260203184020360](/images/Advanced/image-20260203184020360.png)

### Recommended Plugins

We recommend installing the following official plugins. You can also choose the plugins you need based on their descriptions:

| Plugin Name | Description |
|---------|---------|
| code-review | Code review |
| code-simplifier | Code simplification |
| commit-commands | Commit commands |
| context7 | Library documentation lookup |
| feature-dev | Feature development wizard |
| frontend-design | Frontend UI design |
| github | GitHub operations |
| playwright | Browser automation |
| pr-review-toolkit | PR review toolkit |
| pyright-lsp | Python language server |
| supabase | Supabase database |
| TypeScript-lsp | TypeScript language server |

## 2. Install CC-SWITCH

Install [CC-SWITCH](https://github.com/farion1231/cc-switch) to manage multiple AI model providers, MCP, and Skills.

### For Windows Users

Download the latest version from the official [Releases](https://github.com/farion1231/cc-switch/releases) page, or quickly download the installer through the link below:

<https://ghfast.top/https://github.com/farion1231/cc-switch/releases/download/v3.10.3/CC-Switch-v3.10.3-Windows.msi>

### For macOS Users

Open Terminal and install it with Homebrew:

```bash
brew tap farion1231/ccswitch
brew install --cask cc-switch
```

### For Linux Users

Please refer to [cc-switch/README_ZH.md](https://github.com/farion1231/cc-switch/blob/main/README_ZH.md) for installation.

## 3. Basic Configuration

After downloading and installing, open CC-SWITCH. Click the plus sign in the upper-right corner to manage providers. After importing different models, select a provider and enter the corresponding API Key to start using it.

| Main Interface                                                       | Add Provider                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [![Main Interface](https://github.com/farion1231/cc-switch/raw/main/assets/screenshots/main-zh.png)](https://github.com/farion1231/cc-switch/blob/main/assets/screenshots/main-zh.png) | [![Add Provider](/images/Advanced/add-zh.png)](https://github.com/farion1231/cc-switch/blob/main/assets/screenshots/add-zh.png) |

### Switch Providers

- **Main Interface**: Select a provider → Click "Enable"
- **System Tray**: Click the provider name directly (takes effect immediately)

### Restart the Application

Restart your terminal or the Claude Code / Codex / Gemini client to apply the changes.

## 4. MCP Management

Click the "MCP" button in the upper-right corner to enter the MCP management interface. You can click the Import Existing button to import and view the MCPs installed through plugins:

![image-20260203185306838](/images/Advanced/image-20260203185306838.png)

If you have not installed the context7 plugin, you can click the upper-right corner here to add the context7 MCP server.

> After installation, enter usecontext7 during a conversation, and the AI will call the context7 plugin to look up official documentation, improving the accuracy of the code it writes.

![image-20260203184852968](/images/Advanced/image-20260203184852968.png)



## 5. Skills Management

Click the "Skills" button in the upper-right corner to enter the skills management interface:

- **Discover Skills**: Automatically scan preconfigured GitHub repositories (Anthropic official, ComposioHQ, community, etc.)
- **Custom Repositories**: Supports adding custom repositories (including subdirectory scanning)
- **Install Skills**: Click "Install" to install with one click to `~/.claude/skills/`
- **Uninstall Skills**: Click "Uninstall" to safely remove and clean up state
- **Manage Repositories**: Add/remove custom GitHub repositories

For this tutorial, we recommend the following skills. Readers can use them as needed.