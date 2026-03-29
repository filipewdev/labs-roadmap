# Lab 18 — Linux System Administration

**Goal**: Set up a hardened VPS or Raspberry Pi from a blank OS to a running,
monitored Docker host — everything done by hand.

**Time box**: 6 hours

**Language**: Shell + systemd + Nginx config

**Done when**:
- [ ] SSH hardened: key-only auth, non-standard port, fail2ban installed
- [ ] Firewall configured: only ports 22, 80, 443 open (UFW)
- [ ] Docker installed correctly (not via snap)
- [ ] Nginx serving a static page with Let's Encrypt SSL
- [ ] Automatic security updates configured
- [ ] A systemd service created that starts on boot and restarts on failure
- [ ] Node Exporter running as a systemd service, visible in Lab 06 Grafana

**Key concepts**:
- `systemd`: units, services, `journalctl`, `systemctl enable`
- UFW firewall rules and default-deny policy
- Why fail2ban matters for brute force protection
- `crontab` vs systemd timers
- `journalctl` for structured log inspection

**Testing requirement**: Write a runbook (verification checklist in markdown). After setup, follow it to confirm everything works. This is what SREs actually do. Include automated checks where possible (`systemctl is-active`, `ufw status`, `curl` assertions).

**AI usage note**: Use AI freely for `systemd` unit file syntax — it's verbose. The concepts (why `Restart=on-failure`, what `WantedBy=multi-user.target` means) should be your own understanding.

---

## Phase 9 — Automation & AI

---
