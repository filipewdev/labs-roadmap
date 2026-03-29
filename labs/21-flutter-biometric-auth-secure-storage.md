# Lab 21 — Flutter Biometric Auth + Secure Storage

**Goal**: Replace plaintext token storage with biometric-gated hardware-backed
secure storage. Understand what "secure" actually means at the OS level.

**Time box**: 8 hours

**Language**: Dart/Flutter

**State management**: BLoC (`flutter_bloc`)

**Done when**:
- [ ] Auth tokens stored in iOS Keychain / Android Keystore via `flutter_secure_storage`
- [ ] Biometric prompt shown on app launch (fingerprint or Face ID)
- [ ] Graceful fallback to PIN when biometrics unavailable
- [ ] App auto-locks after 5 minutes in background
- [ ] Sensitive screen has screenshot prevention (`FLAG_SECURE` on Android)
- [ ] On logout, all secure storage cleared
- [ ] Auth state managed with BLoC: events (`AppStarted`, `BiometricSucceeded`, `BiometricFailed`, `PinEntered`, `AppBackgrounded`, `LogoutRequested`) drive state transitions (`AuthInitial` → `AuthBiometricPrompt` → `AuthAuthenticated` → `AuthLocked`)
- [ ] Written state diagram showing all transitions (Mermaid or ASCII in README)

**Key concepts**:
- iOS Secure Enclave vs Android Keystore (hardware security modules)
- `KeychainAccessibility` options — what "first unlock" means
- Biometric as a key to unlock stored credentials, not as the credential itself
- App lifecycle states and when to trigger auto-lock
- `FLAG_SECURE` — how it prevents screenshots and screen recording
- **BLoC pattern**: events in, states out — why auth is a textbook finite state machine
- **Cubit vs Bloc**: when you need explicit events (Bloc) vs when simple methods are enough (Cubit)
- `BlocObserver` for logging every state transition (useful for debugging auth flows)

**Testing requirement**: Widget tests for the lock screen UI states. Unit tests for the auto-lock timer logic. **BLoC tests using `blocTest`** — verify every state transition (e.g., `AppStarted` → `AuthBiometricPrompt`, `BiometricFailed` → `AuthPinPrompt`). Mock the biometric service — you cannot test actual biometrics in CI. **CI**: `dart analyze` → `flutter test --coverage`.

**AI usage note**: Security library APIs are complex and platform-specific. Use AI to understand `IOSOptions` and `AndroidOptions` parameters — the documentation is scattered.

---
