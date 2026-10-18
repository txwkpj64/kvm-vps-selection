# kvm vps: how to spot real KVM virtualization, compare plans, and pick the right size for your stack

Search for a kvm vps and you'll find two very different products sold under one name. A true KVM VPS is a full virtual machine with its own Linux kernel — real root access, loadable kernel modules, bootable custom ISOs, Docker that behaves exactly like its documentation promises. A container-based "VPS" (OpenVZ, LXC) shares the host's kernel and quietly takes those options off the table, usually at a slightly lower price. The price overlap between the two is real, which is why telling them apart matters more than hunting for the single cheapest listing.

The short version before the details: Vultr sells genuine KVM VPS under its Cloud Compute line, with plans starting at $2.50/month, hourly billing capped at the monthly rate, and more than 30 data center regions. If you'll keep a box running for a full year and don't mind prepaying, a budget annual special — RackNerd's 1 GB KVM plan at $21.99/year is a frequent pick in budget guides — works out cheaper than any monthly plan. Everything below covers how to tell real KVM from a container in disguise, what the plans cost once the small print is included, and which size fits which workload.

> **Quick picks**
> - Want a real KVM VPS with no commitment: 👉 [see Vultr's current KVM VPS pricing](https://www.vultr.com/pricing/?ref=7268283) — shared CPU plans from $2.50/mo, billed hourly, destroy anytime.
> - A 24/7 box you'll keep for a year or more: annual KVM specials from budget hosts (roughly $1.83/mo equivalent) beat monthly pricing.
> - Small Docker stack with a database: Vultr's $10–$20 shared CPU tiers.
> - Production database or CPU-bound app: dedicated-core plans, starting at $28/mo on Vultr.

## What a KVM VPS actually is (and why half the market isn't one)

A KVM VPS runs on KVM (Kernel-based Virtual Machine), the hypervisor built directly into the Linux kernel. Each customer gets a complete virtual machine: its own kernel, its own virtualized hardware, a hard memory allocation, and unrestricted root. The hypervisor enforces that boundary, so a noisy neighbor can neither steal your CPU time nor touch your kernel's state.

Container-based products do something cheaper. OpenVZ and LXC put every customer on the node inside the host's kernel. That's fine until you want to do anything kernel-adjacent: load the WireGuard module, tune sysctls, set custom nftables chains, or run Docker with working overlay2 and iptables. On a shared-kernel VPS those are the provider's decisions, not yours, and budget providers rarely say yes to all of them. Badly nested setups can even force Docker onto the slow `vfs` storage driver, where image pulls and builds crawl.

Three checks will separate a real KVM VPS from an impostor in about a minute:

1. The product page names its virtualization. If it says OpenVZ or LXC, it's a container. If the page dodges the question entirely, assume container.
2. Custom ISO support. A virtual machine doesn't care which OS you boot; a container physically can't boot anything but the host's setup.
3. Full root without caveats. If loading a kernel module requires a support ticket, the kernel was never yours.

To be fair, container VPS has its place. If you're hosting a static site or a small PHP app and never touch the kernel, the density savings do get passed on to you — OVHcloud, for instance, openly positions its budget virtualization option as the choice for smaller projects. The trouble starts when a container plan is marketed as if it were a full VM, which is common enough that every serious KVM hosting guide now leads with this warning.

## Vultr's KVM VPS plans and prices

Vultr sells its KVM VPS under the name Cloud Compute. Every plan in the table below is a full KVM virtual machine — own kernel, full root, your choice of OS image or a custom ISO upload — and every plan is billed by the hour with a monthly cap. The shared CPU line is where most "cheap KVM VPS" use cases land.

| Plan | vCPU | RAM | Storage | Monthly price | Deploy |
| --- | --- | --- | --- | --- | --- |
| 512 MB | 1 | 512 MB | 10 GB SSD | $2.50 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 1 GB | 1 | 1 GB | 25 GB SSD | $5.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 2 GB | 1 | 2 GB | 55 GB SSD | $10.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 2 GB (2 vCPU) | 2 | 2 GB | 60 GB SSD | $15.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 4 GB | 2 | 4 GB | 80 GB SSD | $20.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 8 GB | 4 | 8 GB | 160 GB SSD | $40.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 16 GB | 6 | 16 GB | 320 GB SSD | $80.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 24 GB | 8 | 24 GB | 640 GB SSD | $160.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 32 GB | 8 | 32 GB | 800 GB SSD | $320.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 48 GB | 12 | 48 GB | 1,280 GB SSD | $480.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |
| 64 GB | 16 | 64 GB | 1,700 GB SSD | $640.00 | [Get this plan](https://www.vultr.com/pricing/?ref=7268283) |

Details the table doesn't show:

- Bundled transfer scales from 0.5 TB on the 512 MB plan up to 9 TB on the 64 GB plan. Overage bills at $0.01/GB — no hard cutoff, just a metered surcharge.
- The 512 MB plan is also available IPv6-only for $2.00/month if you can live without inbound IPv4.
- Automatic backups add 20% of the plan price. Manual snapshots bill separately at $0.05/GB per month.
- No setup fees and no annual contracts; the listed price is what an always-on instance actually costs.

Above the shared line sit two more families, and the difference is technical rather than cosmetic:

| Family | Starts at | What changes | Fits |
| --- | --- | --- | --- |
| High Performance | $6.00/mo | Newer CPUs and NVMe storage, same hourly model | Busier web apps, small databases |
| Optimized – General Purpose | $28.00/mo | Dedicated vCPU, balanced RAM | Production APIs and app servers |
| Optimized – Dedicated | $42.00/mo | Dedicated vCPU with more RAM per core | Memory-hungrier app stacks |
| Optimized – CPU Optimized | $56.00/mo | More dedicated cores per gigabyte | Compute-heavy processing |
| Optimized – Memory Optimized | $70.00/mo | The most RAM per dedicated core | Database caches, in-memory workloads |

Each family scales up in several size steps — 👉 [the full grid lives on Vultr's pricing page](https://www.vultr.com/pricing/?ref=7268283). One bandwidth note for context: if raw throughput is your main criterion, OVHcloud's KVM VPS line advertises unlimited traffic at up to 3 Gbps (excluding its Asia-Pacific datacenters), which can beat an included-transfer model if you push constant heavy traffic. Most self-hosted setups never come close to a terabyte, let alone nine.

## What a Vultr KVM VPS actually costs per month

Hourly billing is not a discount — the cap equals the monthly price, so a 24/7 instance costs exactly what the table says. The hourly part pays off when instances don't live a full month. Run a load test on the 1 GB plan for ten days and destroy it: roughly 240 hours × $0.007 comes to about $1.70. Spin up a staging clone for an afternoon and it costs cents. Nothing is reserved, so there is nothing to cancel.

Against annual specials, the math works like this. RackNerd's 1 GB KVM Special costs $21.99/year — about $1.83/month for 1 vCPU, 20 GB SSD, and 3 TB of transfer on a 1 Gbps port, paid a year upfront. Vultr's 1 GB plan runs $5/month with no commitment. The annual plan pays for itself once the box survives about four and a half months; past that point it's pure savings. Under that point — or any time you're unsure the project survives the quarter — hourly wins, because the bill stops the moment you stop needing the machine.

| Provider | Cheapest KVM option | Key specs | Price | Billing |
| --- | --- | --- | --- | --- |
| Vultr | 1 GB shared CPU | 1 vCPU, 25 GB SSD, 1 TB transfer | $5.00/mo | Hourly, capped monthly |
| RackNerd | 1 GB KVM Special | 1 vCPU, 20 GB SSD, 3 TB transfer, 1 Gbps | $21.99/yr | Annual, prepaid |
| BuyVPS | S-2 Standard | 1 vCPU, 2 GB RAM, 30 GB NVMe, unlimited traffic | $7.99/mo (first month $1) | Monthly |
| X-Zone | Nano | 1 vCPU, 2 GB RAM, 20 GB NVMe, unmetered 1 Gbps | EUR 4/mo | Hourly, capped monthly |

Reading that table honestly: BuyVPS gives you double the RAM and unlimited transfer for $3 more per month, a better always-on deal if 2 GB is your target. X-Zone's Nano is aggressive on specs per euro. RackNerd's special is untouchable on annual price. Vultr's edge is flexibility — 30-plus regions, hourly billing, no prepay — plus one platform to grow into if you later need dedicated cores, managed databases, or object storage instead of a migration.

## Which plan size do you actually need?

RAM is the axis that matters for most self-hosted workloads; a single app rarely runs out of CPU before it runs out of memory. A rough mapping from what people actually run:

- **512 MB ($2.50)** — a VPN endpoint, a DNS relay, one small bot. Add swap. Don't plan a database here.
- **1 GB ($5.00)** — the practical floor for a blog, a personal Nextcloud, or one lightweight service. If you're torn between this and 512 MB, take this one.
- **2 GB ($10–$15)** — a small Docker Compose stack: app, database, reverse proxy. The $15 variant's second vCPU earns its keep once builds or backups run alongside traffic.
- **4 GB ($20.00)** — a production multi-service app or a small CI runner.
- **8 GB and up ($40+)** — busy databases, JVM applications, several services on one box.
- **Memory-bound workloads** — Postgres with a real cache, Java heaps, in-memory queues — belong on the Memory Optimized line from $70/mo rather than on stacked shared plans.

Two choices matter more than size. First, region: latency is set by distance, and having 30-plus locations means you can put the box near your users instead of near the provider's headquarters. Budget hosts often cluster in one or two cities, which is how a cheap deal turns into a 250 ms SSH session from Asia. Second, IPv4: the IPv6-only option saves $0.50 a month on the smallest plan, but only pick it if you genuinely don't need inbound IPv4, since adding it later means redeploying.

## Docker, WireGuard, and your own ISO

Because every Vultr instance is a KVM guest, the kernel-level work that budget hosts block simply works. You install Docker from Docker's own repository and get the fast overlay2 driver with working iptables and bridge networking. WireGuard runs as a kernel module. Sysctls are yours to tune, and you can boot a custom ISO for a distribution Vultr doesn't ship — or a Windows image, which full virtualization supports as a genuine guest OS.

The deploy flow is short: pick a region, pick an OS image (or upload your ISO), pick a size, and the instance is up in under a minute, already billing hourly. Destroy it when you're done and billing stops with it. If you'd rather poke at a real instance before settling on a size, 👉 [deploy a test instance from Vultr's Cloud Compute page](https://www.vultr.com/products/cloud-compute/?ref=7268283) — a few hours of testing costs less than the coffee you'll drink while it runs.

## KVM VPS FAQ

### Is hourly billing more expensive than monthly?

No. The hourly rate is the monthly price spread across the hours of the month, and billing stops at the monthly cap. An always-on instance costs exactly the listed monthly price; anything shorter-lived costs proportionally less.

### What happens if I go over my bandwidth?

On the shared CPU plans, overage bills at $0.01/GB with no hard cutoff. A traffic spike dents your invoice instead of taking the server offline. If you routinely push multiple terabytes, compare against providers with unmetered ports before assuming included transfer is cheaper.

### Can I upgrade a Vultr plan later?

Yes — moving to a larger plan is a stop-and-start from the control panel, and billing switches to the new rate. Downgrades aren't supported in place; you deploy a new instance and migrate. That asymmetry is an argument for starting slightly smaller than you think you need, since scaling up is painless.

### Do I specifically need KVM, or is any VPS fine?

Plain web hosting on a stock stack runs fine on a container VPS, often for less. You need KVM the moment any of these apply: Docker with full features, WireGuard or other kernel modules, custom firewall chains or sysctls, a non-Linux guest, or your own ISO.

## Before you check out

Three things worth thirty seconds:

- Decide on backups at deploy time. They add 20% to the plan price, and you can enable them anytime — but the most expensive backup is the one you didn't take.
- Match region to your users, not to a price list. A $2.50 plan in the right city beats a $5 plan an ocean away.
- Vultr's long-running new-account promo has been $250 in credits, valid for 30 days; the signup page shows the current amount, and it's enough to test several sizes before the first invoice lands.

So, the decision in one line: a keeper box with a fixed budget and no objection to prepaying → the annual KVM specials win on raw math. Anything with an unpredictable lifespan, anything you'll test or scale, anything that needs a nearby region today and dedicated cores next quarter → start on Vultr's hourly KVM plans and let the bill follow the workload. 👉 [Sign up on Vultr and deploy your first KVM VPS](https://bit.ly/VulTR)
