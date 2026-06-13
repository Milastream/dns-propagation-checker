[Dns Propagation Checker](https://apify.com/nexgendata/dns-propagation-checker?fpr=data)

Checks how a domain resolves across **15 major global DNS resolvers** — Google, Cloudflare, Quad9, OpenDNS, Verisign, Yandex, AdGuard, CleanBrowsing, Neustar, and more. Detects mid-propagation inconsistency, regional cache poisoning, and misconfigured authoritative servers.

DNSChecker.org's free tier caps you at 1 domain per check. Their Pro plan is $29/mo for bulk. This actor runs 15 resolvers × N domains in parallel for **$0.002 per domain-check**.

## What it does

For every domain and record type you provide:

- Queries 15 geographically diverse resolvers in parallel
- Groups identical responses together ("consistency groups")
- Returns **propagation status**: `fully_propagated` / `partially_propagated` / `inconsistent` / `no_data`
- Reports per-resolver: records returned, TTL, NXDOMAIN/NoAnswer flags, errors
- Covers record types A, AAAA, MX, TXT, NS, CNAME, SOA, CAA

## Example

```
import requests

r = requests.post(
    "https://api.apify.com/v2/acts/nexgendata~dns-propagation-checker/run-sync-get-dataset-items?token=" + APIFY_TOKEN,
    json={
        "domains": ["newdomain.example.com"],
        "recordTypes": ["A", "MX"]
    }
)

for r_item in r.json():
    print(f"{r_item['domain']} {r_item['record_type']} — {r_item['propagation_status']}")
    print(f"  {r_item['unique_responses']} unique responses across {r_item['total_resolvers']} resolvers")
    for group in r_item["groups"]:
        print(f"  [{group['resolver_count']} resolvers] → {group['records']}")
```

Sample output:

```
newdomain.example.com A — partially_propagated
  2 unique responses across 15 resolvers
  [12 resolvers] → ['203.0.113.50']
  [3 resolvers] → ['203.0.113.99']   ← stale cache
newdomain.example.com MX — fully_propagated
  1 unique responses across 15 resolvers
```

## cURL

```
curl -X POST "https://api.apify.com/v2/acts/nexgendata~dns-propagation-checker/run-sync-get-dataset-items?token=$APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"domains":["example.com"],"recordTypes":["A","AAAA","MX","TXT"]}'
```

## Common use cases

- **Post-change verification** — after a DNS update, confirm it's live everywhere before cutting over traffic
- **CDN debugging** — CDN pointing to old IP in one region? Find which resolvers are stale
- **Email delivery investigation** — MX records inconsistent means deliverability problems
- **SPF/DKIM/DMARC rollout** — verify TXT records propagated globally before flipping enforcement policy
- **CAA enforcement verification** — before requesting new TLS certs, confirm CAA records are propagated

## Why 15 resolvers, not 50?

Most "global DNS" tools claim 40-60 resolvers but 90% of them share cache infrastructure — querying them is redundant. The 15 we query are **operator-distinct**: Google, Cloudflare, Quad9, OpenDNS (Cisco), Verisign, Yandex, AdGuard, NextDNS, Cisco Umbrella, Neustar, etc. Each has independent cache behavior. You see real diversity, not noise.

## Pricing

- **$0.005 per run** (startup)
- **$0.002 per domain-check** (one record type on one domain)

10 domains × 4 record types = 40 domain-checks = **$0.085**.

## FAQ

**Q: Does this query authoritative servers or just recursive resolvers?**
A: Recursive resolvers. That's the point — we're measuring what *users* see, not what the zone file says.

**Q: TTL countdown?**
A: Each resolver's current TTL is returned. Useful for predicting when a stale response will expire.

**Q: Can I check internal / private domains?**
A: No — external resolvers can't resolve your internal zones.

**Q: What's "inconsistent" mean vs. "partially_propagated"?**
A: `partially_propagated` = exactly 2 unique responses (classic mid-migration state). `inconsistent` = 3+ unique responses (either active attack, misconfigured split-horizon, or broken authoritative servers).

## Related tools

- [WHOIS Domain Lookup](https://apify.com/nexgendata/whois-domain-lookup?fpr=2ayu9b)
- [SSL Certificate Checker](https://apify.com/nexgendata/ssl-certificate-checker?fpr=2ayu9b)
- [Email RBL Bulk Checker](https://apify.com/nexgendata/email-rbl-checker?fpr=2ayu9b)
- [Email DMARC Auditor](https://apify.com/nexgendata/email-dmarc-auditor?fpr=2ayu9b)

## Try it

[🌐 DNS Propagation Checker on Apify](https://apify.com/nexgendata/dns-propagation-checker?fpr=2ayu9b)

New to Apify? [Get free platform credits](https://www.apify.com/?fpr=2ayu9b).

 

## 💻 Code Example — Python

```
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")
run = client.actor("nexgendata/dns-propagation-checker").call(run_input={
    # Fill in the input shape from the actor's input_schema
})

for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item)
```

## 🌐 Code Example — cURL

```
curl -X POST "https://api.apify.com/v2/acts/nexgendata~dns-propagation-checker/run-sync-get-dataset-items?token=YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{ /* input schema */ }'
```

## ❓ FAQ

**Q: How do I get started?**
Sign up at [apify.com](https://www.apify.com/?fpr=2ayu9b), grab your API token from Settings → Integrations, and run the actor via the Apify console, API, Python SDK, or any integration (Zapier, Make.com, n8n).

**Q: What's the typical cost per run?**
See the pricing section below. Most runs finish under $0.10 for typical batches.

**Q: Is this actor maintained?**
Yes. NexGenData maintains 165+ Apify actors and ships updates regularly. Bug reports via the Apify console issues tab get responses within 24 hours.

**Q: Can I use the output commercially?**
Yes — you own the output data. Check the target site's Terms of Service for any usage restrictions on the scraped content itself.

**Q: How do I handle rate limits?**
Apify manages concurrency and retries automatically. For very large batches (10K+ items), run multiple smaller jobs in parallel instead of one mega-job for better reliability.

## 💰 Pricing

Pay-per-event pricing — you only pay for what you actually extract.

- **Actor Start:** $0.0001
- **result:** $0.0050

## 🔗 Related NexGenData Actors

- [Hacker News Scraper](https://apify.com/nexgendata/hacker-news-scraper?fpr=2ayu9b)
- [Google Maps Lead Scraper](https://apify.com/nexgendata/google-maps-scraper?fpr=2ayu9b)
- [SEC EDGAR Scraper](https://apify.com/nexgendata/sec-edgar-scraper?fpr=2ayu9b)

## 🚀 Apify Affiliate Program

New to Apify? Sign up with our [referral link](https://www.apify.com/?fpr=2ayu9b) — you get free platform credits on signup, and you help fund the maintenance of this actor fleet.

## 📚 More From NexGenData

Explore the full catalog, tutorials, Gumroad data packs, and newsletter at **[thenextgennexus.com](https://thenextgennexus.com)** — the brand home for everything we ship.

- 📖 Tutorials & how-to guides
- 🗂️ Full actor catalog with usage examples
- 📦 Gumroad data packs (one-time purchases)
- 📬 Newsletter — monthly drops of new actors and revenue experiments

---

*Built and maintained by [NexGenData](https://apify.com/nexgendata?fpr=2ayu9b) — 165+ actors covering scraping, enrichment, MCP servers, and automation.*
🏠 Home: [thenextgennexus.com](https://thenextgennexus.com)