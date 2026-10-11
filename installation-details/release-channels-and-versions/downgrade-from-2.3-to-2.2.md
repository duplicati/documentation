---
description: This page describes how to downgrade from Duplicati 2.3 to 2.2
---

# Downgrade from 2.3 to 2.2

Duplicati 2.3 updated the [server database](../../detailed-descriptions/database-and-storage/the-server-database.md) to version 11 and the [local databases](../../detailed-descriptions/database-and-storage/the-local-database.md) to version 19. Duplicati 2.2 expects server version 9 and local version 17, and will not start with a database that has a newer version. See [Database versions](../../technical-details/database-versions.md) for an overview.

{% hint style="info" %}
Note: make sure you run the DatabaseTool from the 2.3 installation **before** you downgrade Duplicati. The older DatabaseTool does not know how to downgrade the newer databases.
{% endhint %}

## Remove the database encryption

Starting with 2.3, if no `--settings-encryption-key` is provided, Duplicati encrypts the server database with a random key that is stored in the operating system's secret store (Windows Credential Manager, MacOS Keychain, or `libsecret`/`pass` on Linux). Duplicati 2.2 does not read the key from there, so it cannot open the encrypted fields.

If you have not provided your own encryption key, stop all running instances and start the [Server](../../duplicati-programs/server.md) or [TrayIcon](../../duplicati-programs/trayicon.md) once with:

```
duplicati-server --disable-db-encryption=true
```

This removes the field-level encryption from the server database. Stop Duplicati again before continuing.

If you provide your own key with `--settings-encryption-key` or the `SETTINGS_ENCRYPTION_KEY` environment variable, you can skip this step, as long as you also provide the same key to Duplicati 2.2.

## Downgrade the databases

Make sure Duplicati is not running, and then use the [DatabaseTool](../../duplicati-programs/command-line-interface-cli-1/databasetool.md) to downgrade the server database and all local databases:

```
duplicati-database-tool downgrade --server-version=9 --local-version=17
```

After the downgrade is complete, uninstall Duplicati 2.3 and install 2.2.

## Obtaining older releases

The [installer packages for 2.2.0.3](https://github.com/duplicati/duplicati/releases/tag/v2.2.0.3_stable_2026-01-06) are available on Github. You can [browse the list of releases](https://github.com/duplicati/duplicati/releases) for other versions you may want.
