---
title: "15.1 Open Graph and Social Sharing"
description: "Make your links stand out on social platforms"
---

# 15.1 Open Graph and Social Sharing

> If you share a carefully designed website link on social media and it only shows up as a blue link with no image preview, the click-through rate will be very low. The Open Graph protocol exists to solve exactly this problem.

---

## Xiaoming's Blue Link

Xiaoming excitedly dropped his website link into the group chat: "Check out the project I built!"

But the chat only showed a blue URL. No title, no image, no description. It was like posting `https://example.com/app` into the chat—who would know what that is? His friends glanced at it and kept talking about other things.

Xiaoming wasn't satisfied, so he posted it again and added, "It's really useful!" But a link with no visual appeal is like a drop of water falling into the ocean in a crowded feed—it disappears instantly.

The old hand saw it and said: "You're missing Open Graph."

---

## What Is Open Graph

![image-20260303152540838](/images/Advanced/image-20260303152540838.png)

Have you ever noticed that when some links are shared on QQ, they automatically expand into a nice-looking card—with a title, description, and cover image—while others are just a string of blue text?

The difference is the **Open Graph (OG)** protocol.

OG is a metadata standard introduced by Facebook in 2010. The idea is simple: you put a few specific `<meta>` tags inside the HTML `<head>` of your page, and when social platforms crawl your link, they read those tags and use them to generate a preview card.

X, LinkedIn, Telegram, Discord, Facebook, QQ... most major social platforms support OG. This is not a private feature of any one platform, but a widely adopted open standard.

One important caveat: **WeChat does not support the OG protocol**. WeChat has its own sharing mechanism, and you need to use the WeChat JS-SDK to customize the title, description, and image in the share card. If most of your users are in the WeChat ecosystem, OG won't help you—you need to handle it separately. We'll mention this again later.

| With OG Tags | Without OG Tags |
|-----------|-----------|
| Polished card preview | Only a blue link |
| Title + description + image | Only a URL |
| High click-through rate | Low click-through rate |

This comparison is not exaggerated. Based on social media marketing data, links with image previews usually get 2-5 times the click-through rate of plain text links. The reason is simple—people are visual creatures, and an attractive card grabs attention much more easily than a string of characters.

### Core OG Tags

The OG protocol defines a set of tags, and these five are the most important:

| Tag | Description | Example |
|------|------|------|
| `og:title` | Title of the shared card | "My Product Name" |
| `og:description` | Description of the shared card | "A one-line summary of the product's value" |
| `og:image` | Image URL for the shared card | `https://...` |
| `og:url` | Canonical URL of the page | `https://...` |
| `og:type` | Content type | `website` or `article` |

These tags are placed in the HTML `<head>` in the form `<meta property="og:xxx" content="xxx" />`. When someone shares your link on a social platform, the platform's crawler visits the URL, reads the OG tags in the `<head>`, and renders a card from them.

How do you actually configure it? Tell Claude Code that you need OG tags configured, and it will handle it automatically based on your framework. For Next.js users, once you've loaded the `next-best-practices` Skill, setting up metadata becomes effortless—Next.js's Metadata API has first-class support for OG.

---

## Xiaoming Configured OG

Xiaoming asked Claude Code to add OG configuration to his project. Claude Code set up the `openGraph` field inside the `metadata` object in `layout.tsx`, filling in the title, description, and image.

A few minutes later, Xiaoming shared the link in the group chat again.

This time it was different—the message expanded into a polished preview card. The title stood out, the description clearly explained what the product did, and there was even a nice cover image. The whole thing looked like a real product, not just a random link someone tossed into the chat.

"This looks good—let me click and check it out." His friends started clicking.

Watching the growing number of "read" markers under the message, Xiaoming finally understood what the old hand meant: **OG optimization isn't optional—it's foundational marketing infrastructure**. You may have spent months building your product, but if the first impression people get when it's shared is just a string of blue text, that's like carefully decorating a storefront and forgetting to hang the sign.



---

## OG Images: The Visual Core of Share Cards

Among all OG tags, `og:image` has the biggest impact. People's eyes are drawn to the image first, and only then do they read the title and description. A good OG image can be the deciding factor in whether someone clicks or scrolls past.

### Recommended Sizes by Platform

Different platforms have slightly different image size requirements, but there is one universal size: **1200 x 630 pixels**, roughly a 16:9 ratio. This size displays properly on all major platforms without getting awkwardly cropped. If you're only making one image, use this size.

### Design Principles

| Principle | Description |
|------|------|
| Keep it simple | The title should be short and highlight the core message—users will only glance at it in the feed |
| Stay on-brand | Use your brand colors and fonts so people recognize it immediately |
| Text safe area | Keep important content away from the outer 20% edges, because different platforms crop differently |
| High contrast | Make sure text is clearly readable on any background, especially on small mobile screens |

### Where OG Images Come From

Xiaoming ran into a practical issue: he's not a designer, so how could he make a good-looking OG image?

A few options:

- **Design tools**: Canva has lots of ready-made social media templates, so you can just pick one and swap in your own text. Figma is a good fit for people with some design background. This is the fastest option.
- **AI generation**: Use an AI image generation tool, describe the style and content you want, and generate a cover image. The result depends on the quality of your prompt.
- **Dynamic generation**: Next.js supports server-side dynamic OG image generation with `next/og` and `ImageResponse`. This is especially useful for blogs—each article can automatically get a cover image with its title, so you don't have to design one manually every time. If your site has a lot of pages, dynamic generation is the lowest-maintenance option.

For a single-page app like Xiaoming's, one carefully designed static OG image is enough. But if he adds a blog or product pages later, dynamic generation will be more practical.

---

## Differences Between Platforms

Xiaoming shared the same link to X, a QQ group, a WeChat group, and LinkedIn. On X, QQ, and LinkedIn, it showed up as a nice card—but in the WeChat group, it was still just a blue link.

"Why doesn't it work on WeChat?"

The old hand said: "WeChat doesn't read OG tags. It has its own system."

### X

X has its own set of `twitter:card` tags (the tag names still use the old Twitter naming), but it also falls back to reading OG tags. So if you only configure OG tags, cards will still display properly on X.

X cards come in several types:

- `summary`: small-image card, with the image on the left and text on the right
- `summary_large_image`: large-image card, with the image taking up the top of the card (recommended)

In most cases, `summary_large_image` looks better and gets a higher click-through rate. If you want precise control over how it appears on X (for example, using a different title or image than OG), you can additionally configure tags like `twitter:title` and `twitter:image`. But usually that's unnecessary—OG tags are enough.

### WeChat

WeChat is a completely separate ecosystem. It **does not support the OG protocol**—no matter how perfectly you configure your OG tags, WeChat will not read them.

WeChat share cards (the kind with a title, description, and thumbnail) need to be implemented through the **WeChat JS-SDK**. The rough process is: register on the WeChat Official Accounts platform, configure a secure domain, and call APIs such as `wx.updateAppMessageShareData` on the frontend to customize the shared content. This workflow is much more complex than OG, and it requires a verified WeChat Official Account.

For personal projects, integrating the WeChat JS-SDK is relatively expensive in terms of effort. If your users mostly live in the WeChat ecosystem, it's worth the investment; if not, configure OG first to cover other platforms, and deal with WeChat later.

### LinkedIn

LinkedIn is the platform with the strictest support for the OG standard. It renders cards closely according to OG tags and doesn't do much "creative interpretation" of its own. Just make sure the image size is correct (1200 x 627) and the URL is publicly accessible.

---

## Testing and Debugging

After configuring OG, don't test by immediately posting the link into a group chat—social platforms cache link information. If OG wasn't configured correctly the first time they crawled it, the platform may cache that "empty" result, and even after you fix it, it may still show the old version.

The right approach is to use the debugging tools provided by each platform:

| Tool | Purpose |
|------|------|
| Facebook Sharing Debugger | Verify whether OG tags are correct and force-refresh the cache |
| X Card Validator | Preview how the card looks on X |
| LinkedIn Post Inspector | Preview how the share looks on LinkedIn |

These tools don't just let you preview the result—they also tell you which tags are missing or broken. The first time Xiaoming used Facebook Debugger, he discovered that his `og:image` URL was wrong—the image path was missing a slash. Small mistakes like that are hard to spot by eye, but a debugging tool can pinpoint them instantly.

If you've updated your OG tags but the platform still shows old content, use the debugging tool's "Scrape Again" or "Fetch new scrape information" feature to force-refresh the cache.

---

## Common Questions

### Q1: What should I do if the OG image doesn't show up?

This is the most common problem, and it's usually caused by one of the following:

1. **The image URL isn't publicly accessible**—social platform crawlers can't access localhost addresses or images that require login
2. **The image format is unsupported**—only JPG, PNG, and WebP are supported; SVG is not
3. **The image is too large**—it's best to keep it under 5MB, since very large images may cause crawlers to time out and give up
4. **The URL is wrong**—check with a debugging tool, which will show you the specific error

### Q2: How do I get a card preview when sharing on WeChat?

WeChat does not support the OG protocol. You need to customize share content through the WeChat JS-SDK. This requires a verified WeChat Official Account, and the integration cost is significantly higher than OG. If your product targets users in China and WeChat is your main distribution channel, it's worth the investment; otherwise, configure OG first to cover other platforms.

---

## Xiaoming's Sharing Habit

Now every time Xiaoming releases a new feature, he does two things by reflex: checks the OG card preview, then shares it on a few social platforms. He found that a good OG image paired with an appealing description brings in more clicks than shouting about it ten times in the group chat.

Social sharing became one of the major traffic sources for his website. But Xiaoming also realized that traffic from social sharing is "bursty"—you post once, and when the buzz fades, the traffic is gone. To get steady, free traffic, you still need search engines.

When someone searches on Baidu or Google for a problem your product can solve, and your site appears in the results—that's real long-term traffic.

That's what the next section is about: SEO.