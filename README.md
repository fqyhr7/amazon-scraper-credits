# Amazon Search Results Scraper: How to Pull Product Data from Amazon at Scale — What Actually Works, What Blows Up Your Credit Budget, and Which Plan Is Worth It (Complete ScraperAPI Guide)

Amazon search results contain some of the most commercially valuable data on the internet. Rankings, pricing, review counts, Prime eligibility, sponsored vs. organic positions — whoever tracks this stuff consistently has a serious edge. The problem is Amazon really doesn't want you doing it.

If you've tried building a DIY scraper for Amazon search results, you already know the drill. Send a few hundred requests, hit a CAPTCHA wall. Rotate some proxies, get fingerprinted anyway. Use a headless browser, watch your IP get banned in 15 minutes. Amazon's anti-bot infrastructure is genuinely impressive — in an adversarial kind of way.

That's exactly why scraper APIs exist. You hand off the proxy rotation, CAPTCHA solving, and browser fingerprinting to someone else, and you just get back the data. The question is: which API actually works on Amazon search results, and what does it really cost once you stop looking at the headline numbers?

This guide is specifically focused on **scraping Amazon search results** — not just product pages, but the actual SERP-style results you get when someone searches for "wireless earbuds" or "standing desk converter" on Amazon. We'll dig into how ScraperAPI handles this, what the real-world costs look like, and when it makes sense versus just doing something else entirely.

---

## Why Amazon Search Results Are Harder to Scrape Than Product Pages

Most people assume scraping Amazon product pages (individual ASINs) is the hard part. Actually, search results pages tend to be trickier for a few reasons.

**Volume.** A product page scrape is often a one-and-done pull on a specific ASIN. Amazon search result monitoring means you're running queries continuously — every keyword, every day, possibly across multiple geographic markets. That's a fundamentally different volume profile.

**Volatility.** Search results change constantly. Sponsored placements shift. Organic rankings reshuffle. A new product enters with aggressive pricing. If your scraper is slow or caches aggressively, you're making decisions on stale data.

**Geo-sensitivity.** Amazon search results aren't uniform globally. What ranks #1 for "protein powder" in Germany looks completely different than the US results. Anyone doing international competitive research needs geo-targeted scraping, and that costs extra.

**Detection surface.** Search queries look very different from organic browsing patterns. Fire off 10,000 search queries from a handful of IPs and Amazon's systems notice. The pattern-matching is sophisticated.

All of which is a long way of saying: scraping Amazon search results properly requires infrastructure that most solo developers or small teams don't want to maintain themselves.

---

## What ScraperAPI's Amazon Search Scraper Actually Does

ScraperAPI is a web scraping API that handles the infrastructure layer — proxy rotation across 40M+ IPs in 50+ countries, automatic CAPTCHA solving, JavaScript rendering, and retry logic — behind a single HTTP endpoint. You send it a URL, it sends you back data.

For Amazon specifically, ScraperAPI offers what they call **Structured Data Endpoints (SDEs)** — purpose-built parsers for Amazon that return clean JSON instead of raw HTML. This is a meaningful distinction. With a raw HTML approach, you're writing and maintaining your own CSS selectors. When Amazon updates their page structure (and they do), your parser breaks. With an SDE, ScraperAPI maintains the parser and you just get structured data.

The **Amazon Search Scraper** endpoint returns the following fields per result:

- Product position in results
- ASIN
- Product name and URL
- Product image URL
- Price (with currency symbol)
- Star rating and total review count
- Prime eligibility badge
- Amazon's Choice / Best Seller badges
- Sponsored flag (distinguishes paid placements from organic)
- Recent purchase history message (e.g., "2K+ bought in past month")

Here's what a basic call looks like in Python:

python
import requests

payload = {
    'api_key': 'YOUR_API_KEY',
    'asin': 'search_query_here',  # For search: pass the query keyword
    'tld': 'com',                 # Amazon marketplace (com, co.uk, de, etc.)
    'country_code': 'us'          # Geotarget
}

r = requests.get('https://api.scraperapi.com/structured/amazon/search', params=payload)
print(r.json())


21 Amazon marketplaces are supported: `.com`, `.co.uk`, `.ca`, `.de`, `.es`, `.fr`, `.it`, `.co.jp`, `.in`, `.nl`, `.com.au`, and more. For geo-targeting within a marketplace (scraping amazon.com from a specific US ZIP code to get region-specific pricing and availability), ScraperAPI also supports ZIP code-level targeting.

In independent benchmarks run in early 2026 by Scrape.do, ScraperAPI achieved a **100% success rate on Amazon** with an average response time of 11,807ms. Not the fastest in the field (Scrape.do clocked in at 3,029ms, and ScrapingBee at 3,223ms), but the reliability number is what matters most for production pipelines where failed requests need to be retried.

> 👉 [Start your free trial — 5,000 credits, no credit card required](https://www.scraperapi.com/?fp_ref=coupons)

---

## The Credit System: The Thing Most Reviews Don't Explain Clearly

Here's where things get interesting — and where a lot of people end up surprised by their bills.

ScraperAPI bills in **API credits**, not raw requests. One credit does not equal one request. The actual credit cost per request depends on the domain you're hitting and the feature flags you enable. For Amazon search results specifically, the math goes like this:

**Amazon base cost: 5 credits per request.** This is a hard-wired domain multiplier. It applies automatically — you don't opt into it, and you can't opt out.

On top of that, if you need JavaScript rendering (some Amazon pages require it for dynamic content):

- `render=true` adds **+10 credits**
- `premium=true` (premium residential proxy) adds **+10 credits**
- Combined `premium=true + render=true` costs **+25 credits total** (not 20 — there's a non-linear stacking penalty)

So a worst-case Amazon search result with premium proxy and JavaScript rendering will cost you **30 credits per request**.

What does that mean in practice? On the Hobby plan (100,000 credits/month at $49), if you're running basic Amazon search scrapes at 5 credits each, you're getting about 20,000 requests. If those searches need rendering, you're down to roughly 3,300 requests per month. That's not a lot for anyone doing serious competitor tracking.

A quick reference table:

| Request Type | Credits per Request | Hobby Plan (100K) | Business Plan (3M) |
| --- | --- | --- | --- |
| Amazon search (basic) | 5 | 20,000 requests | 600,000 requests |
| Amazon search + JS render | 15 | ~6,667 requests | 200,000 requests |
| Amazon search + premium proxy | 15 | ~6,667 requests | 200,000 requests |
| Amazon search + premium + render | 30 | ~3,333 requests | 100,000 requests |

The Scaling plan ($475/month, 5M credits) is where the math starts to look more reasonable for moderate-scale Amazon search monitoring. At 5 credits per basic request, that's 1,000,000 Amazon search result pages per month — more than enough for most competitive intelligence workflows.

One more gotcha: **credits do not roll over**. Whatever you don't use by end of month is gone. And Pay-As-You-Go overage (continuing to use the API after hitting your limit) is only available on the Scaling plan and above. On Hobby, Startup, or Business, hitting 100% of your credits means you're cut off until renewal.

---

## All ScraperAPI Plans: Full Breakdown

Here's every plan currently available, including the annual pricing (which saves 10% across the board):

| Plan | Monthly Price | Annual (per mo) | API Credits | Concurrent Threads | Geo-Targeting | PAYG Overage |
| --- | --- | --- | --- | --- | --- | --- |
| **Free** | $0 | — | 1,000 | 5 | None | No |
| **Hobby** | $49 | $44.10 | 100,000 | 20 | US & EU only | No |
| **Startup** | $149 | $134.10 | 1,000,000 | 50 | US & EU only | No |
| **Business** | $299 | $269.10 | 3,000,000 | 100 | 50+ countries | No |
| **Scaling** | $475 | $427.50 | 5,000,000 | 200 | 50+ countries | Yes |
| **Professional** | $975 | $877.50 | 10,500,000 | 300 | 50+ countries | Yes |
| **Advanced** | $1,975 | $1,777.50 | 21,500,000 | 500 | 50+ countries | Yes |
| **Enterprise** | Custom | Custom | 22M+ | 500+ | Full | Yes |

A few things worth noting about this table:

**Country-level geo-targeting is a Business plan and above feature.** If you need to scrape Amazon search results from specific international markets (essential for global brand monitoring or international repricing), you'll need at least the $299/month Business plan. The Hobby and Startup plans only support US and EU geo-targeting.

**Concurrent threads matter for throughput.** The Hobby plan's 20 concurrent threads cap means even if you have credits left, you can't fire more than 20 requests simultaneously. For bulk scraping projects, this is the real bottleneck. The Business plan's 100 threads is a meaningful step up for parallel workflows.

**The Scaling plan is the first with Pay-As-You-Go.** This is a bigger deal than it sounds. If you're running an Amazon search monitoring workflow and you miscalculate your volume, running out of credits on a Hobby or Startup plan means hard downtime until billing renews. Scaling plan users just get a prompt to continue at a per-credit overage rate.

| Plan | Purchase Link |
| --- | --- |
| Free (1K credits) | [Get Started Free](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby – $49/mo | [Start Hobby Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup – $149/mo | [Start Startup Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business – $299/mo | [Start Business Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling – $475/mo | [Start Scaling Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional – $975/mo | [Start Professional Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced – $1,975/mo | [Start Advanced Plan](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | [Contact Sales](https://www.scraperapi.com/contact-sales/?fp_ref=coupons) |
