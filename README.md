# pwman

A secure, minimal CLI password and secret manager featuring authenticated encryption, key rotation, and a cinematic Matrix-style UI.

## Features

-   **Authenticated Encryption**: Uses AES-GCM to ensure your vault's confidentiality and integrity.
-   **Strong Key Derivation**: Master keys are derived using PBKDF2-HMAC-SHA256 with 100,000 iterations.
-   **Key Rotation**: Update your master passphrase anytime; the entire vault is re-encrypted with a fresh key.
-   **API Key Storage**: Securely store existing secrets like API keys without them touching your shell history.
-   **Interactive Generation**: Customize length and character types (no symbols, no digits, etc.) with real-time suggestions.
-   **Matrix Animation**: Cinematic "Right-to-Left" character reveal when suggesting or retrieving passwords.
-   **Secure Permissions**: Automatically restricts vault file access to the owner only (`0600`).

## Installation

### Prerequisites

-   Python 3.7+
-   `cryptography` library

```bash
pip install cryptography
```

### Setup

Ensure `pwman` is in your PATH:

```bash
chmod +x /usr/local/bin/pwman
```

## Custom Vault Location

By default, `pwman` stores your secrets in `~/.pwman_vault.json`. You can customize this location using the global `--vault` flag or the `PWMAN_VAULT` environment variable.

**Using the flag:**
```bash
pwman --vault /path/to/my/secret_vault.json list
```

**Using the environment variable:**
```bash
export PWMAN_VAULT="/path/to/my/secret_vault.json"
pwman list
```

## Usage

### 1. Create a Password (Interactive)
Generate a new password with specific constraints.
```bash
pwman create github --length 20 --no-symbols
```
*The password will reveal from right to left. Choose `(a)ccept`, `(r)egenerate`, or `(q)uit`.*

### 2. Store an Existing Secret (API Keys, Seedphrases)
Secure an existing secret. For long items like 24-word seedphrases, use the `--visible` flag to see your input and avoid typos.
```bash
pwman store my_crypto_wallet --visible
```
*Prompts securely for the secret value. If `--visible` is used, it uses a standard prompt instead of a hidden one.*

### 3. Update an Existing Secret
Modify the value of a secret already in the vault.
```bash
pwman update github
```

### 4. Delete a Secret
Permanently remove an entry.
```bash
pwman delete old_service
```

### 5. Retrieve a Secret
Decrypt and display a stored secret with a Matrix reveal effect.
```bash
pwman get github
```

### 4. List All Entries
Show stored names and their associated key versions.
```bash
pwman list
```

### 5. Rotate Master Key
Change your master passphrase and re-encrypt the entire vault.
```bash
pwman rotate
```

### 6. Help
Display detailed usage and flags.
```bash
pwman help
```

## Security Architecture

1.  **Vault Structure**: The entire vault is a JSON object, which is then serialized and encrypted.
2.  **Encryption**: `AES-GCM` provides authenticated encryption, meaning any tampering with the encrypted file will be detected during decryption.
3.  **Key Derivation**: Your passphrase never touches the disk. It is used to derive a 256-bit key via `PBKDF2` using a unique 16-byte salt stored in the vault metadata.
4.  **Key Versioning**: Each entry tracks which key version was used to encrypt it, facilitating seamless rotation.

## License
MIT
