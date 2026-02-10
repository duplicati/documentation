---
description: >-
  Don't let accidental wipe erase your AI's memory and configurations. Secure
  your OpenClaw instance with Duplicati’s encrypted, zero-trust backups to keep
  your sensitive data safe
---

# Using Duplicati to backup OpenClaw

Configuring Duplicati to back up OpenClaw (formerly known as Moltbot or Clawdbot) is a smart move. Because OpenClaw stores highly sensitive data - including plain-text API keys, session tokens for WhatsApp/Telegram, and long-term memory - it is critical that your backup is encrypted and stored securely.

This guide assumes you have [Duplicati installed](https://duplicati.com/download) and OpenClaw running locally.

***

### Step 1: Locate your OpenClaw Data

Before opening Duplicati, you need to know exactly what you are backing up. By default, OpenClaw stores its configuration and "memory" in a hidden directory.

* Linux/macOS: `~/.clawdbot` (or `~/.openclaw` in newer versions)
* Windows: `C:\Users\<YourUsername>\.clawdbot`

Key files in this folder:

* `clawdbot.json`: Your main config and API keys.
* `.bak.X` files: Automatic rotating backups (these also contain secrets).
* `memory/`: Markdown documents containing the bot's learned context.

***

### Step 2: Create a New Backup Job

1. Open the Duplicati Web UI.
2. Click Add backup > Configure a new backup > Next.
3. General Settings:
   * Name: `OpenClaw-Backup`
   * Encryption: Leave as AES-256 (Built-in).
   * Passphrase: Crucial. Generate a strong password and save it in a password manager. Since OpenClaw stores keys in cleartext, your backup _must_ be encrypted.

{% hint style="info" %}
Always remember to store the password in a safe place; without the password you cannot recover the backup!
{% endhint %}

***

### Step 3: Choose a Destination

Duplicati supports dozens of backends. To keep your "AI brain" safe, choose a destination that isn't on the same physical machine:

* Cloud Storage: Backblaze B2, Google Drive, or Dropbox.
* S3 Compatible: If you use a VPS (like DigitalOcean or AWS).
* Local/SSH: A NAS or a second computer on your network.

Remember to test the destination to ensure it is working as expected.

***

### Step 4: Select Source Data

Navigate to the path identified in Step 1.

1. In the file tree, find your user directory.
2. Check the box for `.clawdbot` (or `.openclaw`).
3. Filter Rule (Optional): If you are running OpenClaw via Docker and have massive log files, you might want to exclude `*.log` to save space.

***

### Step 5: Schedule and Retention

* Schedule: Since OpenClaw is an "always-on" assistant, a daily backup is usually sufficient. If you use it for heavy task automation, consider every 6 hours.
* Retention: Use Smart backup retention. This keeps one backup for each of the last 7 days, one for each of the last 4 weeks, and one for each of the last 12 months.

***

### Step 6: Finalize and Run

On the last screen (Options), keep the default Remote volume size (50MB). Click Save and then click Run now to start your first backup.

#### ⚠️ A Note on Security

Recent security audits (Jan 2026) have highlighted that OpenClaw stores credentials in cleartext. If your Duplicati destination AND passphrase is compromised, anyone who finds those files has full access to your connected Telegram, WhatsApp, and LLM accounts (OpenAI/Claude). Treat your Duplicati passphrase like the :key: to your house.
