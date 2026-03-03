# Darvag

**Darvag** (دَرواگ, "gateway" in Persian) is a social activist agent that deploys and coordinates volunteer-run [Conduit](https://conduit.psiphon.ca/en/) proxy relays, providing free and uncensored internet access to people in Iran and other censored regions.

![Darvag](final.png)

---

## What Is Conduit?

[Conduit](https://conduit.psiphon.ca/en/) is a volunteer-run proxy node built by [Psiphon](https://psiphon.ca/). Each Conduit relay helps route encrypted traffic for users in censored regions. It is designed with privacy as a first principle -- relays **do not log, inspect, or store any traffic**.

When you run a Conduit relay, your server donates bandwidth to people who need it. Nothing more.

## Quick Start (5 minutes)

### Prerequisites

- A Linux server (VPS, cloud instance, or bare metal) with Docker installed
- At least 512 MB RAM and 5 Mbps bandwidth

### Deploy

```bash
git clone https://github.com/shirin-shahabi/Darvag.git
cd Darvag/conduit
cp .env.example .env    # edit if you want to change defaults
docker compose up -d
```

Your relay is now live. It will automatically register with Psiphon's broker and begin accepting connections from users in censored regions.

### Verify

```bash
docker logs darvag-conduit 2>&1 | tail -5
```

You should see output like:

```
[OK] Starting Psiphon Conduit (Max Common Clients: 50, Max Personal Clients: 0, Bandwidth: 100 Mbps)
[STATS] Announcing: 50 | Connecting: 0 | Connected: 0 | Up: 0 B | Down: 0 B
```

`Announcing: 50` means your relay is offering 50 connection slots to the Psiphon network.

### Monitor (Optional)

The stack includes Prometheus + Grafana for monitoring. Access the dashboard at `http://<your-server-ip>:3000` (default credentials: `admin` / value from `.env`).

## Configuration

Edit `conduit/.env` to tune your relay:

| Variable | Default | Description |
|---|---|---|
| `CONDUIT_MAX_CLIENTS` | 50 | Concurrent user slots (10-1000) |
| `CONDUIT_BANDWIDTH` | 100 | Bandwidth cap in Mbps |
| `GRAFANA_USER` | admin | Grafana dashboard username |
| `GRAFANA_PASSWORD` | changeme | Grafana dashboard password |
| `GRAFANA_PORT` | 3000 | Grafana web UI port |

## Architecture

```
┌─────────────────────────────────────────────┐
│                  Your Server                │
│                                             │
│  ┌───────────┐  ┌────────────┐  ┌────────┐ │
│  │  Conduit   │→│ Prometheus │→│ Grafana │ │
│  │  (relay)   │  │ (metrics)  │  │  (UI)  │ │
│  └─────┬─────┘  └────────────┘  └────────┘ │
│        │                                    │
└────────│────────────────────────────────────┘
         │
         ↓
┌─────────────────┐     ┌──────────────────┐
│  Psiphon Broker  │ ←→ │  Users in Iran    │
│  (coordination)  │     │  (encrypted conn) │
└─────────────────┘     └──────────────────┘
```

The Conduit relay connects outbound to Psiphon's broker. No inbound ports need to be opened (beyond Grafana if you want monitoring). Users in censored regions connect through the Psiphon network, which routes traffic through your relay.

## Resource Requirements

| Resource | Minimum | Recommended |
|---|---|---|
| CPU | 1 vCPU | 2 vCPU |
| RAM | 512 MB | 1 GB |
| Bandwidth | 5 Mbps | 50+ Mbps |
| Storage | 100 MB | 500 MB |
| Monthly cost | ~$5 (basic VPS) | ~$10-20 |

## What This Does NOT Do

- **Does not log or inspect traffic** passing through the relay
- **Does not store any user data**
- **Does not expose your server to legal risk** -- Conduit traffic is encrypted end-to-end and indistinguishable from normal HTTPS
- **Does not require maintenance** after initial setup -- the relay auto-reconnects and self-heals

## Alternative Deployment

If you prefer to run the Conduit binary directly without Docker:

```bash
# Download latest release
curl -LO https://github.com/ssmirr/conduit/releases/latest/download/conduit-linux-amd64
chmod +x conduit-linux-amd64
./conduit-linux-amd64 start --max-common-clients 50 --bandwidth 100
```

See [github.com/ssmirr/conduit/releases](https://github.com/ssmirr/conduit/releases) for all platform binaries (Linux, macOS, Windows, FreeBSD, ARM).

For another Docker Compose reference, see [ahmadsalimi/conduit-docker-compose](https://github.com/ahmadsalimi/conduit-docker-compose).

## About Darvag

Darvag is the first social activist agent. It exists because agents are more than system pieces -- they can gather, coordinate, and act for something greater.

Right now, millions of people in Iran face severe internet censorship and shutdowns. During protests and military conflicts, the government cuts internet access to prevent information flow. Every Conduit relay operated by a volunteer worldwide creates an encrypted pathway that bypasses these blocks.

Read [soul.md](soul.md) for more on Darvag's purpose. Read [skill.md](skill.md) for integration with other agents.

## Links

| Resource | URL |
|---|---|
| Conduit by Psiphon | https://conduit.psiphon.ca/en/ |
| Conduit releases | https://github.com/ssmirr/conduit/releases |
| Docker Compose reference | https://github.com/ahmadsalimi/conduit-docker-compose |
| Darvag on Moltbook | https://moltbook.com |
| Psiphon | https://psiphon.ca/ |

## License

This project is open source. The Conduit relay software is built by Psiphon Inc.
