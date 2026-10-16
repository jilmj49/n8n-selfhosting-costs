# n8n self hosting cost: the real monthly bill, hidden extras, and how a RackNerd VPS runs unlimited executions for about $4 a month

The n8n software costs nothing to self-host. The Community Edition gives you unlimited workflow executions, unlimited users, and all 400+ integrations with no license fee — ever. What you actually pay for is the server it runs on, plus a couple of dollars for a domain and backups, plus a few hours of your time.

That server bill is smaller than most people expect. On a budget host like RackNerd, an annual VPS deal works out to roughly **$2–7 a month**, and the whole stack — domain, TLS, offsite backups included — lands around **$4 a month all-in**. Compare that with n8n Cloud, which starts at €20/month for just 2,500 executions, and the reason "n8n self hosting cost" is such a popular search becomes obvious.

This guide walks through the numbers line by line: what the software genuinely needs from a server, RackNerd's current annual pricing, where the break-even with n8n Cloud sits, and the cost lines most guides quietly skip.

> **In a hurry?**
> - Most solo setups: RackNerd's **2.5GB annual special** — 2 vCPU, 2.5GB RAM, 50GB SSD, 4TB traffic for **$28.88/year** (about $2.41/month). It runs n8n plus PostgreSQL comfortably, with no execution cap, and the price you sign up at is the price you renew at. 👉 [Check RackNerd's current VPS specials](https://bit.ly/my-rn)
> - Running AI agent nodes or a busier team? Grow into the **4GB plan at $48.99/year**.
> - Under 2,500 executions a month and allergic to servers? n8n Cloud's Starter plan (€20/month) is the less fussy option. The math flips hard in favor of self-hosting above roughly 10,000 runs.

## What n8n self-hosting actually costs per month

Three lines decide it: **$0 for the software**, **$2–7 a month for the server** on a budget annual plan, and **$2–3 a month for the extras** you'd want in production (domain, backups, monitoring). That's the cash bill for a solo or small-team setup.

The range depends almost entirely on where you rent the server. Recent cost breakdowns put a modest single-server setup at **$16.60/month in cash** on a mainstream cloud like DigitalOcean ($12 VPS + $3.60 daily backups + $1 domain allowance), while a Hetzner CX22 build lands near **$5.90/month** once IPv4 and backups are counted. RackNerd's annual specials sit below both — more on that in a moment.

Then there's the line item nobody can discount: your time. The realistic figure is **4–6 hours to set things up properly** and **1–2 hours a month** after that. Price that at $60/hour and you're looking at $90–120/month of your own time — several times the server bill. One stricter cost model assumes 4.5 maintenance hours a month and lands at $137/month at a $25 hourly rate, or $362/month at $75.

The server is the only line you can shrink by shopping around. That's what the rest of this piece is about.

## How much server n8n actually needs

For a solo setup, **2GB of RAM is the comfortable floor and 4GB is the recommended spot**. n8n is a Node.js app with an optional PostgreSQL database and an optional Redis for queue mode; the official Docker image defaults to SQLite, which is fine when you're the only user.

| Workload | CPU | RAM | Disk | Verdict |
| --- | --- | --- | --- | --- |
| Solo, a few workflows | 1 vCPU | 1GB | 10GB | Works, tight |
| Active solo (10+ workflows) | 1 vCPU | 2GB | 20GB | Comfortable |
| Team / heavier automation | 2 vCPU | 4GB | 40GB | Recommended |
| High-frequency (100k+ runs/mo) | 2 vCPU | 8GB | 40GB+ | Postgres + Redis, watch RAM |

Three things eat memory faster than workflow count does: **error retries, queue mode, and AI agent nodes** — the last one holds the LLM context in RAM while a run executes. Queue mode also isn't a free toggle; it needs Redis, a shared database, and worker processes, and a 2GB box can run out of memory with many parallel branches.

Database choice matters too. SQLite locks up under concurrent writes, so once you pass roughly **1,000 executions a day**, move to PostgreSQL — the migration is about an hour of swapping the Docker Compose file, and PostgreSQL runs happily on the same box.

## RackNerd VPS plans and prices for n8n

RackNerd sells annual KVM VPS specials that undercut even Hetzner's famously cheap monthly rates. Their 4GB tier — 2.5 vCPU, 65GB SSD, 5TB traffic — runs **$48.99/year (about $4.08/month)** with a dedicated IPv4 included. The comparable Hetzner CX22 (2 vCPU, 4GB, 40GB) costs $4.49/month plus $0.48 for IPv4, or roughly $59.64 a year. RackNerd gives you more storage and more traffic for about $10 less per year, and — this is the part deal-hunters care about — **the renewal price stays at the signup price for as long as you keep the service**.

Here's the current annual lineup:

| Plan | Configuration | Annual price | ≈ Per month | Buy |
| --- | --- | --- | --- | --- |
| 768MB Special | 1 vCPU · 768MB RAM · 10GB SSD · 1TB traffic | $9.89/yr | $0.82 | [ Get the 768MB plan](https://bit.ly/my-rn) |
| 1GB Special | 1 vCPU · 1GB RAM · 17GB SSD · 1.5TB traffic | $11.29/yr | $0.94 | [ Get the 1GB plan](https://bit.ly/my-rn) |
| 2.5GB Special | 2 vCPU · 2.5GB RAM · 50GB SSD · 4TB traffic | $28.88/yr | $2.41 | [ Get the 2.5GB plan](https://bit.ly/my-rn) |
| 4GB Special | 2.5 vCPU · 4GB RAM · 65GB SSD · 5TB traffic | $48.99/yr | $4.08 | [ Get the 4GB plan](https://bit.ly/my-rn) |
| 5GB Special | 3 vCPU · 5GB RAM · 80GB SSD · 6TB traffic | $63.99/yr | $5.33 | [ Get the 5GB plan](https://bit.ly/my-rn) |
| 6.5GB Special | 4 vCPU · 6.5GB RAM · 100GB SSD · 7TB traffic | $85.99/yr | $7.17 | [ Get the 6.5GB plan](https://bit.ly/my-rn) |

A few things worth knowing before you pick a row:

- **How the lineup maps to n8n:** the 768MB and 1GB tiers are for testing and very light use (they match the "works, tight" profile above). The 2.5GB tier is the solo sweet spot with room for PostgreSQL. The 4GB tier matches the recommended team profile and handles queue mode without swapping. At 100,000+ runs a month, the 5GB and 6.5GB tiers cover the heavy profile — or add a second cheap box as a queue worker.
- **Specials rotate.** RackNerd runs different deals around New Year, Black Friday, and other promo windows, and the cheapest pricing is usually tied to specific data center locations. Treat the table as the standard band; the exact configuration and price show at signup.
- **Locations:** several US data centers, including San Jose, Dallas, Chicago, and New York. Pick whichever is closest to where your webhooks come from.
- **What you get:** KVM virtualization, full root access, a dedicated IPv4, and ticket-based support. What you don't get: managed anything. Backups are on you (more on that below), and you're paying a year up front.

If you want someone else to handle updates, monitoring, and restores out of the box, a mainstream managed cloud or n8n Cloud fits better — that convenience is exactly what the price difference buys.

## The full monthly bill, line by line

With the 2.5GB plan, everything lands at about **$4 a month all-in**:

| Item | Monthly cost |
| --- | --- |
| RackNerd 2.5GB VPS ($28.88 ÷ 12) | $2.41 |
| Domain (≈$12/year, amortized) | ~$1.00 |
| TLS certificate (Let's Encrypt) | $0 |
| Offsite backups (restic → Backblaze B2, ~10GB) | ~$0.60 |
| Uptime monitoring (Uptime Kuma, same box) | $0 |
| **Total** | **≈ $4.01** |

The Hetzner-based builds in recent comparisons come out at $5.50–5.90/month, and the DigitalOcean-based model at $16.60. RackNerd's number is lower for two reasons: annual prepay (the biggest one) and a budget-provider price structure. The trade-offs are the ones listed above — money up front, and every maintenance task is yours. 👉 [Grab the 2.5GB annual plan](https://bit.ly/my-rn)

## Self-hosted vs n8n Cloud: where the break-even sits

Cloud wins below roughly 10,000 workflow runs a month; self-hosting wins decisively above it. n8n Cloud's pricing (verified against their pricing page in August 2026): a free tier with 1,000 executions/month and 5 active workflows, **Starter at €20/month for 2,500 executions**, **Pro at €50/month for 10,000**, and a Business tier at €667/month for 40,000. One "execution" is one complete workflow run, no matter how many steps it contains, and overage charges kick in once you pass your quota.

| Monthly runs | n8n Cloud | Self-hosted on RackNerd (cash) | Smarter pick |
| --- | --- | --- | --- |
| 2,500 | Starter, €20/mo (~$22) | ~$4 all-in | Cloud — if 1–2 hrs/mo of upkeep annoys you more than €20 |
| 10,000 | Pro, €50/mo (~$55) | ~$4 all-in | Self-hosted, clearly |
| 25,000 | Above Pro; next tier is Business at €667/mo | ~$4 all-in | Self-hosted, no contest |
| 100,000 | Enterprise, custom quote | ~$5–7 (bigger box) | Self-hosted, decisively |

Two honest caveats before you declare victory over the cloud. First, the time math from earlier still applies: at $60/hour, 1–2 hours of monthly maintenance costs more than either option's server bill, which is why small volumes are genuinely fine on Cloud. Second, execution price isn't the only cloud number worth knowing — the same automation volume that runs on a $4 VPS would cost upwards of $1,500/month on Zapier. The gap you're closing by self-hosting is bigger than it first looks. 👉 [Compare all RackNerd plans and prices](https://bit.ly/my-rn)

## Costs people forget to count

Most guides stop at the server price. These five lines decide whether the headline number holds up:

- **Renewal pricing.** The single most important number to check with any host before comparing headline deals. RackNerd's specials renew at the same rate you signed up at, for the life of the service — the $28.88 plan is still $28.88 on your third renewal. Not every provider works this way.
- **The license.** n8n is fair-code (the Sustainable Use License), which the company itself says is not OSI-certified open source — it launched under a fully permissive license in 2019 and has tightened the terms twice since. You can run it internally at any scale for free, and you can build workflows for clients as a paid service; that restriction was lifted. You can't resell hosted n8n access to others, and files marked `.ee` (SSO, Git version control, multiple environments, log streaming, and similar governance features) require a paid Business or Enterprise license. The Business Plan runs €667/month for 40,000 executions — fewer than the free Community Edition's unlimited — and you still manage the server yourself.
- **Paid APIs.** The n8n box is cheap, but executions that call OpenAI or other paid services bill at whatever those providers charge. Two workflows on the same $4 server can produce wildly different API bills, so budget them separately.
- **Backups.** Budget VPS plans don't include them. A nightly restic job pushing to Backblaze B2 costs about $0.60/month for a typical 10GB footprint — cheap insurance for a server that stores all your workflow logic and run history.
- **The database switch.** SQLite is fine until it isn't. Past roughly 1,000 executions a day, concurrent writes start blocking each other; plan the PostgreSQL migration around then, not after an outage.

## FAQ

**Is n8n free to self-host?**
Yes — the Community Edition has no license fee, no execution limit, no user limit, and all 400+ integrations. What you pay for is the server ($2–7/month on a budget annual plan), optional extras, and your time.

**How much RAM does n8n need?**
2GB is comfortable for an active solo setup, 4GB is the recommended level for teams and queue mode, and 8GB suits high-frequency use above 100,000 runs a month or heavy AI agent workflows.

**Is a $9.89/year VPS enough for n8n?**
For testing and a handful of light workflows, yes — it matches the "works, tight" profile. Don't plan on running PostgreSQL alongside it, and expect to move up to the 2.5GB tier once you pass ten workflows or add AI nodes.

**Does RackNerd raise the price at renewal?**
No. Their special pricing renews at the same rate for as long as you keep the service active. It's one of the main reasons their annual deals have a following among people who've been burned by intro-rate pricing elsewhere.

**When does n8n Cloud make more sense?**
Under about 2,500 runs a month, when you'd rather not touch a terminal, or when automatic updates and official support are worth more to you than the price difference. The free tier (1,000 executions/month) is also the fastest way to prototype before committing either way.

## Which setup should you pick?

Match the plan to your volume and your tolerance for servers:

- **Under ~2,500 executions a month, no interest in managing a server:** n8n Cloud's free tier or Starter plan. Self-hosting's savings don't cover your time at this volume.
- **A few thousand to tens of thousands of runs a month, comfortable with Docker, care about data privacy or custom code:** the RackNerd 2.5GB annual special at $28.88/year. Unlimited executions, locked renewal price, and the whole stack costs about $4 a month.
- **AI agent nodes, queue mode, or a small team:** the 4GB plan at $48.99/year. If you outgrow it, a second cheap box as a queue worker is a straightforward upgrade.
- **Six figures of monthly executions:** the 6.5GB tier, with monitoring on the box and a real backup schedule from day one.

Before you check out, confirm two things: pick the data center closest to wherever your webhooks originate, and set up that offsite backup job the same day you deploy — it's the one mistake that costs real money later. 👉 [See RackNerd's current annual specials](https://bit.ly/my-rn)
