# Security

PlebTap uses a defense-in-depth approach to protect your private keys. This document explains the security architecture and your options for protecting your wallet.

## Authentication Methods

When you create or import a wallet, you choose **one** authentication method:

### PIN Authentication

- Choose a **4-digit** or **6-digit** numeric PIN
- Your private key is encrypted using your PIN
- PIN is never stored - only used for key derivation

**Best for**: Users who want familiar PIN-based security

### Biometric Authentication (WebAuthn)

- Uses your device's Face ID, Touch ID, or platform authenticator
- A random 256-bit encryption key protects your private key
- The key is only accessible after successful biometric verification

**Best for**: Users with devices that have biometric hardware

### Insecure Storage (None)

- Your private key is encrypted with a fixed internal key
- **No authentication required** to access your wallet
- Wallet auto-unlocks on page load

!!! danger "Development Only"
    Insecure storage should only be used for testing and development. Your keys can be accessed by anyone with access to your browser.

**Best for**: Development, testing, or throwaway accounts

## How Encryption Works

```
┌─────────────────────────────────────────────────────────────┐
│                     Key Storage Flow                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   PIN Method:                                               │
│   ┌─────────┐    PBKDF2     ┌──────────────┐               │
│   │   PIN   │ ───────────►  │ Derived Key  │               │
│   └─────────┘   600K iter   └──────────────┘               │
│                                    │                        │
│                                    ▼                        │
│   ┌─────────────┐  AES-256-GCM  ┌──────────────────────┐   │
│   │ Private Key │ ────────────► │ Encrypted Key Blob   │   │
│   └─────────────┘               │ (stored in IndexedDB)│   │
│                                 └──────────────────────┘   │
│                                                             │
│   WebAuthn Method:                                          │
│   ┌─────────────────┐           ┌──────────────┐           │
│   │ Random 256-bit  │           │ Derived Key  │           │
│   │      Key        │ ────────► │ (from random)│           │
│   └─────────────────┘           └──────────────┘           │
│          │                             │                    │
│          │                             ▼                    │
│          │              ┌──────────────────────┐           │
│          │              │ Encrypted Key Blob   │           │
│          │              │ (stored in IndexedDB)│           │
│          │              └──────────────────────┘           │
│          │                                                  │
│          ▼                                                  │
│   ┌──────────────────────────────────┐                     │
│   │ Random key stored in IndexedDB   │                     │
│   │ (accessible after WebAuthn OK)   │                     │
│   └──────────────────────────────────┘                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Encryption Details

| Component | Algorithm | Parameters |
|-----------|-----------|------------|
| Key Derivation | PBKDF2-SHA256 | 600,000 iterations |
| Encryption | AES-256-GCM | 256-bit key, 96-bit IV |
| PIN Hash | PBKDF2-SHA256 | 100,000 iterations |

## Storage

All sensitive data is stored in **IndexedDB**, not localStorage:

- **Encrypted private key blob** - Your key encrypted with your chosen method
- **PIN verification hash** - For validating PIN entry (not the PIN itself)
- **WebAuthn credentials** - Public key and counter for replay protection
- **WebAuthn encryption key** - Random key for WebAuthn method (if used)

## Session Management

When you unlock your wallet:

1. The decrypted key is held **in memory only**
2. Key is cleared on logout or tab close
3. Key is cleared when you close the browser

### Auto-Lock (Opt-in)

By default, your session stays active until you log out or close the browser. You can enable auto-lock in Settings:

- **When enabled**: Session expires after **5 minutes** of inactivity
- **When disabled** (default): Session persists until page close
- Key is always cleared from memory when session ends

!!! tip "Shared Devices"
    Enable auto-lock if you use PlebTap on a shared or public device.

## Rate Limiting

To protect against brute-force attacks:

- **5 failed attempts** triggers a lockout
- **5-minute lockout** period after max attempts
- Counter resets after **1 hour** of no attempts

### What Happens During Lockout

When you reach the maximum failed attempts:

1. The PIN input is **disabled** and hidden
2. A **countdown timer** shows remaining lockout time (e.g., "4:23")
3. The **Unlock button** is removed
4. After lockout expires, you can try again with fresh attempts

### Attempts Remaining

After each failed PIN attempt, you'll see how many attempts remain before lockout:

```
Incorrect PIN. 3 attempts remaining.
```

This helps you know when to stop and think carefully about your PIN before getting locked out.

## Recovery

Your wallet can always be recovered using your **seed phrase** (mnemonic):

1. Navigate to "Import Seed Phrase"
2. Enter your 12-word (or 24-word) BIP-39 mnemonic
3. Set up new authentication
4. Your original Nostr identity is restored

!!! tip "NIP-06 Compatible"
    PlebTap follows [NIP-06](https://github.com/nostr-protocol/nips/blob/master/06.md) for key derivation, making your keys portable across compatible wallets.

## Threat Model

### What We Protect Against

| Threat | Protection |
|--------|------------|
| Casual device access | PIN/Biometric required to unlock |
| Key theft from storage | AES-256-GCM encryption |
| PIN brute force | PBKDF2 + rate limiting |
| Replay attacks | WebAuthn counter verification |
| Memory scraping | Session timeout + key clearing |

### Limitations

| Threat | Status |
|--------|--------|
| Rooted/jailbroken devices | Limited protection |
| Physical device access + time | PIN could be brute-forced offline |
| Browser extensions with full access | Cannot protect against malicious extensions |
| Compromised device OS | Cannot protect |

## Best Practices

1. **Use 6-digit PIN** for better security over 4-digit
2. **Enable biometrics** if your device supports it
3. **Back up your seed phrase** offline, never digitally
4. **Lock your session** when stepping away
5. **Use a dedicated browser profile** for wallet access
6. **Keep your device OS updated** for security patches

## Changing Authentication

You can change your authentication method in Settings:

1. Go to **Settings > Security**
2. Enter current PIN or use biometric
3. Choose new authentication method
4. Complete setup with new credentials

Note: Changing authentication re-encrypts your key with the new method.
