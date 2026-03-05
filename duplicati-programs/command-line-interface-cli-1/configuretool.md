---
description: This page describes the Duplicati ConfigureTool for managing HTTPS certificates.
---

# ConfigureTool

The ConfigureTool is a command-line utility for managing HTTPS certificates and other configuration settings for Duplicati. It provides a convenient way to generate, renew, and manage SSL certificates for secure web UI access.

The ConfigureTool binary is called `Duplicati.CommandLine.ConfigureTool.exe` on Windows and `duplicati-configure` on Linux and macOS.

{% hint style="info" %}
The configure tool was added in Canary 2.2.0.106
{% endhint %}

## Overview

Duplicati can automatically generate HTTPS certificates for secure web UI access. Since Duplicati uses localhost serving by default, there is no external Certificate Authority (CA) to request certificates from. Instead, Duplicati generates its own local CA and uses it to sign server certificates. This approach allows automatic certificate renewal without requiring manual intervention every 90 days.

{% hint style="warning" %}
**Security Notice:** The CA private key is stored in the Duplicati database. If someone gains access to the database, they could use the CA to sign certificates for other domains, potentially enabling man-in-the-middle attacks. Enable database encryption for maximum security.
{% endhint %}

## HTTPS Command

The `https` subcommand manages HTTPS certificates. It supports the following operations:

### Generating Certificates

To generate a new CA and server certificate:

```bash
duplicati-configure https generate
```

This command will:

1. Generate a new local Certificate Authority (CA)
2. Create a server certificate signed by the CA
3. Install the CA certificate in the system trust store
4. Store the certificates in the Duplicati database

#### Options

| Option                      | Description                                                                                                             |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `--hostnames`               | Comma-separated list of hostnames to include in the certificate (defaults to auto-detected hostnames)                   |
| `--no-trust`                | Skip installing the CA certificate in the system trust store                                                            |
| `--auto-create-database`    | Create the database if it does not exist                                                                                |
| `--data-folder`             | Path to the Duplicati data folder (defaults to standard location)                                                       |
| `--settings-encryption-key` | Settings encryption key for the database (if settings are encrypted)                                                    |
| `--store`                   | (Windows only) Certificate store location: `local` or `user`. Defaults to `local` if running as admin, otherwise `user` |
| `--cert-dir`                | (Linux only) Custom certificate directory for installing CA certificate                                                 |
| `--keychain`                | (macOS only) Custom keychain path for installing CA certificate                                                         |

#### Examples

Generate certificates without installing to system trust store (useful for Flatpak/Snap browsers):

```bash
duplicati-configure https generate --no-trust
```

Generate certificates with specific hostnames:

```bash
duplicati-configure https generate --hostnames="localhost,duplicati.local,192.168.1.100"
```

### Renewing Certificates

Server certificates are valid for 90 days and will be renewed automatically. To manually renew the server certificate using the existing CA:

```bash
duplicati-configure https renew
```

#### Options

| Option                      | Description                              |
| --------------------------- | ---------------------------------------- |
| `--data-folder`             | Path to the Duplicati data folder        |
| `--settings-encryption-key` | Settings encryption key for the database |

### Regenerating the CA

If you suspect your CA has been compromised or want to start fresh, regenerate the CA and server certificate:

```bash
duplicati-configure https regenerate-ca
```

This will:

1. Remove the old CA from the trust store
2. Generate a new CA and server certificate
3. Install the new CA in the trust store
4. Store the new certificates in the database

{% hint style="warning" %}
Regenerating the CA will invalidate any previously trusted certificates.
{% endhint %}

#### Options

| Option                      | Description                                                  |
| --------------------------- | ------------------------------------------------------------ |
| `--hostnames`               | Comma-separated list of hostnames to include                 |
| `--no-trust`                | Skip installing the CA certificate in the system trust store |
| `--data-folder`             | Path to the Duplicati data folder                            |
| `--settings-encryption-key` | Settings encryption key for the database                     |
| `--store`                   | (Windows only) Certificate store location                    |
| `--cert-dir`                | (Linux only) Custom certificate directory                    |
| `--keychain`                | (macOS only) Custom keychain path                            |

### Removing Certificates

To remove the CA from the trust store and delete all certificate data from the database:

```bash
duplicati-configure https remove
```

This is useful when:

- You no longer need HTTPS
- You suspect the CA has been compromised
- You want to clean up before uninstalling Duplicati

#### Options

| Option                      | Description                               |
| --------------------------- | ----------------------------------------- |
| `--data-folder`             | Path to the Duplicati data folder         |
| `--settings-encryption-key` | Settings encryption key for the database  |
| `--store`                   | (Windows only) Certificate store location |
| `--cert-dir`                | (Linux only) Custom certificate directory |
| `--keychain`                | (macOS only) Custom keychain path         |

### Displaying Certificate Status

To view the current certificate status:

```bash
duplicati-configure https show
```

This displays:

- CA certificate details (subject, issuer, validity dates, trust store status)
- Server certificate details (subject, issuer, validity dates, DNS names, IP addresses)
- Database encryption status
- Certificate expiration status

#### Options

| Option                      | Description                               |
| --------------------------- | ----------------------------------------- |
| `--data-folder`             | Path to the Duplicati data folder         |
| `--settings-encryption-key` | Settings encryption key for the database  |
| `--store`                   | (Windows only) Certificate store location |
| `--cert-dir`                | (Linux only) Custom certificate directory |
| `--keychain`                | (macOS only) Custom keychain path         |

### Exporting Certificates

#### Export CA Certificate

To export the CA certificate (public key only) for manual import into browsers:

```bash
duplicati-configure https export-ca
```

This creates `duplicati-ca.crt` in the current directory. To specify a custom path:

```bash
duplicati-configure https export-ca --file ~/Desktop/duplicati-ca.crt
```

#### Export Server Certificate

To export the server certificate (public key only):

```bash
duplicati-configure https export
```

This creates `duplicati-server.crt` in the current directory. To specify a custom path:

```bash
duplicati-configure https export --file ~/Desktop/duplicati-server.crt
```

## Browser Trust Configuration

### Chrome and Firefox on Linux

Chrome and Firefox on Linux maintain their own certificate stores and may not automatically trust the system CA. This is especially true for sandboxed installations (Snap, Flatpak).

#### Importing to Firefox

1. Export the CA certificate: `duplicati-configure https export-ca`
2. Open Firefox and go to **Settings** → **Privacy & Security**
3. Scroll to **Certificates** and click **View Certificates**
4. Select the **Authorities** tab
5. Click **Import** and select the exported `duplicati-ca.crt` file
6. Check **"Trust this CA to identify websites"** and click OK

#### Importing to Chrome

1. Export the CA certificate: `duplicati-configure https export-ca`
2. Open Chrome and go to **Settings** → **Privacy and security** → **Security**
3. Click **Manage certificates**
4. Select the **Authorities** tab
5. Click **Import** and select the exported `duplicati-ca.crt` file
6. Check **"Trust this certificate for identifying websites"** and click OK

{% hint style="info" %}
For Flatpak or Snap installations, browsers run in a sandbox that may restrict file access. If you encounter "error reading file" during import, copy the certificate to `/tmp/` before importing.
{% endhint %}

## Security Model

### Local-Only CA

- The CA is generated locally on your machine and is not shared with any external service
- The CA certificate is installed only in your system's local trust store
- Other machines do not trust this CA unless explicitly configured to do so
- The CA should never be exported or shared with other systems

### CA Constraints

The CA certificate has `pathLenConstraint=0` in its Basic Constraints extension, which means:

- The CA can sign end-entity (server) certificates
- The CA **cannot** sign subordinate/intermediate CA certificates
- This limits the scope of trust to only certificates directly signed by this CA

### Certificate Validity Periods

- **CA Certificate:** Valid for approximately 10 years
- **Server Certificate:** Valid for 90 days (browser requirement)
- **Auto-renewal:** Server certificates are automatically renewed 30 days before expiration

### CA Key Storage Security

The CA private key is protected with multiple layers:

1. **Encryption:** AES-256 encryption is applied to the key
2. **Password Separation:** The encryption password is stored separately from the encrypted key
3. **Database Encryption:** If database field encryption is enabled, an additional encryption layer is applied

## Database Encryption Recommendation

For maximum security, enable database field encryption, default enabled since Canary 2.2.0.105.

If database encryption is not enabled, a warning notification is emitted on startup, explaining that the CA private key is readable from the database.

## Revocation and Compromise Response

If you suspect your CA private key has been compromised:

1. **Immediate Action:** Remove the certificates using `duplicati-configure https remove`
2. **Regenerate:** Create a new CA with `duplicati-configure https regenerate-ca`
3. **Review:** Check recent server logs and backup history for unauthorized access
4. **Monitor:** Set up alerts for unexpected certificate changes

## Using Custom Certificates

If you prefer providing your own certificate instead of using the auto-generated CA, you can do so by setting the `server-ssl-certificate` and `server-ssl-certificatepassword` settings. When using custom certificates:

- Auto-renewal will not be activated
- No CA will be generated
- You are responsible for certificate renewal

See the [Server documentation](../server.md) for details on configuring custom SSL certificates.

## Exit Codes

The ConfigureTool returns the following exit codes:

| Code | Meaning                                                           |
| ---- | ----------------------------------------------------------------- |
| 0    | Success                                                           |
| 1    | General error (certificate generation failed, export error, etc.) |

Error messages are printed to the console with details about what went wrong.
