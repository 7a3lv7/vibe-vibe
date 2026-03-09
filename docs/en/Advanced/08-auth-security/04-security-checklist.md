---
title: "8.4 Security Checks and Troubleshooting"
description: "Security checklists for before/during/after development, plus symptoms → causes → solutions for common security issues"
chapter: "Chapter 8"
---

# 8.4 Security Checks and Troubleshooting

> **Goal of this section**: Build security awareness for each stage of development so you can quickly locate and fix issues when they arise.

---

## Security Checklist

After going through a leaked secret key, an exposed admin dashboard, and CORS errors, Xiaoming asked the mentor, "Is there a checklist I can follow every time I develop, so I stop making the same mistakes?"

The mentor said, "Yes. Security isn't something you rush through once before launch and then forget. It runs through the entire development lifecycle—from the very first second you create the project."

**Before development—lay a solid foundation.** Xiaoming thought back on his experience: if he had configured `.gitignore` from the start, the secret key wouldn't have leaked; if he had chosen Better Auth from the beginning, he wouldn't have considered writing authentication himself. The root cause of many security issues is "not getting it right from the start." If you wait until something goes wrong to patch it, the cost is far greater than doing it correctly in the first place. At this stage, confirm three things: is `.gitignore` configured properly? Make sure `.env`, `node_modules/`, and `.next/` are all on the ignore list—this should be the first thing you do after creating a project, even before writing your first line of business code. Did you create `.env.example`? List all required environment variable names, leave the values empty, and commit it to Git—this is a signpost for your future self and any collaborators you may have. Have you chosen an authentication solution? Use a mature library like Better Auth; don't write your own—Section 8.2 already explained why.

> Tell AI: "Help me check whether my project's .gitignore is complete and whether any sensitive files are being tracked by Git."

**During development—check as you go.** The easiest mistake to make during development is "just get it working first"—hardcode the key for now, skip route protection for now, skip input validation for now. "I'll come back and fix it after the feature is done," but you never do. This is exactly how Xiaoming gradually accumulated security debt until it all exploded at once—a leaked key, an unprotected admin dashboard, and unvalidated user input. Every one of these was a trap left behind by "just get it working first." At this stage, you need to build the habit of checking as you go: are all secrets stored in `.env`? Search your code for keywords like `sk-`, `password`, and `secret` to see whether anything is hardcoded. Is the Server/Client boundary clear? Handle sensitive data only on the server, and prefix frontend variables with `NEXT_PUBLIC_`. Is route protection in place? Use Middleware plus an internal API double-check. Is user input being validated? Use Zod on the backend to validate all input, and never trust any data sent from the frontend—frontend validation is for user experience, backend validation is for security. Did you restart after changing `.env`? Environment variables are loaded only at startup, so you need to restart after making changes.

> Tell AI: "Review my code and find all hardcoded secrets, API endpoints missing input validation, and routes without permission checks."

**Before launch—the last line of defense.** Xiaoming's app was about to be deployed. He thought all he had to do was push the code, but the mentor said, "There are still a few things you must check before going live, or you'll be exposing your app to the world." Do your dependencies have vulnerabilities? Run `pnpm audit` and fix high-severity issues—your project may indirectly depend on hundreds of packages, and any one of them could have known vulnerabilities. Are production environment variables configured properly? Set them one by one in your deployment platform's environment variable page, and note that some values differ between local and production environments (for example, `BETTER_AUTH_URL` is `http://localhost:3000` locally, but in production it should be your domain). Is HTTPS enabled? Platforms like Vercel and Railway enable it by default, but self-hosted servers need manual configuration—without HTTPS, user passwords and cookies are sent over the network in plain text and can be intercepted by anyone. Are error messages safe? In production, don't return stack traces; return only friendly error messages instead—stack traces can expose your code structure, file paths, and even database table names, all of which are valuable intelligence for attackers.

**After launch—ongoing maintenance.** Launching your app isn't the finish line; it's the start of a new phase. Xiaoming's app had been running for three months when one day he received a GitHub security alert email: "Your project dependency `lodash@4.17.20` has a prototype pollution vulnerability." He was confused—he had never installed that package directly. After investigating, he discovered that one of the packages he installed depended on another package, which in turn depended on the vulnerable one. That's how the npm ecosystem works—your project may indirectly depend on hundreds of packages, and any of them can become a problem. At this stage, you should: rotate secrets every 3-6 months (even if nothing has leaked, regular rotation is a good habit), run `pnpm audit` monthly to check for dependency vulnerabilities, and watch for unusual logins (the same account logging in from different regions within a short time), frequent failed requests (possibly brute-force attacks), and sudden spikes in API calls (possibly someone abusing your endpoints).

---

## Troubleshooting Guide

Security-related errors are often confusing—the error messages aren't intuitive, and the real cause may be somewhere you wouldn't expect. Every pitfall Xiaoming ran into became an entry in this guide. Hit a problem? Look it up by symptom.

**`.env` is configured but can't be read.** The symptom is that `process.env.XXX` returns `undefined`. Restart first—environment variables are loaded only at startup, so stop the service with `Ctrl+C` and run `pnpm dev` again. In 90% of cases, that solves it. If it still doesn't work, check whether the variable name matches exactly (including case), make sure the `.env` file is in the project root (not inside `src/`), and check for extra spaces or quotes—quotes are not needed in `.env`; `DATABASE_URL=postgresql://...` is correct, while `DATABASE_URL="postgresql://..."` may cause problems.

**Readable on the Server but not on the Client.** The symptom is that the environment variable is available in an API Route but shows up as `undefined` in a React component. The cause is a missing `NEXT_PUBLIC_` prefix—this is part of Next.js security behavior, which does not send environment variables to the browser by default. If the value truly needs to be used on the frontend and is not sensitive, rename it to `NEXT_PUBLIC_XXX`. If it's sensitive (like an API Key), it should not be used on the frontend at all—call it through an API Route instead.

**Cross-origin error (CORS).** The symptom is that the browser console shows `Access to fetch at 'xxx' has been blocked by CORS policy`. If your frontend and backend are in the same Next.js project, you shouldn't normally see CORS issues—check whether the request URL is wrong (for example, using the full `http://localhost:3000/api/...` instead of the relative path `/api/...`). If you're calling an external API, you need to configure CORS on the backend to allow access from your domain. Sometimes this is specific to the development environment, so check the `rewrites` configuration in `next.config.js`.

**`.gitignore` is configured but files are still being tracked.** The symptom is that `.env` has been added to `.gitignore`, but `git status` still shows it. The reason is that the file was already tracked by Git before being added to `.gitignore`—`.gitignore` only applies to files that have **never been tracked**. The fix is to run `git rm --cached .env` to remove it from the index, then commit again.

**A secret key has already leaked.** The symptom is that the key was committed to GitHub (whether in a public or private repository). Here are the four emergency steps: immediately go to the relevant platform (OpenAI, Supabase, GitHub, etc.) and regenerate the key; update `.env` with the new key; tell AI, "My .env file was committed into Git history. Help me completely remove it using git filter-branch or the BFG tool"; and check your billing for unusual charges.

::: danger Deleting the file and committing is not enough
Running `git rm .env` and then committing only removes the file from the latest version. Every older version in Git history still contains the file. Anyone can find it with `git log`. You must rewrite the history.
:::

**Still redirected to the login page after logging in.** The symptom is that login succeeds, but visiting a protected page still redirects you to `/login`. First, check whether the cookie was set correctly—open browser F12 → Application → Cookies and see whether a session cookie exists. Then make sure the domain is consistent—use `http://localhost:3000` in development, not `http://127.0.0.1:3000`. Cookies are bound to domains, and different domains do not share cookies. Finally, check the `matcher` configuration in Middleware to make sure `/login` itself is not protected (the login page should not be protected).

You don't need to memorize every item on this checklist. Let AI review your project regularly:

> "Review my project's security posture: check for hardcoded secrets, whether .env is being tracked by Git, whether dependencies have known vulnerabilities, and whether all protected routes have permission checks."

---

::: tip Key takeaways from this section
- Execute the security checklist by phase: configure `.gitignore` before development, check boundaries and validation during development, audit dependencies before launch, and rotate regularly after launch
- 90% of `.env` issues can be fixed by restarting
- `.gitignore` only applies to untracked files; for tracked files, run `git rm --cached` first
- After a secret leaks, you must reset the key + clean Git history; deleting the file alone is not enough
:::

::: info Next step
Now that you have both the checklist and the troubleshooting guide, continue to [Advanced Security Protection](./05-advanced-security.md) to learn about deeper security threats such as SQL injection, XSS, and CSRF, as well as security issues specific to AI applications.
:::