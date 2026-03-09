---
title: "8.3 Route Protection and Access Control"
description: "Middleware, CORS, page-level vs. API-level protection, role-based permissions—make sure every request is checked"
chapter: "Chapter 8"
---

# 8.3 Route Protection and Access Control

> **Goal of this section**: Understand why hiding entry points on the frontend does not equal security, and learn how to use Middleware and access control to protect every route.

---

## Hiding Things on the Frontend Is Not Security

Xiaoming added an admin panel at `/admin` to his "Personal Douban" app, where he could bulk delete movies and manage user comments. He didn’t put a link to `/admin` in the frontend navigation bar, and figured that made it secure—regular users couldn’t see the entry point, so naturally they couldn’t get in.

Until one day, his friend Lao Wang said in the group chat, "Your admin panel is pretty handy. I helped you delete a few duplicate movies."

Xiaoming was startled: "How did you get in?"

Lao Wang: "I just typed `localhost:3000/admin` directly into the browser address bar, and it opened."

That’s when Xiaoming realized: **hiding an entry point on the frontend is just security through obscurity**. Leaving a link out of the navigation only makes users "not see" the entry point, but anyone can still manually type the URL into the address bar. Worse, an attacker can send requests with curl or write scripts to call your API in bulk—frontend "hiding" is meaningless against those methods. It’s like hiding your house key under a flowerpot and then covering the flowerpot with a cloth. You think people won’t find the key if they can’t see the pot, but anyone can just lift the cloth and take it. Real protection has to happen on the **server side**—not "hide the entrance," but "without the key, the door won’t open."

## Middleware—Next.js's Gatekeeper

In Next.js, you don’t need to write permission checks on every page. You just need to place a `middleware.ts` file in the project root—it acts like the site’s gatekeeper, and **every request** must pass through it before reaching a page or API. A user requests `/admin`, Middleware intercepts it and checks whether they’re logged in and whether they’re an admin. If they’re not logged in, redirect them to `/login`; if they’re not an admin, return 403—403 means "I know who you are, but you don’t have permission," which is different from 401: "Who are you? Log in first." Only if the check passes does the request continue to the `/admin` page. This check happens on the server side, so the user’s browser never receives the `/admin` page content at all—it’s not "the page loaded and then redirected," it’s "the page was never sent to you in the first place."

> Tell AI: "Create a middleware.ts that intercepts all paths starting with /admin. If the user is not logged in or their role is not admin, redirect them to the login page. Also protect all APIs starting with /api/admin."

<details>
<summary>Curious what Middleware looks like? Expand to take a look</summary>

```typescript
// middleware.ts (project root)
import { betterFetch } from '@better-fetch/fetch'
import { NextRequest, NextResponse } from 'next/server'

export async function middleware(request: NextRequest) {
  // Check if the user is logged in
  const { data: session } = await betterFetch('/api/auth/get-session', {
    baseURL: request.nextUrl.origin,
    headers: { cookie: request.headers.get('cookie') || '' },
  })

  if (!session) {
    return NextResponse.redirect(new URL('/login', request.url))
  }

  return NextResponse.next()
}

// Specify which paths need protection
export const config = {
  matcher: ['/dashboard/:path*', '/admin/:path*'],
}
```

</details>

Middleware doesn’t just protect pages—it can protect APIs too. With the `matcher` configuration, you can precisely control which paths need checking—`/dashboard/:path*` protects all dashboard pages, `/admin/:path*` protects the admin panel, and `/api/admin/:path*` protects admin APIs. `:path*` is a wildcard that means "all subpaths under this path," so `/admin/users` and `/admin/settings` will both be protected. Any path you don’t want to protect (home page, login page, signup page, public APIs) can simply be left out of `matcher`—Middleware only intercepts the paths you specify, and lets all others pass through directly.

<MiddlewareFlow />

## Page Protection and API Protection Are Both Required

Xiaoming added Middleware to protect the `/admin` page. He thought things were secure now—users trying to visit `/admin` in a browser would be blocked. But then an experienced developer asked him: "Have you also protected your movie deletion endpoint at `/api/admin/delete-movie`?"

Xiaoming said, "Doesn’t Middleware already block paths starting with `/admin`?"

The experienced developer replied, "Middleware intercepts page requests. But if someone directly calls `POST /api/admin/delete-movie` with curl and includes a valid user Cookie, Middleware might not stop it—that depends on your matcher configuration. And even if Middleware does block it, the API itself should still have its own permission checks. That’s the principle of **defense in depth**."

Why do you need both layers? Because Middleware is like the "main entrance," but each room behind that entrance should still have its own lock. Even if the page is protected, an attacker can still bypass the browser and call the API directly with curl or Postman—it’s like skipping the mall’s front entrance and sneaking into the warehouse through the employee entrance. The security check you placed at the main entrance does nothing there. If the API has no permission checks of its own, then as long as the attacker gets a valid user Cookie, they can delete data, change settings, or even escalate privileges using curl. Page-level protection relies on Middleware interception plus server-side checks; API-level protection relies on validation inside the API Route itself—both layers are controlled on the server side, and neither can be bypassed. This is what security professionals mean by **defense in depth**: don’t put all your eggs in one basket, and make sure every layer has its own protection.

<DefenseInDepth />

> Tell AI: "Make sure all APIs starting with /api/admin check user permissions internally in code, and do not rely only on Middleware."

## CORS—The Browser’s Cross-Origin Security Mechanism

<CORSMechanism />

Later, Xiaoming wanted to add a feature to "Personal Douban": fetching movie posters from Douban’s public API. He wrote a `fetch('https://api.douban.com/...')` call in the frontend, and the browser console immediately showed a red error: "Cross-origin request blocked (CORS policy)." He was confused—the API returned data just fine when opened directly in the browser address bar, so why didn’t it work when called from JavaScript?

This is not a bug—it’s the browser’s **security mechanism**. Imagine this: you’re logged into your bank’s website, and your browser is storing your login Cookie. Then you open a malicious website. Without cross-origin restrictions, the malicious site’s JavaScript could secretly send requests to the bank’s website—because the browser would automatically include your bank Cookie, and the bank server would think it was really you making the request. The attacker could then transfer money while impersonating you. CORS (Cross-Origin Resource Sharing) is the browser’s line of defense: **by default, JavaScript running on a webpage is not allowed to make requests to a different domain unless the target server explicitly allows it**. Directly visiting a URL in the browser address bar is unrestricted (that’s an intentional action by the user), but requests made by JavaScript code inside a webpage are blocked (because they could be malicious code acting in secret).

When do you run into CORS? If your frontend and backend are in the same Next.js project (as in this tutorial), and the frontend calls its own API Route, you won’t run into CORS because it’s same-origin. But if the frontend and backend are deployed separately—frontend at `app.example.com` and API at `api.example.com`—then you will. You’ll also hit it when calling third-party APIs directly from the frontend. If your frontend and backend are in the same Next.js project, you usually don’t need to configure CORS. If you really do need cross-origin access, tell AI: "My frontend is at `https://app.example.com` and needs to call APIs at `https://api.example.com`. Help me configure CORS so only this domain is allowed."

::: warning Do not configure `Access-Control-Allow-Origin: *`
Allowing all domains is effectively the same as having no CORS protection. Always specify the exact domain.
:::

## Role-Based Access Control

<RBACMatrix />

Middleware solves the "is the user logged in?" problem, but Xiaoming quickly ran into a new one: after logging in, Lao Wang could still access the `/admin` page—because Middleware only checked whether the user was logged in, not whether they were an administrator. Xiaoming’s app had two kinds of users: he himself was an admin who could delete movies and manage comments; his friends were regular users who could only browse movies and write comments. This is where **role-based access control (RBAC)** comes in—different roles can do different things.

The simplest implementation is to add a `role` field to the `user` table: Xiaoming’s role is `admin`, and Lao Wang’s role is `user`. Then in Middleware or the API, check `session.user.role !== 'admin'` and return 403 if true. This check can go in Middleware (for centralized interception), or in each API Route (for fine-grained control), but ideally it should be in both places—again, defense in depth.

As your application grows more complex, having only `admin` and `user` may not be enough. For example, if Xiaoming’s "Personal Douban" becomes popular and gets a few hundred users, he may want a few trusted friends to help moderate the comments section. At that point, you need more granular roles: `guest` can only browse public content, `user` can browse, post, and edit their own content, `moderator` has all `user` permissions plus the ability to remove inappropriate content, and `admin` has all permissions. Better Auth’s organization management plugin supports more advanced permission models, but for most personal projects, `admin` and `user` are enough. Don’t overengineer—expand only when you actually need to. When the time comes, tell AI what you need, and it can help you adjust the permission model.

> Tell AI: "Help me implement a complete route protection solution: create middleware.ts to protect /dashboard and /admin paths, require users accessing /admin to also have the admin role, make all APIs starting with /api/admin check permissions internally in code as well, redirect unauthenticated users to /login, and redirect logged-in users away from /login to /dashboard."

---

::: tip Key takeaways from this section
- Hiding entry points on the frontend is not a security measure; real protection happens on the server side
- Middleware is Next.js’s unified interception layer, and `matcher` defines what gets protected
- Page protection and API protection are both essential
- CORS is a browser security mechanism, and usually doesn’t need configuration within the same project
- Start role-based access control with simple `admin`/`user` roles, and expand only when needed
:::

::: info Next step
Now that your routes are protected, continue to [Security Checks and Troubleshooting](./04-security-checklist.md)—a practical security checklist for every stage of development, plus a quick troubleshooting guide for when issues come up.
:::