---
title: "10.2 Network Tunneling: Let a Friend Take a Quick Look"
description: "Use a tunneling tool to generate a temporary public link so friends far away can access your local project"
---

# 10.2 Network Tunneling: Let a Friend Take a Quick Look

> **Goal of this section**: Understand how network tunneling works, use Cloudflare Tunnel to generate a temporary public link, and send it to a friend.

## Why network tunneling is needed

Xiaoming's friend is in another city, and they're not on the same local network. A formal deployment would require Git, platform configuration, and a domain name—but he just wants his friend to take a quick look at how the rating feature works.

The old hand said: "There's a temporary solution—network tunneling. No deployment needed, no domain name needed, and a single command can generate a public link."

## How a tunnel works

At its core, network tunneling creates a **tunnel** between your computer and a public server.

<TunnelFlow />

Your friend visits a temporary address on the public server, and that server forwards the request to your computer, then sends your computer's response back to your friend. Throughout the process, your friend doesn't need to know your IP address, and you don't need to have a public IP.

A real-life analogy: a WeChat video call. You and your friend aren't on the same network, but WeChat's servers relay the audio and video data between you. Network tunneling is similar, except what gets relayed is web requests.

The key difference is this: you establish the tunnel proactively. Your computer initiates the connection to the public server (outbound), rather than the public server trying to reach your computer (inbound). Because outbound connections aren't blocked by NAT and firewalls, a tunnel can get around the "home broadband doesn't have a public IP" problem.

## Cloudflare Tunnel (recommended)

Cloudflare Tunnel is currently the simplest option: free, no registration required, and no traffic limits.

### Install

Tell Claude Code:

> "Help me install the cloudflared command-line tool."

Or install it manually:

::: code-group

```bash [Windows]
winget install cloudflare.cloudflared
```

```bash [macOS]
brew install cloudflared
```

```bash [Linux (Debian/Ubuntu)]
curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb -o cloudflared.deb
sudo dpkg -i cloudflared.deb
```

:::

### Start the tunnel

Make sure your project is running locally (for example, `pnpm dev` on port 3000), then **open another terminal**:

```bash
cloudflared tunnel --url http://localhost:3000
```

The terminal will output a temporary link:

```
https://random-words-here.trycloudflare.com
```

Send this link to your friend, and they'll be able to see your project.

::: tip The link is different every time
In free mode, each time you start cloudflared it generates a new random link. Once you close the terminal (Ctrl+C), the link immediately stops working. That's by design—a temporary solution should look temporary.
:::

### Let AI handle it in one step

If you don't want to remember commands, just tell Claude Code directly:

> "Help me use Cloudflare Tunnel to expose my local port 3000 to the public internet and generate a temporary link I can send to a friend."

Claude Code will check whether cloudflared is installed, install it for you if it isn't, and then start the tunnel.

### Common issues

**The link was generated, but my friend can't open it?** Make sure your development server is actually running. cloudflared only forwards requests—if your local service isn't running, your friend will just see an error page.

**The terminal shows a connection error?** Check whether the port number is correct. If your project is running on 5173 (Vite's default port) instead of 3000, the command should be `cloudflared tunnel --url http://localhost:5173`.

**It's very slow?** That's normal. The request has to go from your friend's device to a Cloudflare server, then to your computer, and then back along the same path. Latency depends on the network quality between you and the Cloudflare server. If you're in China and the speed is unacceptable, consider using cpolar.

## Alternative options

Cloudflare Tunnel is good enough for most scenarios. If you have special requirements, there are two alternatives:

**ngrok**: A long-standing tool that requires registering for a free account. Its advantage is a built-in Web debugging dashboard (`http://localhost:4040`) where you can inspect all requests and responses passing through the tunnel, which is very convenient for troubleshooting. The free plan has connection limits, and visitors will see ngrok's warning page. Official site: [ngrok.com](https://ngrok.com)

**cpolar**: A China-based service. If both you and your friend are in China, it will be noticeably faster than Cloudflare and ngrok. It requires an account. Official site: [cpolar.com](https://www.cpolar.com)

| Tool | Registration Required | Speed in China | Highlights |
|------|------------|---------|------|
| Cloudflare Tunnel | No | Average | Simplest, free with no limits |
| ngrok | Yes | Average | Great debugging dashboard |
| cpolar | Yes | Fast | Optimized for China |

Which one should you choose? If you don't have special requirements, use Cloudflare Tunnel. If you need a debugging dashboard, use ngrok. If you're in China and speed is the bottleneck, use cpolar.

::: tip You don't need to learn all of them
The core workflow for all three tools is almost identical: install → start with one command → get a link → send it to a friend. Just pick one and get comfortable with it. If you're not sure, use Cloudflare Tunnel—no registration, no configuration, free, and unlimited.
:::

## Security notes

The moment the tunnel opens, your computer is temporarily exposed to the public internet. Even if it's only temporary, there are a few things you absolutely need to keep in mind:

**Close it as soon as you're done**. Press Ctrl+C to stop the cloudflared process, and the tunnel will be cut off. Don't leave the tunnel running overnight, and don't leave home with it still open.

**Don't expose ports that shouldn't be exposed**. The tunnel should point only to your Web application port (such as 3000). Database ports (5432, 3306), admin panels, debugging tools—these must never be exposed through a tunnel.

**Make sure authentication is enabled**. If your project has a login feature (the result from Chapter 8), make sure authentication is turned on. Don't temporarily disable login just because "it's only for a friend to take a look"—the tunnel link is public, and anyone who gets the link can access it.

**Don't transmit sensitive data through the tunnel**. Tunnels are suitable for demoing UI and features, not for handling real user data or passwords.

::: warning One-sentence rule
A tunnel is a temporary hole drilled into the wall. Once your friend is done looking, patch the hole immediately.
:::

### Security checklist

Before opening the tunnel, do a quick review:

- [ ] Is the project's login feature enabled (the result from Chapter 8)
- [ ] Does the `.env` file contain sensitive information (API keys, database passwords)—these won't be leaked through the tunnel, but it's always good to double-check
- [ ] Does the tunnel point only to the Web application port, without exposing any other services
- [ ] Will the tunnel be closed immediately after the demo

## From temporary to production

Xiaoming sent the link to his friend, and his friend finally saw the rating feature. "Pretty cool!" his friend said.

But Xiaoming quickly noticed the problems:

- Every time he restarts cloudflared, the link changes, so he has to send it again
- His computer has to stay on the whole time; if he closes the laptop lid, his friend gets disconnected
- His friend says it sometimes loads slowly, so the experience is inconsistent
- He wants more people to see it, but it's impossible to send the link individually to everyone

These aren't bugs—they're the natural limitations of network tunneling. It's meant to be a temporary solution. Your computer is not a server, and your home network is not a data center.

If you want your product to be reliably accessible to everyone, you need a **proper deployment**: put your code on a professional server so it can run 24/7, have a stable address, and provide enough bandwidth.

But before deployment, you need to solve one prerequisite problem: code management. Right now, your code exists only on your computer. How can a deployment platform get it? The answer is **Git**—the topic of the next chapter.

Xiaoming's roadmap is becoming clearer:

1. **Git** (Chapter 11): Push your code to GitHub so deployment platforms can pull it
2. **Deployment** (Chapter 12): Deploy your code to platforms like Vercel and get a stable public address
3. **Domain names and HTTPS** (Chapter 13): Configure a custom domain and enable encrypted connections

Each step builds on the previous one. Network tunneling helped Xiaoming validate that "friends think this feature is interesting." Next comes turning it into a real product.

---

::: tip Chapter recap
- localhost is a loopback address; each device's localhost points to itself
- Devices on the same Wi-Fi can access each other through local network IPs
- Firewalls may block access within the local network, so you may need to allow the corresponding port
- Network tunneling relays requests through a public server to generate a temporary link
- Network tunneling is a temporary solution; a real launch requires deployment (Chapter 12)
:::

---

**Previous section**: [10.1 From Localhost to the Internet](./01-network-layers.md)

**Next chapter**: [Chapter 11: Git Version Control and Cross-Platform Collaboration](../11-git-collaboration/index.md)