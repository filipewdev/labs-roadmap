# Lab 22 — Flutter Local-First + Offline Sync

**Goal**: Build a note-taking app that works completely offline and syncs when
connected — with a conflict resolution UI when two devices diverge.

**Time box**: 10 hours (your most complex mobile lab)

**Language**: Dart/Flutter + TypeScript (sync server)

**State management**: Riverpod (`flutter_riverpod`)

**Done when**:
- [ ] Notes stored in local SQLite via Drift — app works with zero network
- [ ] Sync with a TypeScript server when WiFi is available
- [ ] Conflict scenario reproducible: edit the same note on two emulators while offline, sync both
- [ ] Conflict resolution UI: user sees both versions and picks one
- [ ] Sync status visible per note: `pending`, `synced`, `conflict`
- [ ] Background sync via `workmanager` every 15 minutes
- [ ] State managed with Riverpod: `StreamProvider` wrapping Drift's reactive queries, `AsyncNotifierProvider` for sync operations, `Provider` for repository dependencies
- [ ] Written comparison: BLoC (Lab 21) vs Riverpod (this lab) — which worked better for what, and why

**Key concepts**:
- Drift (SQLite ORM for Dart): tables, DAOs, reactive streams, migrations
- Drift DAOs: encapsulate queries per table, return `Stream<List<T>>` for reactive UI
- Drift migrations: schema versioning for when your local DB evolves between app versions
- Sync protocol: version numbers or vector clocks
- Conflict detection: when local and server have diverged from the same base
- Optimistic UI: show changes immediately, reconcile with server later
- `Connectivity` package: detecting and reacting to network changes
- **Riverpod providers**: `Provider` (sync), `FutureProvider` (async one-shot), `StreamProvider` (reactive streams), `NotifierProvider` (mutable state with logic)
- **Why Riverpod fits here**: Drift returns `Stream<List<Note>>` — Riverpod's `StreamProvider` makes this reactive without boilerplate. BLoC would require more ceremony for the same reactivity.
- **Dependency injection with Riverpod**: repositories, services, and database as overridable providers (makes testing easy)

**Local environment**: The TypeScript sync server runs via `docker-compose.yml` with PostgreSQL. `docker compose up -d` gives you a working sync backend for the Flutter app to connect to.

**Testing requirement**: Unit tests for the conflict detection logic (given local version, server version, base version → is there a conflict?). This logic must be tested completely independently of the UI and network. **CI (server)**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage). **CI (Flutter)**: `dart analyze` → `flutter test --coverage`.

**AI usage note**: The sync algorithm is genuinely hard. Use AI as a rubber duck: *"I'm building offline sync with the following approach. What conflict scenarios does my detection miss?"*

---

## Phase 11 — Hardware & IoT

---
