# Lab 21 — Flutter Biometric Auth + Secure Storage

**Goal**: Replace plaintext token storage with biometric-gated hardware-backed
secure storage. Understand what "secure" actually means at the OS level.

**Time box**: 7 hours

**Language**: Dart/Flutter

**Done when**:
- [ ] Auth tokens stored in iOS Keychain / Android Keystore via `flutter_secure_storage`
- [ ] Biometric prompt shown on app launch (fingerprint or Face ID)
- [ ] Graceful fallback to PIN when biometrics unavailable
- [ ] App auto-locks after 5 minutes in background
- [ ] Sensitive screen has screenshot prevention (`FLAG_SECURE` on Android)
- [ ] On logout, all secure storage cleared

**Key concepts**:
- iOS Secure Enclave vs Android Keystore (hardware security modules)
- `KeychainAccessibility` options — what "first unlock" means
- Biometric as a key to unlock stored credentials, not as the credential itself
- App lifecycle states and when to trigger auto-lock
- `FLAG_SECURE` — how it prevents screenshots and screen recording

**Testing requirement**: Widget tests for the lock screen UI states. Unit tests for the auto-lock timer logic. Mock the biometric service — you cannot test actual biometrics in CI. **CI**: `dart analyze` → `flutter test --coverage`.

**AI usage note**: Security library APIs are complex and platform-specific. Use AI to understand `IOSOptions` and `AndroidOptions` parameters — the documentation is scattered.

---
