# Lab 17 — Bash Scripting Fundamentals

**Goal**: Write real shell scripts that automate things you actually need.
Not toy examples.

**Time box**: 6 hours

**Language**: Bash

**Done when**:
- [ ] Script 1: Database backup — pg_dump, compress, upload to Backblaze B2 or Cloudflare R2 (S3-compatible CLI), notify on failure
- [ ] Script 1b: Restore script — download from cloud, decompress, restore to a fresh database, verify row count matches. **A backup without a tested restore is not a backup.**
- [ ] Script 2: Log analysis — parse Nginx JSON logs, top 10 paths, 5xx error rate
- [ ] Script 3: Health check — ping N services, report which are down, exit 1 if any fail
- [ ] All scripts start with `set -euo pipefail`
- [ ] All scripts have a `--help` flag
- [ ] Scripts tested with `bats` (Bash Automated Testing System)

**Key concepts**:
- `set -euo pipefail` — why every script must start with this
- Variables, arrays, loops, conditionals in Bash
- `jq` for JSON parsing in shell
- Exit codes and their meaning
- `trap` for cleanup on exit or signal

**Testing requirement**: Use `bats`. Test all three scripts — not just the health check. Test the health check with a mock server returning different status codes. Test the log analysis script with a sample JSON log file. Testing shell scripts feels weird — that's normal, do it anyway. **CI**: `bats test/` in GitHub Actions.

**AI usage note**: Bash syntax is inconsistent. Use AI freely for specific syntax questions: *"What does `${variable:-default}` mean?"* The concepts (error handling, exit codes) should be your own understanding.

---
