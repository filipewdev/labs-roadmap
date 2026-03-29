# Lab 23 — Raspberry Pi as a Production Server

**Goal**: Turn a fresh Raspberry Pi into a hardened, monitored server running
one of your previous labs — everything that a VPS tutorial skips.

**Time box**: 6 hours

**Language**: Shell + YAML

**Done when**:
- [ ] Pi boots from USB SSD (not SD card)
- [ ] SSH hardened (key auth, fail2ban)
- [ ] Docker + Docker Compose installed
- [ ] One previous lab deployed and accessible externally
- [ ] DuckDNS dynamic DNS configured
- [ ] Backup script running via cron (database dump → Backblaze B2 or Cloudflare R2 via S3-compatible CLI)
- [ ] CPU temperature visible in Grafana via Node Exporter

**Key concepts**:
- ARM64 architecture and its Docker implications (multi-arch images)
- Thermal throttling — why Raspberry Pis need heatsinks under load
- SD card vs SSD for production reliability
- Dynamic DNS — accessing a home server without a static IP
- Port forwarding — exposing the Pi to the internet

**Testing requirement**: Follow a verification runbook after setup. Document what breaks and how you fixed it — this is the most valuable part of this lab. The runbook must include automated checks where possible (e.g., `curl -sS https://yourdomain | grep -q "expected"`, `systemctl is-active docker`).

**AI usage note**: Use AI for ARM64-specific Docker troubleshooting: *"This image doesn't have an arm64 variant — what are my options?"* The system administration concepts (systemd, firewall, SSH hardening) should be your own understanding.

---
