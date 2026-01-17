# Key Storage

PlebTap stores all sensitive data in **IndexedDB** with encryption. This document explains the storage architecture.

## Storage Location

All wallet data is stored in IndexedDB under the database name `plebtap-secure-storage`. This includes:

| Data | Description |
|------|-------------|
| `encryptedKey` | Your private key encrypted with your chosen method |
| `encryptedMnemonic` | Your seed phrase encrypted (if created with mnemonic) |
| `publicKeyHex` | Your public key hex (unencrypted, for deriving npub when locked) |
| `authMethod` | Your authentication method (`pin`, `webauthn`, or `none`) |
| `pinLength` | Your PIN length (4 or 6 digits) |
| `autoLockSession` | Whether auto-lock is enabled (default: off) |
| `webauthnCredential` | WebAuthn credential for biometric auth |
| `webauthnEncryptionKey` | Random key for WebAuthn encryption |

## Encryption Format (NIP-49)

PlebTap uses **NIP-49** for key encryption, producing standard Bech32-encoded strings:

| Data Type | Format | Example |
|-----------|--------|---------|
| Private Key | `ncryptsec1...` | Encrypted with password via scrypt + XChaCha20-Poly1305 |
| Mnemonic | `ncryptmnem1...` | Same encryption, custom prefix for seed phrases |

This format is interoperable with other Nostr tools that support NIP-49.

## Key Derivation

For PIN-based encryption:

```
PIN → scrypt (log_n=16, r=8, p=1) → 256-bit key → XChaCha20-Poly1305
```

For WebAuthn, a random 256-bit password is generated and used with the same NIP-49 encryption. The password is stored in IndexedDB and only accessible after biometric verification.

## Wallet States

The trigger button reflects the current wallet state:

| State | Button Display | Description |
|-------|---------------|-------------|
| Initializing | Loading skeleton | Checking for stored credentials |
| Loading | "Loading..." spinner | Auto-login in progress |
| Locked | 🔒 "Locked" | Secured wallet needs unlock |
| Ready | Balance display | Wallet unlocked and ready |
| No credentials | "Start" | First-time user |

## Mnemonic Storage

When you create a new account with a seed phrase:

1. A 12-word (default) or 24-word mnemonic is generated
2. Your Nostr private key is derived using NIP-06
3. Both the mnemonic and private key are encrypted separately
4. You can export your mnemonic later from Settings

## Exporting Keys

From Settings > Keys, you can export:

- **npub**: Your public key in Bech32 format (safe to share)
- **nsec**: Your private key in Bech32 format (keep secret!)
- **hex**: Your private key in raw hexadecimal format
- **Mnemonic**: Your seed phrase (if stored)

!!! tip "Backup Before Export"
    Make sure you've backed up your seed phrase before relying on exports. Digital copies are vulnerable to theft.

## Clearing Data

You can clear all wallet data from Settings:

1. Go to **Settings > Security**
2. Select **Clear All Data**
3. Confirm the action

This removes:

- All encrypted keys
- Authentication settings
- WebAuthn credentials
- Cached wallet data

!!! warning "Irreversible"
    Clearing data without a seed phrase backup means permanent loss of access to your wallet.
