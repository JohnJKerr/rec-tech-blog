---
draft: true
title: "Bullhorn, financial data and currencies"
snippet: "How do you get financial data from a system that is currency agnostic?"
image: {
    src: "https://images.unsplash.com/photo-1593720213428-28a5b9e94613?&fit=crop&w=430&h=240",
    alt: "full stack web development"
}
publishDate: "2025-07-10 21:30"
category: "bullhorn"
author: "John Kerr"
tags: [bullhorn, finance, tutorial]
---

So, you've been tasked with creating a Bullhorn integration and you need
to look at placements and pull in get all the fees to power up your new 
platform which is going to revolutionise the recruitment industry? 

No problem you say, I've been building integrations for years. How hard 
can it be...?

Well, one of the first things you will notice is that Bullhorn is 
completely currency agnostic. Despite the fact it is full of 
financial data, none of that financial data has a currency attached. 

In fact, it's a bit worse than that as, most of the time, it does have some 
currency data but it is nestling in one of the 70 (SEVENTY!) custom text 
fields you can find on any placement.

## That's concerning... so what do I do?

The first thing you need to do is scan Bullhorn to look for currency data. 
The Bullhorn API does support this, but you will **need to specify 
all of the custom fields** when querying.

```bash
INSERT REQUEST DETAILS
```

_IF_ you are lucky all the clients for your awesome new recruitment platform 
will use ISO currency codes. However, we have seen the following variations:

- "pound"
- "£"
- "£ pound"

We've even seen those variations within a single account! At the end of the 
day it is often down to individual recruitment consultants to populate this 
data so the general rule of thumb is **expect the unexpected**.