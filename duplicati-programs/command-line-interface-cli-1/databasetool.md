---
description: >-
  This page describes the DatabaseTool command line tool for changing the
  database versions.
---

# DatabaseTool

The DatabaseTool is intended to assist with changing the databases used by Duplicati. Duplicati has a single [settings database for the server](../../detailed-descriptions/database-and-storage/the-server-database.md) and a [local database for each backup job](../../detailed-descriptions/database-and-storage/the-local-database.md), and the DatabaseTool can manage both.

The DatabaseTool is called `Duplicati.CommandLine.DatabaseTool.exe` on Windows and `duplicati-database-tool` on Linux and MacOS. The tool supports the operations `downgrade`, `upgrade`, `list`, `execute`, `verify`, `cleanup`, and `wipe-encryption`. The last three were added in Duplicati 2.4.

{% hint style="info" %}
Note: The database tool must be in the most-recent version for the downgrade to work, so be sure to run the DatabaseTool **before** you downgrade the installed Duplicati version.
{% endhint %}

## Downgrade

The most common command to use is the `downgrade` command. Running the tool will downgrade the database to the version used in the most-recent stable release version. Simply invoke the tool from the commandline:

```
duplicati-database-tool downgrade
```

This will scan the storage folders for databases that should be downgraded and downgrade them all. If you need to specify the folder to look for databases in, this can be done with:

```
duplicati-database-tool downgrade --server-datafolder=/path/to/folder
```

It is also possible to provide the path to one or more databases as arguments, and the tool will then only work on those database. For more fine-grained control, use the `--server-version` and `--local-version` to specify the versions that the databases will be downgraded to.

## Upgrade

The `upgrade` command works exactly like the `downgrade` command, just working in the opposite direction. Usually it is not required to use the `upgrade` command, because Duplicati will auto-upgrade the databases on first use. Should you want to prepare for it anyway, you can invoke the command:

```
duplicati-database-tool upgrade
```

The `upgrade` command supports the same options as the `downgrade` command.

## List and Execute

{% hint style="info" %}
Note: For most serious database queries, a tool such as SQLiteBrowser is superior to the `list` and `execute` commands.
{% endhint %}

The `list` command is mostly used for debug purposes, and can be used to list the contents of one or more tables. You can invoke the tool with the name of a table to see the contents of that table:

```
duplicati-database-tool list /path/to/database.sqlite settings
```

To execute an SQL query, use:

```
duplicati-database-tool execute /path/to/database.sqlite "SELECT * FROM Settings"
```

If the query is stored in a file, it is also possible to point to the file:

```
duplicati-database-tool execute /path/to/database.sqlite /path/to/script.sql
```

## Verify and Cleanup

The `verify` command lists the database files known to Duplicati and reports the status of each as `Found`, `Missing`, or `Orphaned`. A database is orphaned if the file exists in the data folder, but is not referenced by the [server database](../../detailed-descriptions/database-and-storage/the-server-database.md) or by the `dbconfig.json` file used by the commandline:

```
duplicati-database-tool verify
```

Use `--datafolder` to examine a different data folder, and `--output-json` to get the result as JSON.

The `cleanup` command deletes the orphaned database files. Use `--dry-run` to see what would be deleted, and `--force` to skip the confirmation prompt:

```
duplicati-database-tool cleanup --dry-run
```

{% hint style="danger" %}
Always run `cleanup` with `--dry-run` first and check the list. In Duplicati 2.4.0.1 the server database `Duplicati-server.sqlite` is itself reported as orphaned and would be deleted. Do not run `cleanup` without `--dry-run` if the list contains `Duplicati-server.sqlite`.
{% endhint %}

## Wipe encryption

If the [server database](../../detailed-descriptions/database-and-storage/the-server-database.md) is encrypted and the encryption key is lost, Duplicati cannot start with it. The `wipe-encryption` command removes or clears all encrypted values from the server database so it can be opened without the key:

```
duplicati-database-tool wipe-encryption
```

A backup of the database is created first, unless `--no-backups` is given, and `--dry-run` shows what would be changed. After wiping, the passphrases, credentials, and other secrets that were encrypted must be entered again by editing the affected backups and settings. Only the server database is changed; local databases are skipped.
