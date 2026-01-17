# PlebTap

PlebTap is a Bitcoin Lightning and Ecash wallet integrated with Nostr for decentralized identity and payments.

## Features

- **Nostr Identity**: Use your Nostr keys for authentication and identity
- **Ecash Payments**: Send and receive Cashu ecash tokens
- **Lightning Integration**: Connect to Lightning Network via Cashu mints
- **Flexible Security**: Choose PIN, biometric (WebAuthn), or insecure storage

## Quick Start

1. **Create or Import Keys**: Generate a new Nostr identity or import an existing seed phrase/nsec
2. **Secure Your Wallet**: Choose PIN, biometric, or skip security for development
3. **Add Funds**: Receive ecash tokens or connect a Lightning wallet
4. **Start Transacting**: Send payments, receive tokens, and interact with the Nostr ecosystem

## Authentication Options

| Method | Security Level | Best For |
|--------|---------------|----------|
| **PIN (4 or 6 digit)** | Medium-High | Most users |
| **Biometric (WebAuthn)** | High | Devices with Face ID/Touch ID |
| **Insecure (None)** | None | Development/testing only |

## Architecture

PlebTap is built with:

- **Svelte 5** with runes for reactive state management
- **NDK (Nostr Development Kit)** for Nostr protocol interactions
- **Cashu** for ecash token management
- **IndexedDB** for secure encrypted storage
- **Web Crypto API** (AES-256-GCM) for encryption
- **WebAuthn** for biometric authentication

## Documentation

- [Security](security.md) - How your keys are protected
- [Key Storage](key-storage.md) - Storage architecture details
- [Roadmap](roadmap.md) - Upcoming features and improvements

## Recovery

Your wallet can always be recovered using your **12-word seed phrase** (mnemonic). This follows the [NIP-06](https://github.com/nostr-protocol/nips/blob/master/06.md) standard for deriving Nostr keys from BIP-39 mnemonics.

You can also export your **nsec** (private key) and **mnemonic** from Settings > Keys at any time.

!!! warning "Backup Your Seed Phrase"
    Write down your seed phrase and store it securely offline. Anyone with your seed phrase can access your funds.
