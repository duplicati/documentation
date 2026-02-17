---
description: This page describes the database kept by the Duplicati Server
---

# The server database

When the [Server](../duplicati-programs/server.md) is running, either stand-alone or as part of the [TrayIcon](../duplicati-programs/trayicon.md) or [Agent](../duplicati-programs/agent.md), it needs a place to store the configuration. All configuration data, logs and settings are stored inside the file `Duplicati-server.sqlite`. As the file extension reveals, this is an [SQLite](https://www.sqlite.org) database file and as such can be viewed and updated by any tool that works with SQLite databases.

The database file is by default located in a folder that belongs to the user account running it. See the section on the [database location](../duplicati-programs/server.md#storing-data-in-different-places) for details on where this is and how to change it.

## Securing the database

Due to the nature of Duplicati, this database can contain a few secrets that are vital to ensuring the integrity and security of the backups and also the Duplicati server itself. These secrets include both the user-provided secrets, such as the backup encryption passphrase and the connection credentials, but also server-provided secrets, such as the token signing keys, and optionally an SSL certificate password.

Even though the database is located on the machine that makes the backup, it is important to prevent unauthorized access to the database, as it could be used for privilege escalation. And should the database ever be leaked, it is also important to ensure the contents are not accessible.

To protect the database, Duplicati has support for a field-level encryption password. When activated, any setting that is deemed sensitive will be encrypted before being written to the database. This method ensures that the SQLite database itself is still readable, but the secrets are not readable without the encryption passphrase.

To supply the field-level encryption password, start the Server, TrayIcon, or Agent with the commandline option `--settings-encryption-key=<key>`. As the commandline can usually be read by other processes, it is also possible to supply this key via the environment variable `SETTINGS_ENCRYPTION_KEY=<key>`.

If you are aware of the risks, you can also set the commandline argument `--disable-db-encryption=true` instead of the key. This will remove existing encryption and not warn that the database is not encrypted.

The simplest way to apply an encryption key, is to locate the server database, and create the file `preload.json` if it does not already exist. The file should contain the following:

```json
{
  "env": {
    "*": { 
      "SETTINGS_ENCRYPTION_KEY": "<key>"
    }
  }
}
```

Both the commandline arguments and environment variables can be set with the [Preload settings](preload-settings.md) file, which makes it simpler to apply the same settings across executables, and removes the need for changing the service or launcher files.

For additional protection of the encryption key, the [operating system Keychain, or an external secret provider](using-the-secret-provider/), can be used to further secure the encryption key.

## Database location

When running Duplicati for the first time, it will find a place where it can store the configuration database. Some versions of Duplicati change the location where it looks for the databases, but this is always done backwards compatible, so new versions will also find the database in previous locations. Due to this logic, the locations can change a bit depending on what version of Duplicati was originally installed.

It is possible to pick a different location for the database with the commandline option `--server-datafolder=<path>` or use the environment variable `DUPLICATI_HOME`.

To change the folder of an existing instance of Duplicati, perform these steps:

1. Stop Duplicati
2. Move the `Duplicati` folder from the old location to the new location
3. Change the startup parameters (environment variables, commandline arguments, or preload.json)
4. Start Duplicati again

### Database location on Windows

The default location for users running Duplicati is `%LOCALAPPDATA%\Duplicati` which usually resolves to something like `C:\Users\username\AppData\Local\Duplicati`. This folder is the non-roaming folder. Older versions of Duplicati used `%APPDATA%\Duplicati` which is the roaming folder, causing files to be synchronized across machines. However, since Duplicati is not meant to be an app that is useful for roaming, it is now using the non-roaming folder.

When running Duplicati as a Windows Service, the `%LOCALAPPDATA%\Duplicati` folder resolves to:

```
C:\Windows\System32\config\systemprofile\AppData\Local\Duplicati
```

Since this folder is under `C:\Windows` the contents may be deleted on major Windows upgrades (usually when the version number changes). For that reason, Duplicati will detect an attempt to store files in the `C:\Windows` folder and emit a warning. From version **2.1.0.108** and forward, Duplicati will avoid using a folder under `C:\Windows` and instead choose to use:

```
C:\ProgramData\Duplicati
```

### Database location on Linux

The default location when running Duplicati on Linux is `~/.config/Duplicati`. For most distros, running Duplicati as a service means running it as the root users, resulting in `/root/.config/Duplicati`.&#x20;

However, due to some compatibility mapping, the mapping is sometimes missing the prefix, causing Duplicati data to be stored in `/Duplicati`. From version 2.1.0.108, this location is avoided and the location `/var/lib/Duplicati` is used instead, if possible.

### Database location on MacOS

The default location when running Duplicati on MacOS is `~/Library/Application Support/Duplicati`. Duplicati version 2.0.8.1 and older used the Linux-style `~/.config/Duplicati` but this is avoided since version 2.1.0.2.
