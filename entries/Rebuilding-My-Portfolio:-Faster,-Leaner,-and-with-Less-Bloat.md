Tags: #webdev #golang #htmx #performance #nextjs #optimization #backend

![Go + HTMX Architecture](https://raw.githubusercontent.com/arkorty/blog/refs/heads/main/blob/htmx-vs-react.jpeg "HTMX vs React")

**Co-authored by LLMs**

# Rebuilding My Portfolio: Faster, Leaner, and Built for the Web That Actually Exists

I recently rebuilt my portfolio website, and the project turned out to be a great reminder of how modern web stacks can feel powerful—but also unnecessarily heavy—when all you really need is speed and clarity.

My old site was built with Next.js. It started as a borrowed template and slowly grew into a bundle of features: GitHub activity widgets, sandboxed project previews, a little 3D solar system, code-typing animations, and other decorative interactions. All of it worked—just not quickly.

The site consistently took **over one second** to become interactive. For a personal portfolio, that delay felt wasteful. Most visitors aren’t looking for an immersive app; they just want clean, readable information.

You can still see the archived version at *old.webark.in*, and you’ll immediately notice the difference.

---

## Why I Moved Away From Next.js

Next.js wasn’t the villain here. It’s great for full-scale applications. But for a simple, mostly static portfolio, the abstractions—SSR, hydration, client-side bundles, routing layers, and build pipelines—added more complexity than benefit.

So I rebuilt everything using a simpler stack:

* **Go + Echo** for fast, explicit server-side rendering and routing
* **Goldmark** for Markdown processing (server-side, no client-side parsing)
* **HTMX** for small pockets of interactivity without a front-end framework
* A minimal, industrial-feel design focused on speed and readability

This alone cut load times to **~300 ms**. Pages rendered instantly, the codebase shrank dramatically, and the whole system felt easier to reason about.

---

## Adding Features Back… and Hitting a Wall

With the core structure working beautifully, I reintroduced two dynamic pieces:

* My GitHub contribution stats
* My blog feed

Almost immediately, load times jumped from ~300 ms to **~700 ms**.

That wasn’t terrible—but it *felt* like a step backward. The slowdown wasn't caused by Echo, HTMX, or Go. It was caused by *where* I was fetching data:

> I was calling external APIs during the request cycle, blocking the entire render until GitHub responded.

No HTML was sent until the GitHub API call completed. And without framework-level abstractions like caching or static generation, the delay hit the user directly.

---

## HTMX as Lightweight “Hydration”

The fix was obvious once I saw the bottleneck: don’t make the initial page load wait for remote APIs.

Instead, I shifted from **blocking SSR** to **progressive loading** using HTMX.

### Before (Blocking)

* User requests `/`
* Server fetches GitHub data
* Server renders the template
* Only then does HTML get sent back

### After (HTMX-enhanced)

* User requests `/`
* Server immediately returns static HTML (with placeholders)
* HTMX fires background requests to fetch stats
* Received fragments are swapped into the page

This change preserved the super-fast initial load **without** sacrificing dynamic data.

The page becomes visible almost instantly, and the asynchronous HTMX swaps feel natural—similar to hydration, but without any client-side runtime or heavy JS bundles.

---

## Lessons From the Rebuild

### 1. Not every site needs a full framework

For mostly-static content, a minimal backend + progressive enhancement often wins in clarity and speed.

### 2. SSR is only fast when your data is local

If your render pipeline waits on third-party APIs, you're no longer doing “fast SSR”—you’re doing “SSR with built-in delays.”

### 3. Interactivity doesn’t require an SPA

HTMX continues to prove that small, targeted interactions can replace entire client-side frameworks.

### 4. Perceived performance matters most

Sending HTML quickly has a bigger impact than many deep optimizations that happen later.

---

## The Final Result

The new portfolio is dramatically faster, simpler, and lighter.
On a slow 4G network, the old site took **over six seconds** to load.
The new one loads in about **two seconds**, even under similar conditions.

That improvement comes from:

* A lean backend
* An HTMX-powered loading model
* ~10× less data transferred compared to the previous build
* No hydration, no bundlers, no labyrinth of abstractions

The rebuild didn’t just improve the site—it clarified how I want to approach small web projects going forward: focus on speed, reduce moving parts, and keep the behavior predictable.
