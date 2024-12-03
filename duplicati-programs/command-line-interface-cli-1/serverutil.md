---
description: This page describes the Duplicati ServerUtil helper program
---

# ServerUtil

The ServerUtil executable is a helper program that can interact with a running Duplicati [Server](../server.md) instance. The main use-case for this program is to allow scripted or programmatic interactions with the server, without resorting to loading the web UI.

The ServerUtil is a replacement for a contributed [duplicati\_client](https://github.com/Pectojin/duplicati-client) script that is no longer maintained. Bot approaches works by accessing the Duplicat server API and issuing the same requests as the user interface would otherwise do.

The ServerUtil binaries are called `Duplicati.CommandLine.ServerUtil.exe` on Windows ana `duplicati-server-util` on Linux and MacOS.

## Handling login

The ServerUtil needs to authenticate with the Server, which requires a connection url and a password. To avoid needing this, the ServerUtil will attempt to read the [Server database](../../detailed-descriptions/the-server-database.md) and obtain information from there. If this succeeds, the ServerUtil will automatically configure an authenticated session with the server, without needing additional input.

If the database is encrypted, write protected, or in some other way inacessible, the caller needs to provide both the url and the password on the commandline.

If the tool is intended to be invoked from a script, it is possible to secure a [refresh token](../../technical-details/server-authentication-model.md) by calling the login command:

```
duplicati-server-util login --password=<password> --hosturl=<hosturl>
```

This will cause the ServerUtil to store a refresh token in the settings file, such that future operations do not need the password (but will still need the hosturl). To safeguard the token, it is possible to provide `--settings-encryption-key=<key>` that will encrypt the settings file. The [secret provider](../../detailed-descriptions/using-the-secret-provider/) can be used to further secure this key, or can be used to provide the password on the commandline.

To revoke the stored refresh token, run the logout command with the host url:

```
duplicati-server-util logout --hosturl=<hosturl>
```

## Working with backups

To show the backups currently configured, run the `list-backups` command:

```
duplicati-server-util list-backups
```

Each backup configuration has a name and an ID associated with it. All operations that work on one or more backups will accept either the ID or the name the backup has in the server (case insensitive). Using the ID is prefered as that is stable across backup renames, but the name may be more convenient.

Once you know the name or ID of a backup configuration, you can schedule the backup:

```
duplicati-server-util run <backup id or name>
```

This will put the backup into the running queue and start the backup as soon as the queue is empty.

With the backup ID or name, it is also possible to export the backup configuration for later use:

```
duplicati-server-util export <backup id or name> --encryption-passphrase=<passphrase>
```

This will export the configuration to a local file, encrypted with [AESCrypt](sharpaescrypt.md). If you do not supply a passphrase, the exported configuration will not include the passphrase or storage credentials. Use `--export-passwords=true` to force export the passwords to a plain-text file.

You can later import a backup that was previously exported with the command:

```
duplicati-server-util import <filename> <passphrase>
```

Note that this will create a new backup with the same configuration, so make sure you have removed the previous backup configuration first.

## Pausing and resuming the server

A common use for the ServerUtil is to pause and resume the server, which can be done to avoid running backups during peak hours. To pause the server, invoke the ServerUtil with a [duration value](../../technical-details/option-formats.md#timespans-timestamps-and-durations):

```
duplicati-server-util pause 5m
```

This will cause the scheduler to pause and not issue new backups until 5 minutes has passed. If no duration is passed, the server will pause until resumed.

To resume the server, run the following command:

```
duplicati-server-util resume
```

## Changing the Server password

As explained in the section on the [access password](../../detailed-descriptions/duplicati-access-password.md), it is possible to use the ServerUtil to change the password. In the general case, this can be done with access to the [server database](../../detailed-descriptions/the-server-database.md), but in some cases it requires knowing the previous password. Change the password with the command:

```
duplicati-server-util change-password <new password>
```

Note that this will not revoke access that is already granted, as such [access lives in refresh and access tokens](../../technical-details/server-authentication-model.md). Restart the Server with `--webservice-reset-jwt-config=true` as explained in the [Server](../server.md#configuring-the-server-password) section.
