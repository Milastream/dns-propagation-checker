[Dns Propagation Checker](https://apify.com/automation-lab/dns-propagation-checker?fpr=data)

Check DNS record propagation across multiple public resolvers worldwide.

## What does DNS Propagation Checker do?

**DNS Propagation Checker** queries multiple public DNS resolvers (Google, Cloudflare, Quad9, OpenDNS, AdGuard, and more) to determine whether DNS changes have fully propagated. It compares results across 10 resolvers and reports the propagation status, agreement level, and individual resolver responses for each domain. Supports A, AAAA, CNAME, MX, NS, and TXT record types.

Use it after changing nameservers, updating A records, configuring MX records for a new email provider, or modifying any DNS configuration to confirm your changes are live across the internet. The clear `isPropagated` boolean in the output makes it easy to build automated workflows that wait for propagation before proceeding with the next step.

## Who is it for?

- 🔧 **DevOps engineers** — monitoring DNS changes as they propagate across global nameservers
- 🌐 **System administrators** — verifying that DNS updates have reached all regions
- 💻 **Web developers** — confirming domain migrations and SSL changes are live worldwide
- 🏢 **Managed service providers** — tracking DNS propagation for client domain changes
- 📋 **IT managers** — ensuring zero-downtime DNS transitions during infrastructure moves

## Use cases

- **System administrators** verifying that DNS records have propagated after migrating to a new hosting provider
- **DevOps engineers** confirming A or CNAME record updates are live before switching traffic in a blue-green deployment
- **Email teams** checking MX record propagation after setting up a new email provider
- **Domain managers** monitoring NS record changes after transferring a domain to a new registrar
- **Support engineers** diagnosing DNS resolution inconsistencies reported by users in different regions
- **Website owners** confirming that CDN or hosting provider CNAME records are visible to all major DNS resolvers

## Why use DNS Propagation Checker?

- **10 global resolvers** — checks Google, Cloudflare, Quad9, OpenDNS, AdGuard, and more in a single run
- **Multiple record types** — supports A, AAAA, CNAME, MX, NS, and TXT lookups
- **Propagation verdict** — returns a clear `isPropagated` boolean based on resolver agreement
- **Batch processing** — check propagation for many domains at once
- **Per-resolver detail** — see response time and records from each individual resolver
- **Pay-per-event pricing** — costs scale with actual usage, just $0.002 per domain checked
- **Structured output** — JSON results ready for filtering, dashboards, or downstream automation via the API

## Input parameters

| Parameter | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `domains` | string[] | Yes | — | List of domains to check DNS propagation for. |
| `recordType` | string | No | `"A"` | DNS record type to check. One of: `A`, `AAAA`, `CNAME`, `MX`, `NS`, `TXT`. |

### Example input

```
{
    "domains": ["google.com", "github.com"],
    "recordType": "A"
}
```

## Output example

Each domain produces one result object with propagation status, resolver agreement counts, the most common records, and per-resolver details including response times. The `results` array contains one entry per resolver.

```
{
    "domain": "google.com",
    "recordType": "A",
    "isPropagated": true,
    "resolversChecked": 10,
    "resolversAgreed": 10,
    "resolversFailed": 0,
    "mostCommonRecords": ["142.250.80.46"],
    "results": [
        {
            "resolver": "8.8.8.8",
            "provider": "Google",
            "records": ["142.250.80.46"],
            "responseTimeMs": 12,
            "error": null
        }
    ],
    "error": null,
    "checkedAt": "2026-03-01T12:00:00.000Z"
}
```

## How to check DNS propagation

1. Go to [DNS Propagation Checker](https://apify.com/automation-lab/dns-propagation-checker) on Apify Store
2. Enter one or more domain names (e.g., `google.com`, `github.com`)
3. Select the DNS record type to check (A, AAAA, CNAME, MX, NS, or TXT)
4. Click **Start** and wait for the run to finish
5. Review the propagation status and per-resolver results for each domain
6. Download your results as JSON, CSV, or Excel from the **Dataset** tab

## How much does it cost to check DNS propagation?

DNS Propagation Checker uses Apify's pay-per-event pricing:

| Event | Price | Description |
| --- | --- | --- |
| Start | $0.035 | One-time per run |
| Domain checked | $0.002 | Per domain checked |

**Examples:**

- 1 domain: $0.035 + 1 x $0.002 = **$0.037**
- 50 domains: $0.035 + 50 x $0.002 = **$0.135**
- 500 domains: $0.035 + 500 x $0.002 = **$1.035**

## Using the Apify API

The Apify API lets you control DNS Propagation Checker programmatically. Schedule runs, read datasets, manage webhooks, and more. See the [Apify API reference](https://docs.apify.com/api/v2) for details.

### Node.js

```
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: '<YOUR_API_TOKEN>' });
const run = await client.actor('automation-lab/dns-propagation-checker').call({
    domains: ['google.com'],
    recordType: 'A',
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items);
```

### Python

```
from apify_client import ApifyClient

client = ApifyClient('<YOUR_API_TOKEN>')
run = client.actor('automation-lab/dns-propagation-checker').call(run_input={
    'domains': ['google.com'],
    'recordType': 'A',
})
items = client.dataset(run['defaultDatasetId']).list_items().items
print(items)
```

### cURL

```
curl "https://api.apify.com/v2/acts/automation-lab~dns-propagation-checker/run-sync-get-dataset-items?token=YOUR_TOKEN" \
  -X POST \
  -H "Content-Type: application/json" \
  -d '{"domains": ["google.com"], "recordType": "A"}'
```

## Use with AI agents via MCP

DNS Propagation Checker is available as a tool for AI assistants via the [Model Context Protocol (MCP)](https://docs.apify.com/platform/integrations/mcp).

### Setup for Claude Code

```
$claude mcp add --transport http apify "https://mcp.apify.com?tools=automation-lab/dns-propagation-checker"
```

### Setup for Claude Desktop, Cursor, or VS Code

```
{
    "mcpServers": {
        "apify": {
            "url": "https://mcp.apify.com?tools=automation-lab/dns-propagation-checker"
        }
    }
}
```

### Example prompts

- "Check if DNS changes for example.com have propagated"
- "Has the A record update for our domain reached all resolvers?"
- "Verify MX record propagation for our new mail server"

Learn more in the [Apify MCP documentation](https://docs.apify.com/platform/integrations/mcp).

## Integrations

Connect DNS Propagation Checker with other apps and services using [Apify integrations](https://apify.com/integrations). You can integrate with Make, Zapier, Slack, Google Sheets, Google Drive, n8n, and many more. Set up a webhook to receive a Slack notification the moment propagation completes, or push results to a Google Sheet to track rollout progress across your domain portfolio.

## Tips and best practices

- **Check immediately and again later** — DNS propagation can take minutes to hours; run the actor right after a change and schedule a follow-up check 30-60 minutes later
- **Use the right record type** — make sure you select the record type that matches your change (e.g., MX for mail server changes, CNAME for alias updates)
- **Look at individual resolver results** — if `isPropagated` is `false`, check the `results` array to see which resolvers still return stale data
- **Combine with scheduling** — set up periodic runs after a DNS change to get a timeline of when propagation completes
- **Monitor after TTL expiry** — if your old records had a high TTL, propagation may take longer; schedule checks after the old TTL period has elapsed
- **Export for reporting** — download results as JSON, CSV, or Excel from the dataset tab to document propagation status for change management records

## Legality

This tool analyzes publicly accessible web content. Automated analysis of public web resources is standard practice in SEO and web development. Always respect robots.txt directives and rate limits when analyzing third-party websites. For personal data processing, ensure compliance with applicable privacy regulations.

## FAQ

**How many resolvers are checked?**
The actor queries 10 well-known public DNS resolvers including Google (8.8.8.8), Cloudflare (1.1.1.1), Quad9 (9.9.9.9), OpenDNS, and AdGuard. This provides a reliable cross-section of global DNS resolution.

**What does "isPropagated" mean exactly?**
The `isPropagated` field is `true` when all resolvers that successfully responded return the same set of records. If any resolver returns different records, `isPropagated` is `false`.

**Can I check propagation for multiple record types at once?**
Each run checks one record type at a time. To check both A and MX records, run the actor twice with different `recordType` values.

**How long does DNS propagation usually take?**
Most DNS changes propagate within minutes to a few hours, depending on the TTL (Time to Live) of the previous records. Changes to name servers or records with long TTLs (e.g., 86400 seconds / 24 hours) may take up to 48 hours in rare cases. Running this actor periodically after a change gives you a clear picture of progress.

**What does it mean when some resolvers fail?**
A failed resolver means that particular DNS server did not return a response within the timeout period. The `resolversFailed` count and the per-resolver `error` field identify which resolvers had issues. Occasional failures are normal and do not necessarily indicate a propagation problem.

**Which DNS resolvers are queried?**
The actor queries 10 well-known public resolvers: Google Public DNS (8.8.8.8, 8.8.4.4), Cloudflare (1.1.1.1), Quad9 (9.9.9.9), OpenDNS (208.67.222.222), AdGuard (94.140.14.14), and others. This selection provides broad coverage of the most commonly used public DNS infrastructure worldwide.

**isPropagated is false but my website works fine. Is something wrong?**
Not necessarily. The `isPropagated` field is `false` when not all resolvers return identical records. This can happen when your DNS provider uses anycast or GeoDNS, which intentionally returns different IPs to different resolvers. If your site is reachable and working, the "disagreement" may be by design rather than a propagation delay.

**Some resolvers show errors. Should I be concerned?**
Occasional resolver failures are normal and usually caused by temporary network issues between Apify's infrastructure and that specific resolver. If only 1-2 of 10 resolvers fail, this is not a propagation problem. Re-run the actor to confirm -- transient failures typically resolve on the next attempt.

## Other DNS and domain tools

- [DNS Lookup](https://apify.com/automation-lab/dns-lookup) — Look up all DNS record types for any domain
- [DMARC Record Checker](https://apify.com/automation-lab/dmarc-record-checker) — Check DMARC email authentication policies
- [SPF Record Checker](https://apify.com/automation-lab/spf-record-checker) — Check SPF records for email sender authorization
- [MX Record Checker](https://apify.com/automation-lab/mx-record-checker) — Check MX records for mail server configuration
- [TXT Record Checker](https://apify.com/automation-lab/txt-record-checker) — Look up TXT records for any domain
- [AAAA Record Checker](https://apify.com/automation-lab/aaaa-record-checker) — Check IPv6 AAAA records
- [CNAME Record Checker](https://apify.com/automation-lab/cname-record-checker) — Check CNAME alias records
- [NS Record Checker](https://apify.com/automation-lab/ns-record-checker) — Check authoritative name servers
- [SSL Certificate Checker](https://apify.com/automation-lab/ssl-certificate-checker) — Check SSL/TLS certificate details
- [WHOIS Lookup](https://apify.com/automation-lab/whois-lookup) — Look up domain registration details
- [Domain Age Checker](https://apify.com/automation-lab/domain-age-checker) — Check domain registration age
- [Domain Availability Checker](https://apify.com/automation-lab/domain-availability-checker) — Check if domains are available for registration