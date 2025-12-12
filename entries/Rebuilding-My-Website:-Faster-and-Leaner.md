Tags: #webdev #golang #htmx #performance #nextjs #optimization #tech

![HTMX vs React](https://raw.githubusercontent.com/arkorty/blog/refs/heads/main/blob/htmx-vs-react.jpeg "HTMX vs React")

**Proofread by LLM**

# Rebuilding My Website: Faster and Leaner

I recently rebuilt my website, and the project reminded me how modern web stacks can feel powerful—but also unnecessarily heavy—when all you really need is speed and clarity.

My old site was built with Next.js. It started as a borrowed template and gradually grew into a bundle of features: GitHub widgets, sandboxed project previews, a small 3D solar system, typing animations, and other effects. All of it worked—just not quickly.

The site consistently took **over one second** to become usable. For a personal website, that delay felt wasteful. Most visitors aren’t looking for an immersive app; they just want clean, readable information.

You can still see the archived version [here](https://old.webark.in).

---

## Why I Moved Away From Next.js

Next.js wasn’t the villain; it’s great for full-scale applications. But for a mostly static website, the abstractions—SSR, hydration, client bundles, routing layers, and build pipelines—added more complexity than benefit.

So I rebuilt everything using:

* **Go + Echo** for fast, explicit server-side rendering and routing  
* **Goldmark** for Markdown processing  
* **HTMX** for small pockets of interactivity  
* A minimal, industrial-feel design focused on readability

This cut load times to **~300 ms** and made the system far easier to maintain.

---

## Adding Features Back… and Hitting a Wall

After reintroducing two dynamic parts—my GitHub stats and blog feed—load times increased to **~700 ms**.

The slowdown came from one source:

> The server waiting for external APIs before sending any HTML.

This removed the benefits of fast SSR and added unnecessary latency.

---

## HTMX as Lightweight AJAX

To fix this, the page needed to load immediately without waiting for remote APIs. HTMX made this trivial.

### Before (Blocking)

* User requests `/`  
* Server fetches GitHub data  
* Server renders template  
* HTML is finally sent  

### After (HTMX + AJAX)

* Server instantly returns static HTML with placeholders  
* HTMX makes background AJAX requests  
* Returned fragments are swapped in  

This preserves fast first paint while still supporting dynamic data — without hydration or a client-side runtime.

---

## Data Transfer: A Dramatic Difference (But Not Apples-to-Apples)

The new site also transfers far less data on first load:

* **Old website:** ~**670 kB**  
* **New website:** ~**129 kB**  

The comparison **isn't apples-to-apples**—the old site had heavier visuals and more interactive components.  
But the reduction still highlights how much unintentional overhead frameworks can accumulate.

---

## Lessons From the Rebuild

1. Minimal backends + progressive enhancement are often ideal for static-content sites.  
2. SSR is only fast when your data is local; external APIs undo its benefits.  
3. Small, targeted interactions don’t require an SPA.  
4. Perceived performance matters most—send HTML quickly.

---

## The Final Result

The new website loads in about **300 ms** on a fast connection; the old one often took **2+ seconds**, and over **6 seconds** on slow 4G. The new approach stays under **2 seconds** on similar networks.

These improvements come from:

* HTMX-powered AJAX  
* Much smaller data transfer (129 kB vs 670 kB)  
* No heavy client-side runtime or bundler overhead  

I am rethinking how I want to build simple websites going forward: prioritize speed, reduce moving parts, and keep the bloat out.
