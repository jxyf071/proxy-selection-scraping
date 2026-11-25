# Best proxies for web scraping: residential or datacenter, what each type really costs per GB, and when a scraping API beats running your own pool

Pick the proxy type by how hard your target fights back, then buy the cheapest pool that survives it. Across 64 sellers whose per-GB plans were tracked between July 20 and October 14, 2026, the median smallest plan cost **$3.65/GB for residential and $0.60/GB for datacenter**, and at 10GB a month the cheapest and most expensive options sat almost 4x apart.

ScraperAPI bills on a different axis: per API credit, not per gigabyte. That makes it inexpensive on plain pages and expensive on LinkedIn, and it means part of the "best proxy" question is really "do I want to run a pool at all". Both halves are below: the proxy-type decision, the per-GB prices with their caveats, a 20-request test you can run before paying anyone, and the full ScraperAPI plan table with what a credit actually costs on the targets you scrape.

> **Quick picks**
> - **Plain HTML or lightly protected pages, 20k–200k a month, no appetite for maintaining rotation, retries and headless browsers:** ScraperAPI Business, $299/mo for 3,000,000 credits, which works out to about $0.10 per 1,000 standard pages. 👉 [See every ScraperAPI plan and its current price](https://www.scraperapi.com/pricing?fp_ref=coupons)
> - **Cloudflare, DataDome or PerimeterX in front of every request:** a per-GB residential plan usually wins on cost per page, because ScraperAPI adds 10 credits each time it has to bypass one of those.
> - **Testing the water:** the free plan carries 1,000 credits and 5 concurrent connections.

## ScraperAPI plans, all of them

| Plan | Credits / month | Concurrent threads | Monthly price | Annual (per month) | Start |
| --- | --- | --- | --- | --- | --- |
| Free | 1,000 | 5 | $0 | — | [ Free plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | 100,000 | 20 | $49 | $44.10 | [ Hobby plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | $149 | $134.10 | [ Startup plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 | 100 | $299 | $269.10 | [ Business plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | $475 | $427.50 | [ Scaling plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | $975 | $877.50 | [ Professional plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | $1,975 | $1,777.50 | [ Advanced plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Custom | Custom | [ Enterprise enquiry](https://www.scraperapi.com/pricing?fp_ref=coupons) |

Annual rates are billed upfront for the year; the monthly price is what you pay if you stay month to month. Two things differ by tier beyond the credit count. Geographic targeting is limited to the US and EU on Hobby and Startup and opens up to 50+ countries from Business upward. Analytics history is 30 days on the two entry tiers and unlimited above them.

Every paid plan lists the same feature set: JavaScript rendering, rotating proxy pools, CAPTCHA handling, sticky sessions, custom headers, automatic retries, JSON auto-parsing and a 99.9% uptime SLA. The plans don't differ in what the API can do, only in how much of it you get.

## What one credit actually costs on your target

A credit is one standard page request, but the pricing page spells out that harder domains consume more. The published multipliers:

| Target | Credits per request | Hobby ($49 / 100k) per 1,000 | Startup ($149 / 1M) per 1,000 | Business ($299 / 3M) per 1,000 |
| --- | --- | --- | --- | --- |
| Plain HTML page | 1 | $0.49 | $0.15 | $0.10 |
| Amazon | 5 | $2.45 | $0.75 | $0.50 |
| Cloudflare / DataDome / PerimeterX, bypassed | 11 | $5.39 | $1.64 | $1.10 |
| Google or Bing, including subdomains | 25 | $12.25 | $3.73 | $2.49 |
| LinkedIn | 30 | $14.70 | $4.47 | $2.99 |

That last row is where the headline number stops being useful. A team on Business scraping LinkedIn at volume pays $2.99 per 1,000 pages, which is roughly 30 times the cost of a plain page on the same plan. ScraperAPI does give you a Domain Cost Estimator in the dashboard that prices any URL before you commit, and a `max_cost` parameter to cap a single request, so you can find out what your specific target costs for free rather than discovering it on the invoice. 👉 [Price your target on the free plan before choosing a tier](https://www.scraperapi.com/pricing?fp_ref=coupons)

Two limit behaviours matter more than the credit count:

**Running out of credits.** By default scraping pauses until the allowance renews. If you want requests to keep flowing, the options depend on your tier. On Hobby, Startup, Business, Scaling and Professional you can switch on auto-upgrade, which moves you to the next plan when credits run dry. True pay-as-you-go, at a fixed per-credit rate up to a monthly cap you set, only exists on Scaling, Professional, Advanced and Enterprise. So on Hobby, Startup or Business, a traffic spike translates into a bigger subscription, not a metered bill.

**Running out of concurrency.** Exceeding your thread limit returns a 429 asking you to come back within the plan's limit. The request is rejected, not queued, and there is no surcharge for it. If your crawler fires 150 parallel requests on Business's 100 threads, one third of them simply don't happen.

## Which proxy type your target needs

Four buckets cover almost everything:

- **Heavy anti-bot protection (Cloudflare, DataDome, Akamai):** rotating residential. These sites inspect the network as well as the client, and a job sending all its traffic from one hosting ASN stands out as a pattern.
- **High volume against lenient sites:** datacenter. Cheap, fast, and adequate when nobody checks IP reputation.
- **Logged-in flows and long sessions:** ISP proxies, which hold a stable residential-adjacent address rather than rotating.
- **CAPTCHA-heavy targets:** residential plus an unblocker or a scraping API layered on top.

The cost of choosing badly is asymmetric. Residential runs $3.65/GB at the median entry plan, datacenter $0.60/GB, so a workload that doesn't need residential is paying roughly six times over. A small production job of 10GB a month typically lands between $27.50 and $100 depending on which supplier you pick.

The reverse also holds. Buying a $0.60/GB datacenter pool for a Cloudflare-protected retail site is money spent on requests that return 403s, and you pay for retries too. Zyte's own write-up on this makes the point that as failure rates climb, retries multiply, and since proxies are billed by bandwidth or request volume, cost grows faster than data output.

## Why a cheap pool fails even when the IPs are fine

On October 22, 2026, the same plain request was sent to 14 sites from a home connection and from a datacenter address. All 14 gave the same answer from both. Changing only the client's TLS stack, with the same IPs, changed the answer at two of them.

That result is the whole argument in miniature. Blocking decisions run on fingerprints as much as addresses: JA3, its successor JA4, HTTP/2 fingerprints, header ordering, behaviour. Cloudflare, DataDome and their peers profile the client before they ever look up the IP's reputation. A residential IP behind a Python `requests` session with an obviously scripted TLS handshake still gets challenged, and a datacenter IP behind a properly configured browser sometimes doesn't.

A separate retail test on August 24, 2026, took 52 US retail and marketplace storefronts and hit each from a home connection and a datacenter server. Nine responded differently. Seventeen ran more than one protection layer at once. Seventeen served a normal homepage to a bare client, but most of those still kept pricing behind sensor cookies or behavioural scoring, so a 200 response was not the same thing as usable data.

Where this lands for buying decisions: if your target runs JavaScript challenges, putting Playwright or another headless browser behind a residential proxy clears far more requests than bare HTTP ever will. That's a stack you have to build and maintain. It's also the exact work a scraping API sells you, which is why the API can be cheaper than a proxy plan at the same volume even when its per-unit price looks worse.

Residential has a latency tax as well. Real home connections add roughly 200–500ms per request over datacenter, which compounds fast across sequential page fetches. Check published median response times or benchmark before you commit to a large plan.

## The 20-request test that settles it before you pay

Every provider looks fine in a sales demo. Run this against your actual target, not a sample site, because a pool that works on Amazon can fail on LinkedIn and vice versa.

Send 20 requests through the provider's gateway to one URL on your target. Treat 403, 407, 429 and 503 as blocks. Then send 5 requests to an IP-echo endpoint such as `api.ipify.org` and count how many distinct exit IPs come back. Record median latency while you're at it.

How to read the three signals:

- **Success rate under 90%:** that pool is burned for that site. Change providers before you touch your code.
- **Lots of 429s:** the IPs are healthy and your request rate isn't. Slow down or spread concurrency across more IPs.
- **5 requests, 1 unique IP:** you're on a sticky session. Look at the session parameter in the proxy username before concluding rotation is broken.

Twenty requests is enough to separate a working pool from a dead one, and it costs a few cents of bandwidth. Doing it on a trial or free tier before buying a 100GB plan is the cheapest insurance in this whole category. Webshare hands out 10 free datacenter proxies, SOAX has a free Sandbox plan, Decodo runs a 3-day trial and Oxylabs gives $5 of credit, so you can test several without a subscription.

Public free proxy lists are a different thing entirely. They're slow, already blocked by anything worth scraping, and the operator can read your traffic. Use a vendor's free tier instead.

## Where per-GB proxies still beat an API

ScraperAPI is the wrong purchase in a few specific situations, and it's worth being blunt about them.

If you need an identity to persist for hours, for ad verification or a logged-in workflow, a residential plan with a configurable session lifetime is built for that. If your targets are LinkedIn, Instagram or heavily defended commerce sites at real volume, the credit multipliers above will cost more than a per-GB plan doing the same work. And if you want to pay only for the bandwidth you burn, with no monthly floor, per-GB billing is the native fit.

Verified numbers from the per-GB side, with the billing unit spelled out because they aren't comparable across rows:

| Provider | Billing unit | Verified price range | Notes |
| --- | --- | --- | --- |
| Bright Data | Per GB (residential), per IP (datacenter/ISP) | $2.50/GB "from"; $8/GB list, $4/GB under a 50% promotion (Oct 2026) | Datacenter from $0.90/IP, ISP from $1.30/IP. The gap between the "from" banner and the list price is the widest in this table |
| Oxylabs | Per GB | $2.50/GB at volume, $6/GB on the entry plan | Strict verification on signup |
| Decodo | Per GB | $3.75/GB at 3GB down to $2.75/GB at 100GB monthly; $3.50/GB at 5GB, $3.00/GB at 1TB | ZIP and ASN targeting |
| Webshare | Per IP (datacenter, ISP), per GB (residential) | Shared datacenter $0.018–$0.030/IP; dedicated ISP $0.225–$0.30/IP; residential from $1.40/GB at high volume | Self-serve signup, 10 free datacenter proxies |
| DataImpulse | Per GB | $0.80–$1.00/GB; 5GB for $5, 1TB for $800 | No ISP, SERP or dedicated datacenter products; traffic doesn't expire |
| IPRoyal | Per GB | $1.75/GB at volume, $7/GB pay-as-you-go | Traffic doesn't expire |

Bright Data is the clearest illustration of why "from $X/GB" deserves suspicion. The $2.50 figure is a volume starting point, the list price on the residential page runs to $8/GB, and the $4/GB a small buyer might see depends on a promotion running at the time. A small project can easily pay two to three times the advertised rate.

Evomi shows the same problem from a different angle. Its Core Residential tier is marketed around $0.49/GB, but geo-targeting, city-level targeting and ASN selection carry multipliers of up to 15x, so a $0.49/GB base rate can become $7.35/GB once you add the targeting most scrapers need. Its own site quotes $0.99/GB from 15GB. Both numbers are real; neither describes what a geo-targeted job pays.

## IP sourcing belongs in the reliability column

On July 2, 2026, the FBI, working with Google, Lumen and the Shadowserver Foundation, seized domains belonging to the NetNut residential proxy platform. Investigators tied it to Popa, a botnet assembled from roughly two million compromised consumer devices, mostly cheap smart TVs and streaming boxes.

Whatever you think of that case, it has a straightforward purchasing implication: a pool built on machines nobody consented to can disappear between one invoice and the next, taking your pipeline with it. Ask where the IPs come from before you route production traffic through them.

On the legal side, keep it simple. Proxies are legal, scraping public data is generally permitted, and the exposure comes from ignoring a site's terms, bypassing logins, or collecting personal data without a basis for it. RFC 9309 is the robots.txt standard worth respecting while you collect.

## What to pick, and two things to check before you pay

Run your own case against these:

- **Under 10GB a month, testing an idea:** the free ScraperAPI plan, then Hobby at $49 if the credit maths works for your target.
- **20k–200k plain or lightly defended pages a month, small team:** Business at $299/mo, where standard pages cost about $0.10 per 1,000. 👉 [Compare Business against the other ScraperAPI tiers](https://www.scraperapi.com/pricing?fp_ref=coupons)
- **Millions of pages behind Cloudflare or DataDome:** a per-GB residential plan, or ScraperAPI at Scaling and above where pay-as-you-go exists. Run the 20-request test on both before choosing.
- **One job, no subscription:** a pay-as-you-go residential plan. IPRoyal at $7/GB and Decodo's PAYG both fit, and IPRoyal's traffic doesn't expire.
- **A single identity that has to last hours:** ISP proxies or a residential plan with session control, not a rotating API pool.

Before you pay anyone, confirm two things. First, whether the entry price survives your actual target: on ScraperAPI that means pricing your URL with the Domain Cost Estimator, on a per-GB provider it means checking whether the rate you saw included geo-targeting or was a promotional tier. Second, what happens at the limit. On Hobby, Startup and Business, ScraperAPI's answer is an auto-upgrade to the next plan, since metered overage only starts at Scaling; on a per-GB plan it's usually a top-up at the same rate. Those two behaviours produce very different invoices for the same spike.

👉 [Start with the free 1,000 credits, price your target, then pick the smallest plan that covers it](https://www.scraperapi.com/pricing?fp_ref=coupons)
