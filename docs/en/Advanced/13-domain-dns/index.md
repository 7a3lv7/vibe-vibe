---
title: "Chapter 13: Domains, DNS, and Network Access"
---

# Chapter 13: Domains, DNS, and Network Access

![img](/images/Advanced/mll08c63-bcea35d79958ea70.jpg)

## Introduction

Xiaoming's app was finally deployed successfully.

He eagerly shared the default link assigned by EdgeOne Pages in the group chat: "Come check out the website I built!"

Three minutes later, a friend replied with a screenshot: **403 Forbidden**.

Xiaoming panicked — "But my deployment succeeded!" He kept refreshing the page on his own end, and everything looked fine. But his friend still couldn't open it.

The veteran took one look at the screenshot and smiled: "Don't panic. A 403 means 'the door is closed, and you don't have the key.' Default links on domestic platforms often have access restrictions, so only you can view them. If you want other people to access it too, you need to bind a domain name of your own."

"A domain name? You mean that `.com` thing?"

"Exactly. A domain name is like the internet's contact list — when you search for 'Mom' in your contacts, your phone dials the number for you. When a browser looks up `myapp.com`, DNS helps it find the server. Right now you're using the platform's temporary number, so other people can't look you up. You need to register a proper number and put it in the contact list."

Xiaoming remembered seeing free subdomains online — the kind like `yourname.someone-else-domain.com`, hanging under someone else's domain. The veteran stopped him seriously: "That's like borrowing someone else's street address — the landlord can kick you out at any time. If you're building a real product, your domain is part of your storefront. Spending a few dozen yuan on a proper `.com` is absolutely worth it."

In this chapter, Xiaoming is going to give his website a real, official name.

## Learning Path for This Chapter

1. [Buying a Domain and Configuring DNS](./01-domain-setup.md) — choose a registrar, buy a domain, configure DNS, and get the green lock
2. [Troubleshooting Compliance and Access Issues](./02-compliance-access.md) — filing strategies, no-filing options, and diagnosing common errors

---

**Previous Chapter**: [Chapter 12: Serverless Deployment and CI/CD Automation](../12-serverless-deploy-cicd/index.md)

**Next Chapter**: [Chapter 14: Cloud Server Operations and Project Deployment](../14-vps-ops-deploy/index.md)