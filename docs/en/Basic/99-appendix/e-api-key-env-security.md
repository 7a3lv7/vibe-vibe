---
title: "E. API Keys, Environment Variables, and Basic Security"
order: 5
---

# E. API Keys, Environment Variables, and Basic Security

The basic version does not require you to learn a complete security system right away, but there are a few minimum baseline practices that are best established as early as possible.

## Remember the most important sentence first

> Do not put API Keys directly into your code repository.

This may look simple, but it is extremely important. At the foundational stage, many security issues arise not because the principles are too complex, but because the most basic boundaries were not maintained.

## What is `.env`

You can think of `.env` as: a place specifically for storing configuration values that should not be written directly into code in the first place.

For example:

- API Keys
- Database connection URLs
- Third-party service secrets

These values often vary by environment and are also not suitable for being hardcoded directly into a project.

## What is `.gitignore`

The purpose of `.gitignore` is straightforward: it tells Git which files should not be committed to the repository.

One of the most important habits to build at the foundational stage is making sure files like `.env` are not committed.

## At minimum, check these things before going live

- API Keys are not hardcoded in the source code
- Local sensitive files such as `.env` have not been committed to the repository
- The environment variables required by the deployment platform have been configured correctly
- If a key is missing in production, the page does not pretend to work normally

## A very practical check question

If you are not sure whether the current project has secrets stored in the wrong place, you can ask AI directly:

```text
Please help me check whether any sensitive information is written directly in the code in this project.
Focus on checking API Keys, environment variables, and configuration files that should not be committed to the repository.
If there are any issues, please tell me how I should fix them.
```

## What the basic version stops short of here

The basic version only asks you to build baseline awareness. It does not go into a complete authentication system, permission design, or more complex security strategies here. If you want to systematically strengthen this area, you can read Chapter 8 of the advanced version: [Who Can Access My Data](/Advanced/08-auth-security/).