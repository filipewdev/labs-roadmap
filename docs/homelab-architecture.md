# Homelab Architecture Vision

> This document describes the end state of a personal cloud built on homelab
> hardware. It's a living reference — not a build plan. The individual labs
> in [optional-advanced-labs.md](optional-advanced-labs.md) (Infra-A through
> Infra-F) teach each piece in isolation. This doc shows how they connect.

---

## What This Is

A self-hosted environment that serves two purposes:

1. **Dev/ops playground** — deploy and test every lab project, run CI runners,
   experiment with infrastructure without cloud bills
2. **Daily-use services** — file storage (SFTP), media streaming (Jellyfin),
   DNS filtering (Pi-hole), VPN access from anywhere

The constraint: everything runs on hardware you own, on a network you control,
with security you understand end to end.

---

## Target Architecture

```
Internet
    │
    │ :443 (via port forward or Cloudflare Tunnel)
    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         WireGuard VPN                               │
│  Remote access to entire homelab from phone/laptop                  │
│  Split tunnel: only homelab subnet routes through VPN              │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      Proxmox VE (Bare Metal)                        │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  VM: Docker Host (primary)                                   │   │
│  │  ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐   │   │
│  │  │  Traefik    │  │  Portainer   │  │  Harbor          │   │   │
│  │  │  (reverse   │  │  (fleet      │  │  (container      │   │   │
│  │  │   proxy +   │  │   management │  │   registry +     │   │   │
│  │  │   auto SSL) │  │   UI)        │  │   scanning)      │   │   │
│  │  └──────┬──────┘  └──────────────┘  └──────────────────┘   │   │
│  │         │                                                    │   │
│  │         ├── Lab projects (deployed as Docker stacks)         │   │
│  │         ├── Grafana + Prometheus + Loki (observability)      │   │
│  │         ├── n8n (automation)                                  │   │
│  │         ├── PostgreSQL + Redis (shared data layer)           │   │
│  │         └── Any future service (just add Docker labels)      │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  VM: Media & Files                                           │   │
│  │  ├── Jellyfin (media streaming, hardware transcoding)       │   │
│  │  ├── SFTP server (chroot per user, key-only auth)           │   │
│  │  └── Backup agent (→ external drive or cloud)               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  LXC: Network Services (lightweight)                         │   │
│  │  ├── Pi-hole (DNS filtering + local DNS)                    │   │
│  │  ├── WireGuard server                                        │   │
│  │  └── Uptime Kuma (status monitoring)                        │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Raspberry Pi (separate hardware — IoT hub)                  │   │
│  │  ├── HomeHub (Capstone C2)                                   │   │
│  │  ├── MQTT broker (Mosquitto)                                 │   │
│  │  ├── Arduino sensor nodes                                    │   │
│  │  └── YOLOv8 edge inference                                   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ZFS pool: mirrored drives for data, snapshots for rollback        │
└─────────────────────────────────────────────────────────────────────┘
```

---

## How the Pieces Map to Labs

| Layer | Source | Status |
|---|---|---|
| Nginx fundamentals | Lab 05 | Core curriculum |
| Docker Compose patterns | Lab 14 | Core curriculum |
| API gateway (Kong) | Lab 16 | Core curriculum |
| Linux hardening + systemd | Lab 18 | Core curriculum |
| Raspberry Pi server | Lab 23 | Core curriculum |
| Observability stack | Labs 06 + 07 | Core curriculum |
| Proxmox hypervisor | Infra-A | Optional track |
| WireGuard VPN | Infra-B | Optional track |
| Traefik reverse proxy | Infra-C | Optional track |
| Harbor container registry | Infra-D | Optional track |
| Portainer fleet management | Infra-E | Optional track |
| SFTP + media streaming | Infra-F | Optional track |

---

## Suggested Build Order

The infra labs have a natural dependency chain:

```
Infra-A (Proxmox)     ← everything runs on this
    │
    ├── Infra-B (WireGuard)  ← secure access before exposing anything
    │
    ├── Infra-C (Traefik)    ← routing before deploying services
    │       │
    │       ├── Infra-D (Harbor)     ← push your own images
    │       │
    │       └── Infra-E (Portainer)  ← manage everything from one UI
    │
    └── Infra-F (SFTP + Media)  ← daily-use services, can be done anytime after A
```

Start with **Infra-A** (you need a hypervisor) and **Infra-B** (you need VPN
access before exposing anything). After that, order doesn't matter much.

---

## Hardware Considerations

This is intentionally not specified. Hardware decisions depend on budget,
physical space, noise tolerance, and power costs. Some guidelines:

- **Minimum viable**: One used mini-PC (e.g., Dell Optiplex, Lenovo ThinkCentre)
  with 32GB RAM and a 500GB SSD. Runs Proxmox with 3-4 VMs comfortably.
- **Comfortable**: Add a second drive (mirrored ZFS), 64GB RAM, and a
  Raspberry Pi for IoT/edge work.
- **Don't buy everything at once.** Start with what you have. A homelab that
  runs on one old laptop is better than a homelab you planned but never built.

---

## Security Principles

1. **VPN-first access** — nothing exposed to the internet except the VPN endpoint
   (and optionally a Cloudflare Tunnel for public-facing services)
2. **Internal SSL everywhere** — Traefik provisions certificates even for LAN services
3. **Network segmentation** — VLANs separate IoT devices from personal data
4. **Key-only SSH** — no password auth anywhere, fail2ban on every host
5. **Principle of least privilege** — services run as non-root, read-only filesystems
   where possible (same pattern as Lab 14)
6. **Automated backups** — ZFS snapshots + offsite copy. Untested backups don't exist.

---

## What This Is NOT

- Not a production hosting platform for other people's data
- Not a replacement for cloud services where uptime matters (your home power goes out)
- Not something you build in a weekend — this is a long-term project that grows as you learn
- Not specified down to exact commands — the individual Infra labs handle that
