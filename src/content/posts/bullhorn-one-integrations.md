---
draft: true
title: "Getting started with Bullhorn One"
snippet: "Understanding the Bullhorn One endpoints and getting started with an integration"
image:
  {
    src: "https://images.unsplash.com/photo-1593720213428-28a5b9e94613?&fit=crop&w=430&h=240",
    alt: "full stack web development",
  }
publishDate: "2025-07-17 21:30"
category: "bullhorn"
author: "John Kerr"
tags: [bullhorn, bullhorn-one, api, integration, tutorial]
---

So you've been tasked with creating a Bullhorn One integration, and you're staring at the long list of endpoints and 
wondering where to get started? Well, fear not, as we've been exactly where you are and we're here to shed some light 
on how Bullhorn One hangs together.

If you're not already aware, Bullhorn One is Bullhorn's middle office system for North America. It's still relatively 
early days, but the last published adoption statistics (at the time of writing) show that many of Bullhorn's biggest 
customers are on there.

![Bullhorn One adoption](bullhorn-one-adoption.png)

With an average of 200+ users per account, it is well worth your time considering a Bullhorn One integration!

If you already have a Bullhorn integration, then I have good news for you as you are accessing the same API with the 
same authentication mechanisms, so you won't need to reinvent the wheel there. If not, then stay tuned for our future 
post on authenticating with Bullhorn.

Onto the Bullhorn One specifics. There are a LOT of endpoints! 😅 Fortunately, you can identify most of them by 
the "Pay and Bill" prefix that Bullhorn applies. For the purposes of this blog, we'll assume you're looking to get 
billing data out of Bullhorn One.

## Finding the margin

The first place you are likely to look is the 
[timesheets](https://bullhorn.github.io/rest-api-docs/entityref.html#timesheet) endpoint - as it happens this is an example 
of an endpoint without the "Pay and Bill" prefix. While you can potentially get _some_ useful data from there, such 
as hours worked, from a revenue perspective that endpoint is largely useless.

Instead, you want to be looking 
at [payable](https://bullhorn.github.io/rest-api-docs/entityref.html#pay-and-bill-payablecharge) 
and [billable](https://bullhorn.github.io/rest-api-docs/entityref.html#pay-and-bill-billablecharge) charges. All of 
the financial transactions running through Bullhorn One will end up in one of these two places. The subtotal property 
is the one to look at for each type of charge. Remember 
though - [you can't rely on the currencyUnit property](/posts/bullhorn-currencies).

So, to recap what we have so far. Placements are the same as standard Bullhorn, we can go to timesheets to capture data 
such as hours worked and we're going to payable and billable charges for financial data. However, you may also need to 
get the true margin from Bullhorn One (targeting only specific earn codes and deducting any burdens). This is where the 
plot thickens.

## Finding the true margin

Firstly, let's look at earn codes and filtering the pay and charge amounts down to only include valid codes.
