# Lab 13 — Container Internals: Build One From Scratch

**Goal**: Understand what a container actually is by creating one using only
Linux kernel features — no Docker involved until the end.

**Time box**: 8 hours

**Language**: Go + Shell

**Done when**:
- [ ] Manual namespace exploration completed (PID, UTS, NET using `unshare`)
- [ ] Manual cgroup limit applied and tested (`/sys/fs/cgroup`)
- [ ] OverlayFS mounted manually and the layer concept understood
- [ ] Go program (~150 lines) that runs a process in isolated namespaces
- [ ] Memory limit enforced: OOM kill demonstrated inside the mini-container
- [ ] Written explanation of what `docker run` does at the kernel level

**Key concepts**:
- Linux namespaces: what each type isolates (PID, NET, MNT, UTS, USER, IPC)
- cgroups v2: limiting CPU and memory
- OverlayFS: how image layers stack (lower=read-only, upper=read-write)
- `pivot_root`: how a container gets its own filesystem root
- Why containers are not VMs (shared kernel)

**Testing requirement**: Shell test script that starts the mini-container with a memory limit, runs a memory-hungry process, and asserts it was killed. Manual verification is acceptable for namespace isolation.

**AI usage note**: Prefer `man unshare`, `man cgroups`, and kernel docs over AI for this lab. AI is fine for Go syntax questions.

---
