# VaultKey — Personal Encryption Tool (Version 1)

A local-first browser application for encrypting text and files using the Web Crypto API.

## Features

- AES-256-GCM authenticated encryption
- Master-password mode using PBKDF2-HMAC-SHA-256 (600,000 iterations)
- Random 256-bit raw key-file mode
- Text encryption/decryption
- File, image, document and configuration-file encryption/decryption
- Portable `.vaultkey` JSON envelope
- GitHub Pages hosting
- Offline app shell using a service worker

## Security design

- All cryptographic operations run in the browser.
- The app has no backend, database, analytics, or network API calls.
- Passwords and raw key bytes are not persisted by the app.
- Each password-based encryption uses a fresh 16-byte salt and 12-byte AES-GCM nonce.
- AES-GCM uses a 128-bit authentication tag.
- Envelope metadata is authenticated as additional authenticated data (AAD).
- The app does not implement password recovery.

**Important:** GitHub Pages serves the application code. It does not receive encryption keys or plaintext through this app. However, if the hosted JavaScript is compromised or replaced, a malicious version could capture a password when entered. For highly sensitive material, use a trusted offline copy and verify the code you run.

## Requirements

- A modern browser with Web Crypto support.
- HTTPS or localhost. `crypto.subtle` is generally unavailable on an ordinary insecure HTTP origin.
- Keep encrypted files and passwords/key files backed up separately.

## Run locally

1. Extract the ZIP.
2. Open a terminal in the extracted `personal-encryption-tool-v1` folder.
3. Run:

   ```bash
   python -m http.server 8000
   ```

4. Visit `http://localhost:8000` in your browser.

The first visit loads the app. The service worker caches the application shell so it can be used offline after it has been loaded once. Keep the local server running while using the app at localhost.

You can also use another local static web server. Do not use an untrusted public computer.

## Publish on GitHub Pages

1. Create a **private GitHub repository** if you want to keep the source code less visible. (A private repository does not make a public Pages website private.)
2. Upload the contents of this folder to the repository root.
3. Open **Settings → Pages**.
4. Select **Deploy from a branch**, choose `main` and `/ (root)`, then save.
5. Wait for deployment and open the HTTPS Pages URL.
6. Test encryption and decryption with non-sensitive sample data.

Do not commit `.key` files, passwords, personal encrypted data, or private notes to the repository.

## Basic test plan

- Encrypt a short text using a master password, then decrypt it using the same password.
- Try decrypting with a wrong password; it must fail.
- Change one character in the encrypted JSON; authentication must fail.
- Generate a key file, encrypt a small image or text file using key-file mode, then decrypt with that same key file.
- Try a different key file; decryption must fail.
- Encrypt on localhost and decrypt on GitHub Pages, and then test the reverse.
- Reload the app after first loading it online, disconnect the network, and verify that the cached app shell opens.

## Version 1 limitations

- File operations are in-memory and intended for files up to 50 MB.
- Encrypted files use JSON plus Base64, which increases file size.
- No password manager vault, sync, sharing, account system, or recovery mechanism.
- This is a personal utility starter, not a formally audited cryptographic product.

## File format

The `.vaultkey` file is a JSON envelope containing:
- format and version identifiers
- algorithm and key-derivation metadata
- salt (password mode)
- nonce / IV
- authenticated metadata (content type, filename, MIME type)
- Base64 ciphertext (including the AES-GCM authentication tag)

## License

MIT — see `LICENSE`.
