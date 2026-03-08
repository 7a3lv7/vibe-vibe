---
title: "10.1 From Localhost to the Internet"
description: "Understand the three network layers—loopback, LAN, and WAN—and why your project needs to be deployed"
---

# 10.1 From Localhost to the Internet

> **Goal of this section**: Understand why others can't open localhost, learn how to test on your phone over a local network, and figure out the different ways to "let other people see" your project.

## Localhost: Each Device "Talking to Itself"

Xiaoming thought `localhost` meant "my computer's address." Not exactly—localhost actually means "the device making the request itself."

When Xiaoming types `localhost:3000` into his own browser, the browser is accessing Xiaoming's computer. Since a Next.js server is running on Xiaoming's computer, the page displays normally.

When a friend types `localhost:3000` into their own browser, the browser is accessing the friend's computer. Nothing is running on the friend's computer, so the browser shows a "connection refused" error.

Here's an intuitive test: if you type `localhost` into your phone's browser, it accesses the phone itself, not your computer. Every device's localhost points to itself.

Technically, localhost maps to the IP address `127.0.0.1`, called the **loopback address**. Packets leave your computer and return right back to your computer, never going through a network cable or Wi-Fi. That's why localhost is inherently secure—external devices simply can't reach it.

::: info Why do development servers use localhost by default?
Precisely because localhost can only be accessed from the local machine, it's the safest development environment. Your code may have bugs, security vulnerabilities, or test data in the database—and that's fine, because only you can see it. Development frameworks bind to localhost by default to avoid accidentally exposing your work-in-progress to other devices on the local network.
:::

## Local Network: Devices on the Same Wi-Fi Can Reach Each Other

Xiaoming understood the localhost issue, but he still had another need: testing touch gestures and responsive layouts on his phone. Surely he didn't have to deploy to the public internet every time, right?

No need. If your phone and computer are connected to the same Wi-Fi, they're on the same **local area network (LAN)**. Devices on the same LAN can access each other directly.

Imagine devices on the same Wi-Fi as people in the same office—they can call out to each other directly. Different Wi-Fi networks are like different cities—you can't just shout across them.

A local network is the core method for real-device debugging in full-stack development. You don't need to deploy to the public internet every time just to check how things look on your phone—if your phone and computer are on the same Wi-Fi, you can just access it directly.

### Find Your Local IP Address

Your computer has an internal IP address on the local network, usually in the format `192.168.x.x` or `10.x.x.x`. To find it:

::: code-group

```bash [Windows]
ipconfig
```

```bash [macOS]
ifconfig
```

```bash [Linux]
ip addr
```

:::

In the output, look for an address like `192.168.1.105` (not `127.0.0.1`, which is localhost).

### Access the Project on Your Computer from Your Phone

1. Make sure your phone and computer are connected to the same Wi-Fi
2. Make sure the project is running (for example, `pnpm dev` on port 3000)
3. Enter `http://192.168.x.x:3000` in your phone's browser (replace it with your actual IP)

If everything goes smoothly, you'll be able to see your project on your phone. You can test touch gestures, check responsive layouts, and verify the mobile interaction experience—no deployment and no extra tools required.

::: tip Next.js binding address
Some frameworks listen only on `127.0.0.1` (localhost) by default and won't accept requests from other devices on the local network. If your phone can't access it, try specifying that it should listen on all network interfaces in the startup command:

```bash
# Next.js
pnpm dev --hostname 0.0.0.0

# Vite
pnpm dev --host
```

`0.0.0.0` means "accept requests from any network interface," including the local network.
:::

## Firewall: Why Still Can't the Phone Open It?

Xiaoming followed the steps, but his phone showed "Unable to connect."

In 90% of cases, this is the **firewall** blocking it.

A firewall protects your computer from being accessed freely by other devices on the local network. It's like your phone's "Do Not Disturb" mode—not that there are no calls coming in, but that you've chosen to block them.

When you run `pnpm dev`, Node.js listens for requests on port 3000. But the firewall may not allow external devices to access that port, so the phone's request gets stopped at the door.

Troubleshooting order:

1. **Make sure your phone and computer are on the same Wi-Fi** (it sounds obvious, but this is the most common thing people miss)
2. **Make sure the development server is listening on `0.0.0.0`** (see the tip above)
3. **Check your firewall settings**—this is the final and most common roadblock

### Let AI Help You Troubleshoot

Firewall configuration varies by operating system, and doing it manually is easy to get wrong. Just tell Claude Code directly:

> "My phone and computer are connected to the same Wi-Fi, but I can't open my computer's IP address from my phone. Help me troubleshoot the firewall settings and allow Node.js port 3000 through."

Claude Code will generate the appropriate commands based on your operating system.

::: details Example: Allowing Through Windows Firewall

```powershell
# Allow inbound TCP traffic on port 3000
New-NetFirewallRule -DisplayName "Node.js Dev Server" -Direction Inbound -Protocol TCP -LocalPort 3000 -Action Allow
```

:::

::: details Allowing Through macOS Firewall

macOS firewall management is app-based by default. In "System Settings → Network → Firewall → Options," make sure Node.js is allowed to accept incoming connections.

:::

::: tip Remember to revert it when you're done
After real-device debugging is complete, it's a good idea to disable this firewall rule. Leaving development ports exposed on the local network for a long time is not a good habit.
:::

## Wide Area Network: Different Wi-Fi Means It Can't Reach You

Xiaoming got phone debugging working. But his friend is in another city, connected to a different Wi-Fi network. The local IP `192.168.x.x` is only valid inside your own router's network. Your friend's network has no way to find that address.

This brings us to the **wide area network (WAN)**—the actual internet.

To let people on different networks access your service, you need a **public IP**. But regular home broadband usually doesn't come with an independent public IP. ISPs use a technology called NAT to let many users share the same public IP.

Here's an analogy: a public IP is like an apartment building's street address. The delivery driver can only reach the main entrance downstairs (the ISP's public IP), not your apartment door directly (your device). Your computer is hidden behind the ISP's NAT, so external requests can't find it.

You can verify this yourself: search for "my IP" in your browser, and the public IP you see will be completely different from the local IP you got with `ipconfig`. The public IP is assigned by your ISP to your home router (and may be shared by multiple households), while the local IP is assigned by your router to your computer.

So if you want people on the internet to access your project, you need to deploy your code to a server with a public IP. That's what Chapter 12 is about.

## Overview of the Three Network Layers

Now put the three layers we just covered side by side. From the inside out, the reachable scope gets larger and the security risk gets higher. localhost is the safest (only you), the local network is next (people on the same Wi-Fi), and the wide area network is the most open (the whole world).

<NetworkLayers />

## Three Paths: From "Only I Can See It" to "Other People Can See It"

Xiaoming has now sorted out the whole picture. If you want other people to see your project, there are three paths, each suited to a different scenario:

### 1. Direct LAN Access

**Best for**: Testing on your own phone, or letting a friend sitting nearby take a quick look.

**Requirements**: The devices are connected to the same Wi-Fi.

**How**: Enter your computer's local IP + port in the phone browser.

**Limitation**: It stops working as soon as you leave that Wi-Fi.

### 2. Tunneling

**Best for**: A friend is in another city, and you want to let them temporarily check it out without going through the full deployment process.

**How**: Use a tool to create a tunnel between your computer and a public server, generating a temporary link.

**Limitation**: The link changes each time, your computer has to stay on, and the speed can be unstable.

The next section will explain exactly how to do it.

### 3. Formal Deployment

**Best for**: Letting anyone access your product anytime.

**How**: Deploy your code to platforms like Vercel or Tencent Cloud and get a stable public URL.

**Prerequisite**: You need to manage your code with Git first (Chapter 11), and then deploy it (Chapter 12).

<LocalhostVsPublic />

---

Xiaoming looked at these three paths and now had a clear idea. His friend was in another city, so the local network wouldn't work. Formal deployment felt like overkill—he just wanted his friend to take a quick look.

"Is there a middle-ground solution?"

"Yes. The next section will teach you about tunneling."

---

**Previous section**: [Preface](./index.md)

**Next section**: [10.2 Tunneling: Let a Friend Take a Quick Look Temporarily](./02-tunneling.md)