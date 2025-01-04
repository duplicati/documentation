---
description: This page describes how to downgrade from Duplicati 2.1.0.2 to 2.0.8.1
---

# Downgrade from 2.1.0.2 to 2.0.8.1

To downgrade from 2.1.0.2 to an earlier version, note that the two are built on different core technologies (.NET8 vs .NET4/Mono). If you have previously been able to run 2.0.8.1, you should be able to downgrade by installing the previous version as before.

Before you downgrade, you should make sure you have removed database encryption. You can do this by stopping all running instances, and then running [Server](../../duplicati-programs/server.md) or [TrayIcon](../../duplicati-programs/trayicon.md) with:

```
duplicati-server --disable-db-encryption=true
```

This will remove the field-level encryption in the [server database](../../detailed-descriptions/the-server-database.md). After starting with this parameter, stop the server, uninstall 2.1.0.2 and install 2.0.8.1.

Since both the [server database](../../detailed-descriptions/the-server-database.md) and the [local database](../../detailed-descriptions/the-local-database.md) was updated, you need to downgrade both. Note that there is one local database for each backup you have configured, and all of those may need to be downgraded.

To downgrade the server database, use an SQLite tool, such as [SQLite Browser](https://sqlitebrowser.org/). Open the database and run the following query:

```sql
DROP TABLE "TokenFamily";
UPDATE "Version" SET "Version" = 6;
```

This will downgrade the server database to [version 6](../../technical-details/database-versions.md#server-database), and allow it to properly upgrade later if needed.

For each of the local databases, run the following:

```sql
DROP INDEX "UniqueBlockVolumeDuplicateBlock";
UPDATE "Version" SET "Version" = 12;
```

This will downgrade the database to [version 12](../../technical-details/database-versions.md#local-database), and allow it to upgrade later if needed.

Close the SQLite editor, and then start Duplicati 2.0.8.1.

## Obtaining older releases

The [installer packages for 2.0.8.1](https://github.com/duplicati/duplicati/releases/tag/v2.0.8.1-2.0.8.1_beta_2024-05-07) are available on Github. You can [browse the list of releases ](https://github.com/duplicati/duplicati/releases)for other versions you may want.

