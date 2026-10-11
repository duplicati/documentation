---
description: This page describes how to downgrade from Duplicati 2.4 to 2.3
---

# Downgrade from 2.4 to 2.3

Duplicati 2.4 updated the [server database](../../detailed-descriptions/database-and-storage/the-server-database.md) to version 12. Duplicati 2.3 expects server version 11 and will not start with a database that has a newer version. The [local databases](../../detailed-descriptions/database-and-storage/the-local-database.md) are version 19 in both releases. See [Database versions](../../technical-details/database-versions.md) for an overview.

{% hint style="info" %}
Note: make sure you run the DatabaseTool from the 2.4 installation **before** you downgrade Duplicati. The older DatabaseTool does not know how to downgrade the newer databases.
{% endhint %}

## Remove sync jobs

Version 12 of the server database records whether a job is a backup or a sync job. Duplicati 2.3 does not support sync jobs, and the downgrade removes this information, so any sync job would afterwards be treated as a regular backup. Delete any sync jobs before downgrading.

## Downgrade the databases

Make sure Duplicati is not running, and then use the [DatabaseTool](../../duplicati-programs/command-line-interface-cli-1/databasetool.md) to downgrade the server database:

```
duplicati-database-tool downgrade --server-version=11 --local-version=19
```

The `--local-version=19` part leaves local databases from 2.4 unchanged, but also downgrades local databases that were upgraded by a newer canary build.

After the downgrade is complete, uninstall Duplicati 2.4 and install 2.3.

## Obtaining older releases

The [installer packages for 2.3.0.4](https://github.com/duplicati/duplicati/releases/tag/v2.3.0.4_stable_2026-07-09) are available on Github. You can [browse the list of releases](https://github.com/duplicati/duplicati/releases) for other versions you may want.
