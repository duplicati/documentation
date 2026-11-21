---
description: >-
  This page describes how to set up a fully managed PostgreSQL backup that runs
  in the Duplicati Console
---

# Managed PostgreSQL backup

A managed PostgreSQL backup is a backup of a hosted PostgreSQL database that the Duplicati Console runs for you. Nothing is installed in your hosting platform: you give the console a connection to the database with a read-only role, and the console pulls a dump of the database on a schedule, stores it and reports the results. It works with Supabase, Neon, Railway, Render and any other PostgreSQL server that can be reached from the internet.

The managed backup uses the same backup engine as the self-hosted [PostgreSQL backup and restore](../detailed-descriptions/automation-and-integration/postgresql-backup-and-restore.md), so the way the database is stored, the supported PostgreSQL versions (14 to 18) and the restore behavior described on that page also apply here.

{% hint style="info" %}
Managed PostgreSQL backup is in preview. Contact Duplicati support to have it enabled for your organization.
{% endhint %}

{% hint style="warning" %}
Managed backups are not available for organizations that have [confidential processing](confidential-processing.md) enabled. See [Managed backups](managed-backups.md) for an overview of how managed backups are run.
{% endhint %}

## How it works

Each managed backup is executed by a **runner**, which is a Duplicati instance hosted by Duplicati. The runner is started when there is work to do, and stopped again when the work has completed:

1. When the backup is due, or when you click **Run now**, the console starts a runner for the backup.
2. The runner measures the size of the database and checks it against the managed database GB assigned to the backup.
3. The runner runs `pg_dump` against the database and reads the dump as a stream, split into one entry per table. Nothing is written to disk on the way, so the size of the database is not limited by the runner.
4. The data is deduplicated, compressed and encrypted, and then uploaded to the storage destination.
5. The runner reports the result to the console and is shut down.

The connection to the database is always encrypted with TLS. The runner has the PostgreSQL client programs for every supported version, and uses the ones that match the server.

Each managed backup shows up in the [machine list](the-machine-list.md) as a machine named after the backup, followed by "Runner", where the backup reports and activity are collected.

## Managed database GB

Each database backup is assigned a number of **managed database GB**, which is the largest size the database may have for the backup to run. Managed database GB are separate from [managed backup users](managed-backups.md#managed-backup-users), and one cannot be used in place of the other.

### How the size is measured

Before each backup, the runner reads the size of the database as the server reports it (`pg_database_size`), and rounds it up to whole GB. This is the same number your hosting platform shows for the database. It includes indexes and space that has not been reclaimed yet, which are not part of the dump, so it is usually larger than the backup itself. The whole database is measured, even if [filters](managed-postgresql-backup.md#leave-out-tables) leave out some of its tables.

{% hint style="warning" %}
If the database is larger than the GB assigned to the backup, the backup **fails** before anything is read from the database, and the backup report says how large the database is. Assign more GB to the backup to make it run again.
{% endhint %}

The [Managed backups tab](managed-backups.md#the-managed-backups-tab) shows the size the latest backup measured next to the GB assigned to each database backup, such as **4 / 10 GB**. A warning icon is shown when the database is close to, or larger than, its assignment.

### Storage

Managed database GB do **not** include storage. A backup to [Duplicati Storage](duplicati-storage.md) uses the storage of the organization, and a backup to your own storage uses no Duplicati Storage at all. How much storage a backup uses depends on how much of the database changes between backups: each table is stored on its own and in small blocks, so the tables and rows that did not change are not stored again.

## Prepare the database

The backup should connect with a role that can read the whole database and nothing else, rather than as the owner of the database. Connect to the database with an administrative role and create a role for the backup:

```sql
create role duplicati_backup login password '...' in role pg_read_all_data;
```

If any table uses row-level security, the role must also bypass it, or the backup fails:

```sql
alter role duplicati_backup bypassrls;
```

The wizard shows the statements and the connection string to use for each hosting platform, see below. The [PostgreSQL backup and restore](../detailed-descriptions/automation-and-integration/postgresql-backup-and-restore.md#a-role-for-the-backup) page has more details on the role.

The database server must accept connections from the internet, as the runner connects from Duplicati's cloud environment.

## Create a managed backup

In the Duplicati Console, go to **Machines** and choose the **Managed backups** tab. Click **Managed backup** and choose **PostgreSQL**.

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-source-menu-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-source-menu-light.png" alt="Choosing PostgreSQL as the source of a new managed backup"></picture><figcaption></figcaption></figure>

### General

On the first page, fill in the general settings:

* **Backup name**: A name for the backup, such as the name of the database.
* **Encryption key**: Generate the key automatically, or enter one yourself.
* **Managed database GB**: The number of GB to assign to this backup. The field shows how many GB are available. Assign at least the size of the database, with room for it to grow.
* **Run the backup**: How often the backup runs and at what time.
* **Duplicati storage**: Leave this on to store the backup in [Duplicati Storage](duplicati-storage.md), or switch it off to choose your own storage destination, as described in [Using your own storage](managed-google-workspace-backup.md#using-your-own-storage).

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-general-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-general-light.png" alt="The General step of the managed backup wizard for a PostgreSQL database"></picture><figcaption></figcaption></figure>

### Source

On the source page, enter the connection to the database. You can paste the connection string that your hosting platform shows, such as `postgresql://user:password@host:5432/database`, and the fields below are filled in from it. Changing a field also updates the connection string.

* **Host** and **Port**: The address of the database server.
* **Database**: The name of the database. A backup covers one database; create a backup for each database you want to back up.
* **Username** and **Password**: The role the backup connects as.
* **TLS mode**: `require` encrypts the connection. `verify-ca` and `verify-full` also check the server certificate against a root certificate, which you enter under **Advanced options**. Modes that do not always encrypt the connection are not accepted.

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-source-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-source-light.png" alt="The Source step with the connection and the setup instructions for Supabase"></picture><figcaption></figcaption></figure>

Under **Advanced options**, you can also include the large objects of the database. They are left out by default, as one large object that the role cannot read makes the whole backup fail. See [Large objects](../detailed-descriptions/automation-and-integration/postgresql-backup-and-restore.md#large-objects) for details.

**How to set up the database** shows the connection string to copy and the statements that create the backup role, for the platform the database is hosted on. The platform is picked from the host name, and you can choose another one:

| Platform | Connection string                                                   | Notes                                                                         |
| -------- | ------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Supabase | The session pooler connection string, which uses port 5432          | Supabase uses row-level security, so the role must always have `bypassrls`    |
| Neon     | The connection string of the unpooled host                          | A suspended compute is woken by the backup                                    |
| Railway  | The `DATABASE_PUBLIC_URL` variable of the Postgres service          | Railway bills egress per GB, and every backup transfers the whole dump        |
| Render   | The external database URL                                           | Render does not allow `pg_read_all_data`, so read access is granted per schema |
| Other    | Any connection string, or fill in the fields                        | The server must be reachable from the internet and accept TLS connections     |

Click **Create backup** to save the backup. It now runs according to the schedule, and you can click **Run now** in the list to start the first backup right away.

{% hint style="info" %}
Every PostgreSQL backup uses a block size of 64 KB, which keeps the stored size of a changing database small. It is set when the backup is created and cannot be changed.
{% endhint %}

## Test the connection and measure the database

After the backup has been created, click **Edit** on the backup and go to the **Source** page. Two buttons start a runner and check the database as it was saved:

* **Test connection** checks the connection, the TLS encryption, the server version, whether the role can read every table and bypasses row-level security where needed, the large objects, and whether the role is a superuser. A check that the backup does not depend on, such as large objects that are left out, is shown as a warning.
* **Measure database** shows the number of tables, the size of the database and the managed database GB the backup needs.

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-test-connection-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-test-connection-light.png" alt="The result of Test connection for a PostgreSQL database"></picture><figcaption></figcaption></figure>

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-measure-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-measure-light.png" alt="The result of Measure database"></picture><figcaption></figcaption></figure>

If you change the connection, save the backup before testing it, as the runner tests the source as it was saved.

## Leave out tables

By default, every table in the database is backed up. To leave out tables, click **Edit** on the backup and go to the **Filters** page. The console starts a runner that lists the tables of the database, without starting a dump. Click a table to leave out its rows, and click it again to include them. The resulting rules are shown under **Filter rules**, where you can also add rules manually, such as `-/backup/tables/logs.*` to leave out a whole schema.

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-filters-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-filters-light.png" alt="The Filters step with the audit_log table left out"></picture><figcaption></figcaption></figure>

The rows of a table that is left out are never read from the database. The table is still created by a restore of the whole database, with its indexes and constraints, but without rows. Leaving out tables does not change the measured size of the database.

## Monitoring and maintenance

The list of managed backups shows each database backup with the size the latest backup measured and the GB assigned to it. In this example, the second database has reached the 5 GB assigned to it, and its next backup fails if it grows further:

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-list-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-list-light.png" alt="The Managed backups tab with a Microsoft 365 backup and two PostgreSQL backups"></picture><figcaption></figcaption></figure>

The results of each backup are reported to the console in the same way as for regular machines, and can be found in the backup reports for the runner machine. If the connection to the database is lost during a backup, the backup completes with a warning that names the tables that were affected, and those tables keep their copy from the previous backup. See [When a backup fails](../detailed-descriptions/automation-and-integration/postgresql-backup-and-restore.md#when-a-backup-fails) for details.

The actions menu on each backup has the same maintenance operations as other managed backups, described in [Managed backups](managed-backups.md#the-managed-backups-tab). eDiscovery is not available for database backups. Archiving a database backup releases the GB assigned to it.

## Restore

To restore, open the actions menu for the backup in the list of managed backups and choose **Restore**. The restore runs in three steps.

### What to restore

The console starts a runner to browse the backup. Choose the **version** to restore from, and then select what to restore:

* Select **PostgreSQL** at the top to restore the **whole database**: its tables, views, functions, sequences and other objects, the rows, and the indexes and constraints.
* Expand **tables** and select one or more tables to load only the **rows of those tables** into tables that exist.

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-restore-select-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-restore-select-light.png" alt="Selecting two tables to restore"></picture><figcaption></figcaption></figure>

### Where to restore to

Next, choose the database to restore into:

* **The database the backup was taken from**: Restores with the backup's own connection and role. As the backup role is usually only allowed to read, this restore normally fails, so test the connection first.
* **Another database**: Enter the connection to another database, or to the same database with a role that can write, and click **Use this database**. The connection string, fields and TLS modes are the same as for the backup.

Click **Test connection** to check the target before restoring. For a restore, it checks the connection, the TLS encryption, the server version, whether the role can create objects, and whether the database already has tables.

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-restore-where-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-restore-where-light.png" alt="Restoring the whole database into another database"></picture><figcaption></figcaption></figure>

<figure><picture><source srcset="../.gitbook/assets/console-managed-postgresql-restore-check-dark.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/console-managed-postgresql-restore-check-light.png" alt="The connection check of a restore target"></picture><figcaption></figcaption></figure>

When **restoring the whole database**:

* The target must be empty, or the restore stops before changing anything. Check **Replace what exists in the database** to drop and recreate the objects of the backup. This happens in one transaction, so the database keeps what it had if creating the objects fails.
* Owners and grants are not restored: everything is owned by the role that restores.
* A version from a backup that failed part-way holds some tables from an earlier backup. Check **Restore this version even if a backup failed part-way** to restore it anyway. Without it, the rows are loaded, but the indexes and constraints are not created and the restore is reported as failed.

When **restoring tables**:

* A table must be empty, or it is not restored. Check **Replace the rows that exist** to empty the table first.
* Check **Create tables that are missing** to create a selected table that does not exist in the target. Only the table is created, without its indexes and constraints.
* Indexes, constraints and other objects are not changed.

### Progress

Click **Start restore** to begin. The console starts a runner for the restore and shows the progress. The rows are streamed from the backup directly into the database, and you can leave the page while the restore runs.

### Restore without the console

A managed PostgreSQL backup is a normal Duplicati backup, made with the PostgreSQL support of the open-source Duplicati client. If you have the encryption passphrase and access to the storage, you can restore it into your own PostgreSQL server, or as plain files, with the Duplicati client and no console. See [Restore](../detailed-descriptions/automation-and-integration/postgresql-backup-and-restore.md#restore) on the PostgreSQL backup and restore page.
