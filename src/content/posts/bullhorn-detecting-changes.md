---
draft: true
title: "Keeping your system in sync: Bullhorn API change detection"
snippet: "Your systems need to know what's changed on your Bullhorn integration. How to go about it?"
image:
  {
    src: "https://images.unsplash.com/photo-1608431727066-629d1d55dfde",
    alt: "time for change",
  }
publishDate: "2025-07-14 11:18"
category: "bullhorn"
author: "Anth Richardson"
tags: [bullhorn, state management]
---

If your systems are consuming data from many rec-tech source APIs, chances are you're going to want a consistent model on your
end so that you can rely on that, rather than a plethora of disparate and very different source models.

One of the first problems you're going to run into: how do I know when I need to update?

At a glance, not a particularly hard problem - but with each non-standardised API you need to integrate with: <a href="https://english.stackexchange.com/questions/37086/what-is-the-meaning-of-the-phrase-here-be-dragons" target="_blank">_hic sunt dracones_</a>.

Fortunately Bullhorn is among the better rec-tech APIs, so let's get started.

## Step 1: Your Systems

You will have certain things you care about depending on what you're building, but you will definitely care about these benefits of using a **unified model**:

- ✅ **Reduced Coupling and Vendor Lock-in:**

  - Your application becomes independent of specific API schemas.
  - If you need to switch providers or add new ones, there's no need for refactoring code throughout your system.

- ✅ **Simplified Business Logic:**

  - No writing conditional logic that handles multiple API formats throughout your application.
  - Mapping is all handled at the integration boundary.
  - Your core business logic works with a single, predictable data structure.

- ✅ **Future-Proofing:**
  - As APIs evolve or new ones are added, your internal model serves as a stable contract for your application.
  - As your application evolves, it can move separately to the integration concerns in the mapping layer.
