# Lua Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why Lua: it's embedded in Redis (Lua scripts), Nginx (OpenResty), Neovim,
> World of Warcraft, and many IoT firmwares (NodeMCU for ESP8266/ESP32).
> It's a tiny language (30-page manual) that takes 2 hours to learn syntax-wise.
> The value is in the specific contexts where it lives.

---

### Lua-A — Nginx Scripting with OpenResty ★

**Companion to**: Lab 05 (Nginx), Lab 16 (Kong)  
**Time box**: 6 hours  
**What**: Replace Kong with OpenResty (Nginx + Lua) and implement three
Kong-like features yourself: JWT verification, rate limiting, and request logging.

**Why**: Kong is built on OpenResty. Understanding the substrate makes you
a much better Kong (and Nginx) user. Also, many companies use OpenResty
directly instead of Kong for maximum control.

**What you'll learn**:
- Lua syntax (tables, functions, coroutines in 30 min)
- The `ngx.*` API: `ngx.req`, `ngx.resp`, `ngx.var`, `ngx.log`
- Shared memory (`lua_shared_dict`) for rate limiter state across workers
- Why Lua in Nginx is fast: runs inside Nginx worker processes, no subprocess overhead

**Done when**:
- [ ] OpenResty in Docker, proxying to Lab 03's TypeScript API
- [ ] `access_by_lua_block`: verify JWT signature using `resty.jwt`
- [ ] `access_by_lua_block`: rate limiter using `lua_shared_dict` (atomic increment)
- [ ] `log_by_lua_block`: write structured JSON access log
- [ ] All three features tested with `curl` test script

**Key insight**: Kong's rate limiting plugin is ~150 lines of Lua. After this lab
you could read that source code and understand it completely.

---

### Lua-B — Redis Scripting ★

**Companion to**: Lab 09 (Redis)  
**Time box**: 4 hours  
**What**: Write 5 atomic Redis operations as Lua scripts. These cannot be
implemented safely without Lua (or Lua-equivalent transactions).

**Scripts to implement**:

```lua
-- 1. getset_with_expiry(key, value, ttl)
-- Atomically: get old value, set new value with TTL
-- Needed when: you want the previous value AND to update atomically

-- 2. rate_limit(key, limit, window_seconds) → {allowed: bool, remaining: int}
-- Atomically: increment counter, set expiry on first call, return count

-- 3. pop_and_push(source_list, dest_list, max_items) → moved_count
-- Atomically move up to N items from one list to another
-- Needed when: implementing a work queue with a "processing" list

-- 4. sorted_set_upsert_with_decay(key, member, score, decay_factor)
-- Update score, but apply exponential decay to existing score first
-- Used for: trending algorithms (old high-score items decay over time)

-- 5. conditional_set(key, value, ttl, expected_value) → {updated: bool}
-- Set only if current value matches expected (optimistic locking)
```

**Done when**:
- [ ] All 5 scripts implemented and callable from TypeScript via `redis.eval()`
- [ ] Each script has tests that verify atomicity (call from two processes simultaneously)
- [ ] Scripts benchmarked: Lua vs equivalent multi-command approach (measure RTTs saved)

---

### Lua-C — IoT Firmware for ESP8266 / NodeMCU ★★

**Companion to**: Lab 24 (Arduino + MQTT)  
**Time box**: 8 hours  
**Hardware**: ESP8266 NodeMCU board (~R$20) — cheaper than Arduino + WiFi shield  
**What**: Replace the Arduino + Python bridge with a single ESP8266 running
Lua firmware. The ESP8266 reads the DHT22 sensor AND publishes directly to
MQTT over WiFi. No serial bridge needed.

**Why this is significant**: This is a real production IoT pattern.
The ESP8266 is used in millions of commercial IoT devices. Lua on NodeMCU
gives you scripting speed on hardware with 80KB of RAM.

**What you'll learn**:
- NodeMCU Lua API: `gpio`, `i2c`, `net`, `mqtt`, `tmr`
- The event loop model in Lua (single-threaded, callback-based — familiar from Node.js)
- WiFi connection management (reconnect on drop)
- MQTT client in Lua (directly to Mosquitto, no bridge)
- Flash memory constraints: your entire program must fit in ~256KB

**Done when**:
- [ ] ESP8266 reads DHT22 and publishes to MQTT over WiFi
- [ ] Reconnects to WiFi and MQTT automatically on drop
- [ ] Deep sleep between readings (extends battery life 100x)
- [ ] OTA (over-the-air) firmware update working
- [ ] All data from Lab 24's Grafana dashboard still populates correctly

---

### Lua-D — Game Scripting: Love2D Simple Game ★

**Companion to**: Nothing — this is a fun one  
**Time box**: 6 hours  
**What**: Build a simple 2D game in Love2D (a Lua game framework). The game:
a side-scrolling character that collects items. The twist: game logic and
physics are data-driven from a JSON config file, and you implement the
scripting API that modifies game behaviour at runtime (like WoW addons).

**Why**: Game scripting is one of the places where Lua became dominant.
Understanding why — the sandboxed execution model, the embeddability —
teaches you something about language design that applies to how you think
about scripting and DSLs in general.

**Done when**:
- [ ] Playable game: character moves, collects items, has a score
- [ ] Level data in external JSON file (no hardcoded level design)
- [ ] Modding API: a separate `mod.lua` file can change physics constants
  and add items without modifying game source
- [ ] One automated test: use Love2D's headless mode to simulate 10 frames
  and assert expected game state

---
