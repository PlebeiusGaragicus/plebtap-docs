# Roadmap

Upcoming features and improvements for PlebTap.

## Current Version

### v0.1.x - Foundation

- [x] Basic Nostr authentication (NIP-07, private key, mnemonic)
- [x] Cashu ecash wallet integration
- [x] NIP-49 key encryption (scrypt + XChaCha20-Poly1305)
- [x] PIN-based authentication (4 or 6-digit)
- [x] WebAuthn biometric authentication
- [x] Quick access mode (no security)
- [x] Mnemonic and private key export (nsec + hex)
- [x] NIP-06 key derivation from seed phrases

## Planned Features

### v0.2.x - Enhanced Security

- [ ] Change PIN functionality
- [ ] Switch authentication method
- [ ] Device linking via QR code
- [ ] Session timeout configuration

### v0.3.x - Wallet Features

- [ ] Transaction history
- [ ] Multi-mint management UI
- [ ] Token consolidation
- [ ] Lightning address support

### v0.4.x - Social Features

- [ ] Contact list management
- [ ] NIP-17 encrypted DMs
- [ ] Zap support

## Long-term Goals

- **Hardware wallet support** - Sign with external devices
- **Multi-account support** - Switch between Nostr identities
- **Offline mode** - Queue transactions for later
- **Mobile app** - Native iOS/Android via Tauri or Capacitor

## Contributing

Want to help build PlebTap? Check out the [GitHub repository](https://github.com/PlebeiusGaragicus/plebtap) for open issues and contribution guidelines.
