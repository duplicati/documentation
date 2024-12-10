---
description: This page describes the database kept by the Duplicati Server
---

# The server database

When the [Server](../duplicati-programs/server.md) is running, either stand-alone or as part of the [TrayIcon](../duplicati-programs/trayicon.md) or [Agent](../duplicati-programs/agent.md), it needs a place to store the configuration. All configuration data, logs and settings are stored inside the file `Duplicati-server.sqlite`. As the file extension reveals, this is an [SQLite](https://www.sqlite.org) database file and as such can be viewed and updated by any tool that works with SQLite databases.

Due to the nature of Duplicati, this database can contain a few secrets that are vital to ensuring the integrity and security of the backups and also the Duplicati server itself. These secrets include both the user-provided secrets, such as the backup encryption passphrase and the connection credentials, but also server-provided secrets, such as the token signing keys, and optionally an SSL certificate password.

Even though the database is located on the machine that makes the backup, it is important to prevent unauthorized access to the database, as it could be used for privilege escalation. And should the database ever be leaked, it is also important to ensure the contents are not accessible.

To protect the database, Duplicati has support for a field-level encryption password. When activated, any setting that is deemed sensitive will be encrypted before being written to the database. This method ensures that the SQLite database itself is still readable, but the secrets are not readable without the encryption passphrase.

To supply the field-level encryption password, start the Server, TrayIcon, or Agent with the commandline option `--settings-encryption-key=<key>`. As the commandline can usually be read by other processes, it is also possible to supply this key via the environment variable `SETTINGS_ENCRYPTION_KEY=<key>`.

Both the commandline arguments and environment variables can be set with the [Preload settings](preload-settings.md) file, which makes it simpler to apply the same settings across executables, and removes the need for changing the service or launcher files.

Finally, the [operating system Keychain, or an external secret provider](using-the-secret-provider/), can be used to further secure the encryption key.
