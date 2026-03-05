---
description: This page explains how to configure HTTPS for secure web UI access in Duplicati.
---

# Configuring HTTPS

{% hint style="info" %}
**Available since:** Canary 2.2.0.106
{% endhint %}

Duplicati can automatically generate HTTPS certificates for secure web UI access. This document explains the security model and provides platform-specific instructions for configuring HTTPS.

## Certificate Approach

Browsers currently reject certificates that have a validity period for more than 90 days, with the logic that rotation should be frequent and automated to prevent long-term exposure to potential vulnerabilities. Since Duplicati uses localhost serving by default, there is no Certificate Authority (CA) to request certificates from.

If Duplicati creates a self-signed certificate, it would need to be re-authorized after 90 days, which would require admin permissions and possibly manual intervention every 90 days. To avoid this, Duplicati generates its own Certificate Authority (CA) and uses it to sign server certificates.

The CA private key is stored in the Duplicati database file, which is encrypted with the database encryption key. When a new certificate is needed, Duplicati generates a new certificate signed by the CA. Since the CA is local and not shared with any external service, this approach provides a secure way to manage certificates without requiring external dependencies. As the CA is trusted by the user or system, this enables Duplicati to automatically renew certificates without user intervention, in a way that is trusted by the user's browser.

{% hint style="warning" %}
**Security Warning:** While the CA is local, it is still a CA and can be used to sign certificates for other domains. If someone gains access to the Duplicati database, they can use the CA to sign certificates for other domains, essentially providing an undetected man-in-the-middle attack.
{% endhint %}

If you prefer providing your own certificate, you can do so by setting the `server-ssl-certificate` and `server-ssl-certificatepassword` settings. This will not activate auto-renewal or generate a CA.

## Certificate Authority (CA) Security Model

When HTTPS is configured, Duplicati creates a local Certificate Authority (CA) with the following security properties:

### Local-Only CA

- The CA is generated locally on your machine and is not shared with any external service
- The CA certificate is installed only in your system's local trust store
- Other machines do not trust this CA unless explicitly configured to do so
- The CA should never be exported or shared with other systems, unless you understand the security implications

### Certificate Validity Periods

- **CA Certificate:** Valid for approximately 10 years
- **Server Certificate:** Valid for 90 days
- **Auto-renewal:** Server certificates are automatically renewed 30 days before expiration

## CA Key Storage Security

The CA private key is stored with multiple layers of protection:

1. **Encryption:** The private key is encrypted using AES-256 with a password-derived key
2. **Password Separation:** The encryption password is stored separately from the encrypted key
3. **Database Encryption:** If database field encryption is enabled, an additional encryption layer is applied

{% hint style="warning" %}
**Important:** The security of your HTTPS certificates depends on the security of your Duplicati database file. Ensure that database encryption is enabled.
{% endhint %}

## Platform-Specific Configuration

### Windows

On Windows, the CA certificate is installed in the certificate store. By default, it uses the LocalMachine store when running as Administrator, or CurrentUser store when running as a regular user. A dialog is shown to confirm the installation location when installing without Administrator privileges.

#### Generating Certificates on Windows

Open Command Prompt or PowerShell as Administrator (for system-wide trust) or as a regular user (for user-only trust):

```cmd
duplicati-configure https generate
```

To specify a particular certificate store:

```cmd
duplicati-configure https generate --store=local
```

Valid store values are:

- `local` or `machine` - Install in the LocalMachine store (requires Administrator privileges)
- `user` or `currentuser` - Install in the CurrentUser store

#### Trust Store Location

The CA certificate is installed in:

- **LocalMachine:** `Cert:\LocalMachine\Root` (system-wide trust)
- **CurrentUser:** `Cert:\CurrentUser\Root` (user-only trust)

### Linux

On Linux, the CA certificate is installed in distribution-specific certificate directories. The ConfigureTool will automatically detect the correct location for your distribution.

#### Generating Certificates on Linux

Run the ConfigureTool with appropriate privileges (typically requires sudo for system-wide trust):

```bash
sudo duplicati-configure https generate
```

To specify a custom certificate directory:

```bash
sudo duplicati-configure https generate --cert-dir=/usr/local/share/ca-certificates/
```

#### Common Certificate Directories

Different Linux distributions use different paths for CA certificates:

- Debian/Ubuntu: `/usr/local/share/ca-certificates/`
- RHEL/CentOS/Fedora: `/etc/pki/ca-trust/source/anchors/`
- SUSE: `/etc/pki/trust/anchors/`
- Alpine: `/usr/local/share/ca-certificates/`

The ConfigureTool will automatically detect the correct location for your distribution.

#### Chrome and Firefox on Linux

Chrome and Firefox on Linux maintain their own certificate stores and may not automatically trust the system CA. This is especially true for sandboxed installations (Snap, Flatpak).

##### Exporting the CA Certificate

First, export the CA certificate for manual import:

```bash
duplicati-configure https export-ca --file ~/duplicati-ca.crt
```

##### Importing to Firefox

1. Open Firefox and go to **Settings** → **Privacy & Security**
2. Scroll to **Certificates** and click **View Certificates**
3. Select the **Authorities** tab
4. Click **Import** and select the exported `duplicati-ca.crt` file
5. Check **"Trust this CA to identify websites"** and click OK

##### Importing to Chrome

1. Open Chrome and go to **Settings** → **Privacy and security** → **Security**
2. Click **Manage certificates**
3. Select the **Authorities** tab
4. Click **Import** and select the exported `duplicati-ca.crt` file
5. Check **"Trust this certificate for identifying websites"** and click OK

{% hint style="info" %}
For Flatpak or Snap installations, browsers run in a sandbox that may restrict file access. If you encounter "error reading file" during import, copy the certificate to a location accessible by the sandbox:

```bash
cp ~/duplicati-ca.crt /tmp/duplicati-ca.crt
chmod 644 /tmp/duplicati-ca.crt
# Then import from /tmp in the browser
```

{% endhint %}

##### Avoiding System Store Trust

Since Flatpak or Snap browsers don't use the system certificate store anyway, you can avoid installing the CA in the system trust store by using the `--no-trust` flag:

```bash
duplicati-configure https generate --no-trust
```

This generates a CA certificate without requiring elevated privileges. You can then export and manually import it into your browser.

### macOS

On macOS, the CA certificate is installed in the System keychain by default, which requires administrator privileges.

#### Generating Certificates on macOS

Run the ConfigureTool with administrator privileges:

```bash
sudo duplicati-configure https generate
```

To specify a custom keychain:

```bash
duplicati-configure https generate --keychain=/Library/Keychains/System.keychain
```

#### Trust Store Location

By default, the CA certificate is installed in:

- System keychain: `/Library/Keychains/System.keychain` (requires administrator privileges)

The ConfigureTool uses the `security add-trusted-cert` command to add the certificate to the trust store.

## Revocation and Compromise Response

If you suspect your CA private key has been compromised:

1. **Immediate Action:** Remove the certificates using:
   ```bash
   duplicati-configure https remove
   ```
2. **Regenerate:** Create a new CA with:
   ```bash
   duplicati-configure https regenerate-ca
   ```
3. **Review:** Check recent server logs and backup history for unauthorized access
4. **Monitor:** Set up alerts for unexpected certificate changes

## Certificate Pinning Considerations

For high-security environments, consider implementing certificate pinning:

- Extract the CA certificate thumbprint after generation
- Configure clients or monitoring systems to expect only this specific CA
- This prevents acceptance of certificates signed by other CAs that might be installed on the system

## Viewing Certificate Status

To check the current status of your HTTPS certificates:

```bash
duplicati-configure https show
```

This displays:

- CA certificate details (subject, issuer, validity dates)
- Whether the CA is installed in the trust store
- Server certificate details (subject, issuer, validity dates, DNS names, IP addresses)
- Database encryption status
- Certificate expiration status

## Renewing Certificates

Server certificates are automatically renewed 30 days before expiration. To manually renew the server certificate:

```bash
duplicati-configure https renew
```

## Removing HTTPS Configuration

To remove HTTPS certificates and stop using HTTPS:

```bash
duplicati-configure https remove
```

This will:

- Remove the CA certificate from the system trust store
- Delete all certificate data from the Duplicati database

## Using Custom Certificates

If you prefer to use your own certificates instead of the auto-generated CA:

1. Obtain a certificate and private key from a trusted CA (or create your own)
2. Configure Duplicati Server with the `server-ssl-certificate` and `server-ssl-certificatepassword` options
3. Note that auto-renewal will not be available when using custom certificates

See the [Server documentation](../duplicati-programs/server.md) for details on configuring custom SSL certificates.

## See Also

- [ConfigureTool Reference](../../duplicati-programs/command-line-interface-cli-1/configuretool.md) - Complete command reference
- [Duplicati Access Password](duplicati-access-password.md) - Managing server access
- [The Server Database](../database-and-storage/the-server-database.md) - Database configuration
