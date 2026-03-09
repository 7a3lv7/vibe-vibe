---
title: "8.2 Authentication Methods and Choosing a Solution"
description: "Session vs Token, OAuth, Passkeys, Magic Link—understand the differences between authentication methods and choose the right auth library for your project"
chapter: "Chapter 8"
---

# 8.2 Authentication Methods and Choosing a Solution

> **Goal of this section**: Understand how mainstream authentication methods work and where they fit best, choose the right auth library for your project, and let AI help you finish the setup.

---

## After Logging In, How Does the Server Remember Me?

In 8.0, Xiao Ming got the user system working—registration, login, and protected pages all functioned. But he had a question: every time he visits `/dashboard`, how does the server know "this request is from Xiao Ming, not someone else"? Does he have to re-enter his password every time?

This gets into **session management**—after a user logs in once, how do subsequent requests prove "I’m still me"? Every time you open a new page, click a button, or submit a form, the browser sends a new HTTP request to the server. The HTTP protocol itself is "stateless"—once the server handles one request, it forgets about it. When the next request arrives, it doesn’t know whether it’s from the same person. So you need a mechanism that lets the server "remember" who you are across multiple requests. There are two mainstream approaches in the industry.

**The first is Session.** Imagine borrowing a book from a library. The first time you go, the front desk issues you a library card with only an ID number on it. The library’s computer system records your name, borrowing history, and other information associated with that number. After that, whenever you visit, you show the card, and the front desk looks up the number to know who you are. Session works the same way: after a successful login, the server creates a session record in the database (like that record in the library’s computer), then sends the session ID to the browser via a Cookie (like the library card). From then on, the browser automatically includes that Cookie with every request—you don’t have to do anything manually, the browser handles it for you. It’s like walking into a mall and getting a member sticker at the entrance; after that, every store on every floor can see the sticker—you don’t have to show your membership card again each time. When the server receives a request, it looks up the user information associated with the session ID in the Cookie and knows "this is Xiao Ming." The advantage is full server-side control—if you want to log a user out immediately, just delete the session record from the database. On the next request, the server won’t find it, and the user is kicked out. The downside is that every request requires a database lookup. If your app is deployed across multiple servers, they also need to share session storage (otherwise, if the user logs in on server A and a later request goes to server B, the session won’t be found).

**The second is Token (usually JWT).** Here’s another scenario: you go to an amusement park, and at the entrance they stamp the back of your hand. The stamp uses invisible ink to encode your name and ticket type (regular/VIP). Staff at each ride entrance can shine a UV light on it and read the information without calling the main gate to verify. Token works similarly: after a successful login, the server signs and encrypts user information (username, role, expiration time, etc.) into a token string and sends it to the browser. After that, the browser includes that token with every request. When the server receives it, it verifies the signature, confirms it hasn’t been tampered with, and reads the user info directly from the token—no database lookup needed. The advantage is that no server-side storage is required, so it naturally supports multiple servers (because all the information is inside the token, and any server can verify it). The downside is that once issued, it can’t be revoked—until it expires, the token remains valid, just like the amusement park stamp: once it’s there, it won’t come off right away, and staff can’t remotely invalidate it. Even if you want to force a user to log out immediately, you can’t do it (unless you maintain a "blacklist," but then you’re back to needing server-side storage).

For most Web apps, you don’t need to make this choice yourself—**the auth library will handle it for you**. Better Auth uses database-backed Sessions by default (giving you both security and control), so you don’t need to manually manage Cookies or Tokens. The point of understanding these two approaches is this: when you see AI-generated code containing terms like `session`, `token`, `JWT`, or `Cookie`, you’ll know what they mean instead of feeling lost.

<AuthMethodComparison />

## More Than Passwords: Modern Authentication Methods

Password login is the most basic option, but it’s not the only one. When Xiao Ming’s friends tried "Personal Douban," their first reaction was: "I have to register again? I’ve already lost track of how many passwords I have. Can’t I just log in with my GitHub account?" That need leads to several mainstream modern authentication methods.

<ModernAuthMethods />


**OAuth 2.0** is the technology behind the "Sign in with Google/GitHub" buttons you see across the web. Click once, you’re sent to Google’s page to approve access, then automatically redirected back to the original site—already logged in. The idea is like checking into a hotel: you don’t have to prove to the hotel that your ID is real—you show your ID, and the hotel verifies it with the government’s identity system. In OAuth, Google/GitHub is that "identity authority," and your website is the "hotel." The user doesn’t give their password to your website. Instead, Google vouches for them: "This person really owns xxx@gmail.com." For users, it means no new password to remember and one-click login. For developers, it means no need to handle password storage and encryption—the security burden is shifted to Google/GitHub. After Xiao Ming added GitHub OAuth login to "Personal Douban," the registration rate noticeably improved—his friends no longer had to enter an email, think up a password, and wait for a verification email. One click, and they were in.

> Tell the AI: "Add GitHub OAuth login to my Better Auth configuration." You’ll need to first create an OAuth App on GitHub, get the Client ID and Secret, and put them in `.env`.

**Passkeys** are the future of passwordless authentication. When you unlock your phone, you tap your fingerprint or glance at the screen. Passkeys bring that experience to website login—using your fingerprint, face, or a hardware key instead of a password. Why call it the future? Because it eliminates all password-related problems at the root: no password means no password leaks, no phishing attacks (because a Passkey is bound to a specific website domain, so a fake site like `g00gle.com` can’t trigger the Passkey for `google.com`), and users don’t need to remember anything. Better Auth has built-in support for Passkeys. If your users mostly use modern devices (most phones and computers made after 2022 support them), this is worth considering.

**Magic Link** is another passwordless option: enter your email, receive an email, click the link, and you’re logged in directly. No password to remember, no registration flow to go through. Later, Xiao Ming built an internal tool for a few friends and used Magic Link—they might log in only once every few weeks, so forgetting a password is completely normal. Enter your email, click the link, and you’re in—the whole process takes less than 30 seconds. It’s a great fit for low-frequency tools and utility products. The downside is that it depends on email—if the message is delayed or lands in spam, the user experience suffers.

There’s also **SSO (Single Sign-On for enterprises)**—one account to access all company systems. You log in to your company email, then open OA, CRM, and project management tools without logging in again. This is standard in enterprise scenarios, but personal projects usually don’t need it.

## Why You Shouldn’t Build Authentication Yourself

In 8.0, Xiao Ming got Better Auth’s user system working, but he wondered: why not build it myself? Isn’t login just "query the database and compare the password"? He tried implementing a simple version himself: store passwords in the database at registration, then look them up and compare them at login. It worked, and the code wasn’t even complicated—just a few dozen lines. He even felt a little proud—this seems pretty simple, so why does everyone say not to do it yourself?

But an experienced developer took one look at his code and listed a whole string of problems he hadn’t considered: are the passwords stored in plain text? If the database is compromised, every user’s password is exposed immediately. What hashing algorithm are you using? MD5 has been insecure for years, and what cost factor did you set for bcrypt? Where is the Session stored? Did you set the Cookie attributes `HttpOnly`, `Secure`, and `SameSite`? Each of these guards against a different class of risk: `HttpOnly` prevents JavaScript from reading the Cookie (stopping XSS from stealing it), `Secure` ensures it is only sent over HTTPS (protecting against network eavesdropping), and `SameSite` restricts Cookies from being sent with cross-site requests (helping prevent CSRF attacks). Miss even one, and you have a security hole. Do password reset links expire? Are Tokens single-use? If a reset link never expires, an attacker who intercepts it once can keep using it. Is there a limit on failed login attempts? Without one, an attacker can script thousands of password guesses per second, and brute-force attacks become only a matter of time. Did you implement CSRF protection?

After hearing all that, Xiao Ming gave up on the idea of writing it himself. Every one of those details is a security risk, and they’re interconnected—missing even one can get you compromised. The few dozen lines of code he wrote looked like they "worked," but to a security expert, they were full of holes. **Do not write your own authentication logic**—this is a hard rule in software development. It’s not because you can’t write it, but because in security, "the devil is in the details." Mature auth libraries have already stepped on all the landmines for you and have been battle-tested across countless real-world projects.

## How to Choose an Auth Library

There are several mainstream options: **Better Auth** is open source and free, stores data in your own database, has native TypeScript support, integrates seamlessly with Drizzle, and includes advanced features like 2FA, Passkeys, and organization management—great for projects that need full control. **NextAuth (Auth.js)** is also open source and free, but its type support is average and Drizzle requires an extra adapter, so it’s better suited for simpler third-party login scenarios. **Clerk** provides complete UI components out of the box, but it isn’t open source (though it has a free tier), and data is stored with a third party, making it a good fit for projects that need to launch quickly and don’t mind the cost. **Supabase Auth** integrates deeply with Supabase and supports RLS (row-level security), but it doesn’t support Drizzle, so it’s best for projects already using Supabase.

The core reason this tutorial chooses Better Auth is **data ownership**—your user tables and session tables live in your own PostgreSQL, without depending on a third-party service or getting locked into a platform. Also, the Auth.js (NextAuth) team has now joined Better Auth, and Better Auth is officially recommended as the first choice for new projects. Auth.js is now in maintenance mode—it will still receive security patches, but no major new features. **For new projects, use Better Auth directly**. If you already have an Auth.js project that’s stable in production, there’s no need to rush a migration.

::: tip Load a Skill to Help AI Understand Better Auth Better
Before asking AI to configure your authentication system, load the `better-auth-best-practices` Skill. Once loaded, the AI will automatically follow Better Auth best practices—Session management strategy, plugin configuration order, secure defaults, and more will all be more standardized. With the Skill enabled, the AI is much less likely to make mistakes like "forgetting to configure CSRF protection" or "setting the Session expiration too long."
:::

When configuring the authentication system, give the AI instructions like this:

> "Integrate Better Auth into my Next.js project using Drizzle ORM and PostgreSQL. I need: email/password signup and login, GitHub OAuth login, logout functionality, and a protected /dashboard page (redirect unauthenticated users to /login)."

The AI will help you complete: dependency installation, environment variable setup, database table creation, Middleware-based route protection, and login/registration page generation. You only need to fill in the OAuth keys in `.env`. If you need more features later, such as two-factor authentication (2FA) or organization management, Better Auth’s plugin system makes these extensions very easy—just tell the AI what you want, and it will configure the right plugins for you.

## Another Kind of Authentication in Development: Public/Private Keys

Besides Web login, there’s another authentication method you’ll encounter in development: **public/private keys**. When you use `git push` to send code to GitHub, how does GitHub know it’s you? Through SSH keys—your computer has the private key (known only to you), and GitHub stores the corresponding public key. When you push code, a successful key match proves your identity. There’s also GPG signing, which adds an "authenticity seal" to your Git commits. When others see the `Verified` badge on your commit, they know it really came from you and wasn’t forged by someone else. You’ll run into these when setting up your development environment. If you haven’t configured them yet, tell the AI: "Help me generate an SSH key and configure it for GitHub."

---

::: tip Key takeaways from this section
- Session stores data on the server and offers stronger control; Token stores no data on the server and offers better scalability—the auth library will usually make the choice for you
- OAuth lets users sign in with existing accounts, and Passkeys are the future of passwordless authentication
- Don’t write your own authentication logic—use a mature auth library
- For new projects, Better Auth is the top choice; load the `better-auth-best-practices` Skill to improve AI output quality
:::

::: info Next step
Now that you’ve chosen an authentication method, move on to [Route Protection and Access Control](./03-route-protection.md)—learn how to use Middleware to guard every route and keep unauthorized requests out.
:::