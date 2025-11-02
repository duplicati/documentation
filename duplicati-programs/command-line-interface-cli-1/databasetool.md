---
description: >-
  This page describes the DatabaseTool command line tool for changing the
  database versions.
---

# DatabaseTool

The DatabaseTool is intended to assist with changing the databases used by Duplicati. Duplicati has a single [settings database for the server](../../detailed-descriptions/the-server-database.md) and a [local database for each backup job](../../detailed-descriptions/the-local-database.md), and the DatabaseTool can manage both.

The DatabaseTool is called `Duplicati.CommandLine.DatabaseTool.exe` on Windows and `duplicati-database-tool` on Linux and MacOS. The tool supports 4 operations: `downgrade`, `upgrade`, `list` and `execute`.

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
