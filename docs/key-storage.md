# Key Storage

PlebTap stores all sensitive data in **IndexedDB** with encryption. This document explains the storage architecture.

## Storage Location

All wallet data is stored in IndexedDB under the database name `plebtap-secure-storage`. This includes:

| Data | Description |
|------|-------------|
| `encryptedKey` | Your private key encrypted with your chosen method |
| `encryptedMnemonic` | Your seed phrase encrypted (if created with mnemonic) |
| `authMethod` | Your authentication method (`pin`, `webauthn`, or `none`) |
| `pinLength` | Your PIN length (4 or 6 digits) |
| `webauthnCredential` | WebAuthn credential for biometric auth |
| `webauthnEncryptionKey` | Random key for WebAuthn encryption |

## Encryption Format

Encrypted data follows this structure:

```json
{
  "version": "plebtap-v1",
  "algorithm": "aes-256-gcm",
  "salt": "<base64>",
  "iv": "<base64>",
  "ciphertext": "<base64>",
  "kdfParams": {
    "iterations": 600000,
    "algorithm": "PBKDF2-SHA256"
  },
  "createdAt": 1704067200000
}
```

## Key Derivation

For PIN-based encryption, the encryption key is derived using PBKDF2:

```
PIN → PBKDF2-SHA256 (600,000 iterations) → 256-bit AES key
```

For WebAuthn, a random 256-bit key is generated and stored (only accessible after biometric verification).

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

- **npub**: Your public key (safe to share)
- **nsec**: Your private key (keep secret!)
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
