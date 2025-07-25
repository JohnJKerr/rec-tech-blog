---
draft: false
title: "Keeping your state in sync: Bullhorn API Placement change detection"
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

Fortunately Bullhorn is among the better rec-tech APIs when it comes to change, so let's get started.

## Step 1: Your Systems

You will have various concerns depending on what you're building, but you will definitely care about these benefits of using a **unified model**:

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

Let's keep things simple for now, and we'll go into more detail in future posts.
You might have a model for a placement in your system that looks a little bit like this:

```csharp
public class Placement
{
  public string SourceId { get; private set; }
  public DateOnly StartDate { get; private set; }
  public Amount Fee { get; private set; } = new Amount("GBP", 0);

  public record Amount(string Currency, decimal Value);

  public void SetFee(Amount amount)
  {
      Fee = amount;
  }

  public void SetStartDate(DateOnly date)
  {
      StartDate = date;
  }
}
```

To keep your internal Placements in the same state as those on Bullhorn you will need to know when the last time you looked at Bullhorn was:

```csharp
public class BullhornIntegration
{
  public Guid Id { get; private set; }
  public DateTime LastExecutedAt { get; private set; }
}
```

We've now defined what state we need for our systems, and we've defined where we can track when the last time we executed our integration was. Onward!

## Step 2: Getting the data

We need to query the placements collection to get our data. Here's what your query might look like:

```
GET {your-bullhorn-url}/query/Placement?count=200&start=0&fields=id,customText22,dateBegin,dateLastModified,flatFee
```

You're probably very familiar with this, but if not, things to note here are:

- ✅ **Include the fields you need:**

  - The `fields` section of the querystring tells Bullhorn which fields you'd like include on your placements.
  - `dateBegin` will be our `StartDate`
  - `flatFee` will contain our fee
  - `customText22` will contain our currency here. Problems with currencies? Check our <a href="/posts/bullhorn-currencies">other post</a> on this!
  - `dateLastModified` will let us work our magic...

- ✅ **The structure of the data:**
  - Your json response will have this structure:
    ```json
    {
      "id": 156626,
      "flatFee": 13200.0,
      "dateBegin": 1753675200000,
      "dateLastModified": 1752187762203,
      "customText22": "USD"
    }
    ```
  - Dates are returned in Unix timestamp format, and you'll need to convert them to your desired format. In our case (C#) we can use: `DateTime.UnixEpoch.AddMilliseconds(1753675200000)`

From this we can populate our unified model, but how can we best update our models when Bullhorn changes? Step 3!

## Step 2: Change is coming...

Too soon (still)?
![alt text](change-is-coming.png)

When things change on Bullhorn you want to update your systems? Earlier we defined `LastExecutedAt` on our integration. We can use that to filter our query to only include placements that were last modified after the last time we executed the integration.

```http
GET {your-bullhorn-url}/query/Placement?count=200&start=0&where=dateLastModified > {your-last-executed-date}&fields=id,customText22,dateBegin,dateLastModified,flatFee
```

As always when using the Bullhorn API you'll need to convert to Unix timestamps for dates. In C# we could do this like so:

```csharp
var unitTimeMilliseconds = new DateTimeOffset(integration.LastExecutedAt).ToUnixTimeMilliseconds();
```

You're left at this point to update your unified models, with whatever you're interested in that may have changed.

Last of all - make sure you update the `LastExecutedAt` date on your integration to this execution.

Of course, this says nothing of managing your own codebase, infrastructure, correctness and keeping all of this backwards compatible...

### Hic sunt dracones 🐉!
