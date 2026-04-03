# Homelab Infrastructure Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: the core curriculum teaches you to build systems. This track
> teaches you to host and operate them on your own hardware. The goal is a
> personal cloud — a self-hosted environment where you deploy everything you
> build, plus daily-use services (file storage, media streaming, VPN).
>
> Several core labs are foundations: Lab 05 (Nginx), Lab 14 (Docker Compose),
> Lab 16 (Kong), Lab 18 (Linux Admin), Lab 23 (Raspberry Pi). This track
> builds on top of them.
>
> Full architecture vision: [homelab-architecture.md](../homelab-architecture.md)

---

### Infra-A — Proxmox: Virtualization From Bare Metal ★

**Companion to**: Lab 18 (Linux Admin), Lab 23 (Raspberry Pi)
**Time box**: 8 hours
**What**: Install Proxmox VE on bare metal (or a spare machine), create VMs
and LXC containers, and understand the hypervisor layer that sits under
everything else in a homelab.

**Why Proxmox**: It's free, runs KVM and LXC, has ZFS support built in, and
is what most serious homelabs run. Understanding the VM/container boundary
at this level makes every Docker decision clearer.

**What you'll learn**:
- KVM vs LXC containers (when each is appropriate)
- ZFS basics: pools, datasets, snapshots, scrubbing
- Network bridges and VLANs inside Proxmox
- VM templates and cloud-init (spin up a new Ubuntu VM in 30 seconds)
- Backup and restore: Proxmox Backup Server or built-in `vzdump`

**Done when**:
- [ ] Proxmox installed on bare metal (not inside a VM)
- [ ] One VM created from a cloud-init template (Ubuntu Server)
- [ ] One LXC container running a lightweight service (e.g., Pi-hole)
- [ ] ZFS pool configured with at least one snapshot taken and restored
- [ ] Network bridge configured — VMs and containers can reach each other and the LAN
- [ ] Backup of one VM completed and restore verified

---

### Infra-B — WireGuard VPN: Secure Remote Access ★

**Companion to**: Lab 18 (Linux Admin), Infra-A (Proxmox)
**Time box**: 5 hours
**What**: Set up a WireGuard VPN server on your homelab so you can securely
access all services from anywhere — phone, laptop, coffee shop WiFi.

**Why WireGuard**: It's the modern VPN standard — faster, simpler, and more
auditable than OpenVPN. The entire codebase is ~4,000 lines (OpenVPN is
~100,000). Understanding it teaches you real networking: routing tables,
NAT traversal, and key exchange.

**What you'll learn**:
- WireGuard key pairs and the cryptokey routing model
- Split tunneling vs full tunneling (route only homelab traffic vs everything)
- NAT traversal and persistent keepalives
- `iptables` / `nftables` forwarding rules for VPN traffic
- DNS resolution inside the tunnel (Pi-hole or Unbound over VPN)

**Done when**:
- [ ] WireGuard server running on a Proxmox VM or LXC container
- [ ] Phone connects via WireGuard and reaches homelab services
- [ ] Laptop connects from external network and accesses internal IPs
- [ ] Split tunnel configured: only homelab subnet routes through VPN
- [ ] DNS queries from VPN clients resolve internal hostnames
- [ ] `wg show` output understood and documented

---

### Infra-C — Traefik: Docker-Native Reverse Proxy ★

**Companion to**: Lab 05 (Nginx), Lab 14 (Docker Compose)
**Time box**: 6 hours
**What**: Replace static Nginx config with Traefik — a reverse proxy that
auto-discovers Docker services and provisions SSL certificates from
Let's Encrypt automatically.

**Why Traefik over Nginx for a homelab**: In Lab 05 you learned Nginx config
by hand — that's essential knowledge. But in a homelab with 15+ services,
manually updating Nginx config files doesn't scale. Traefik reads Docker
labels and generates routes automatically. One `docker compose up` and the
service is routable with HTTPS.

**What you'll learn**:
- Traefik entrypoints, routers, services, and middleware
- Docker provider: auto-discovery via container labels
- Let's Encrypt ACME with DNS challenge (works behind NAT, no port 80 needed)
- Middleware chains: rate limiting, basic auth, IP allowlisting
- Dashboard: real-time view of all routes and their health

**Done when**:
- [ ] Traefik running in Docker Compose with the Docker provider enabled
- [ ] At least 3 services routed via Docker labels (e.g., Grafana, n8n, a lab API)
- [ ] Let's Encrypt wildcard certificate via DNS challenge (DuckDNS or Cloudflare)
- [ ] HTTP → HTTPS redirect on all routes
- [ ] One middleware chain applied (e.g., basic auth on the Traefik dashboard)
- [ ] A new service becomes routable by adding 3 Docker labels — no config file changes

---

### Infra-D — Harbor: Private Container Registry ★★

**Companion to**: Lab 14 (Docker Compose), Infra-C (Traefik)
**Time box**: 6 hours
**What**: Run your own container registry so you can `docker push` images
built from your labs and deploy them anywhere in your homelab without
touching Docker Hub.

**Why Harbor**: It's the CNCF-graduated registry. It adds vulnerability
scanning (Trivy), RBAC, image signing, and replication — all things you
need when your homelab grows beyond "one Pi running Docker."

**What you'll learn**:
- OCI image spec: what a registry actually stores (manifests, layers, blobs)
- Trivy vulnerability scanning integrated into push workflow
- Image retention policies (auto-delete old tags, keep last N)
- Robot accounts for CI/CD (GitHub Actions pushes to your Harbor)
- Replication: mirror Docker Hub images locally (faster pulls, offline access)

**Done when**:
- [ ] Harbor running behind Traefik with a valid SSL certificate
- [ ] One lab image pushed to Harbor and pulled on a different machine
- [ ] Trivy scan runs automatically on push — vulnerabilities visible in UI
- [ ] Retention policy configured: keep only last 5 tags per repo
- [ ] GitHub Actions workflow pushes images to Harbor on tag
- [ ] Robot account used for CI (not your personal credentials)

---

### Infra-E — Portainer: Docker Fleet Management ★

**Companion to**: Lab 14 (Docker Compose), Infra-A (Proxmox)
**Time box**: 4 hours
**What**: Install Portainer to manage Docker across multiple hosts (your Pi,
a Proxmox VM, your dev machine) from a single UI.

**Why Portainer**: The CLI is great for one machine. When you have 3+ Docker
hosts, Portainer gives you a single pane of glass — deploy stacks, inspect
logs, manage volumes, and control access. Understanding the difference between
CLI-first and UI-first Docker management is a real ops skill.

**What you'll learn**:
- Portainer agent architecture (agent on each host, server on one)
- Stacks: deploying docker-compose via Portainer (GitOps-style from a repo)
- Environment management: grouping and tagging Docker hosts
- Access control: team-based permissions per environment
- App templates: one-click deploys for common services

**Done when**:
- [ ] Portainer server running with at least 2 connected environments
- [ ] One stack deployed via Portainer from a Git repository
- [ ] Container logs viewable and searchable through Portainer UI
- [ ] One app template deployed (e.g., Uptime Kuma for status monitoring)
- [ ] Understand when Portainer helps vs when `docker compose` CLI is better

---

### Infra-F — Home Services: SFTP + Media Streaming ★

**Companion to**: Lab 14 (Docker Compose), Lab 18 (Linux Admin)
**Time box**: 5 hours
**What**: Set up the daily-use services that make a homelab actually useful:
a secure file server (SFTP) and a media streaming server (Jellyfin).

**Why**: A homelab that only runs dev experiments gets neglected. One that
also serves your movies and secures your files gets used every day. Daily
usage is what teaches you operations — you notice when things break because
you actually depend on them.

**What you'll learn**:
- SFTP configuration: chroot jails, key-only auth, per-user directories
- Jellyfin/media server: transcoding, hardware acceleration, library management
- Storage planning: mount points, permissions, disk monitoring
- Backup integration: these services have real data you can't lose
- Docker volumes vs bind mounts for persistent media data

**Done when**:
- [ ] SFTP server accessible externally via VPN (not exposed to internet)
- [ ] Per-user directories with chroot isolation
- [ ] Jellyfin streaming media on LAN and via VPN
- [ ] Storage health monitoring (disk usage alerts in Grafana)
- [ ] Automated backup of SFTP data and Jellyfin config to a separate drive or cloud

---
