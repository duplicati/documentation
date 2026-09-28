---
description: >-
  This page describes how to set up a fully managed Google Workspace backup
  that runs in the Duplicati Console
---

# Managed Google Workspace backup

A managed backup is a Google Workspace backup that is run for you by the Duplicati Console. There is no software to install and no machine to maintain: you provide the credentials for the Google Workspace domain, and the console takes care of running the backup on a schedule, storing the data and reporting the results.

The managed backup uses the same backup engine as the self-hosted [Google Workspace backup and restore](../detailed-descriptions/automation-and-integration/google-workspace-backup-and-restore.md), so the supported data types, data formats and limitations described on that page also apply here.

{% hint style="info" %}
Managed backups require **managed backup users** on your subscription. They are purchased separately from machine licenses and do not require an Enterprise plan. Trials do not include managed backup users.
{% endhint %}

{% hint style="warning" %}
Managed backups are not available for organizations that have **confidential processing** enabled.
{% endhint %}

## How it works

Each managed backup is executed by a **runner**, which is a Duplicati instance hosted by Duplicati. The runner is not running all the time. It is started on demand each time there is work to do, and stopped again when the work has completed:

1. When the backup is due, or when you click **Run now**, the console starts a runner for the backup.
2. The runner receives the backup configuration, connects to your Google Workspace domain through the Google APIs and reads the data.
3. The data is deduplicated, compressed and encrypted, and then uploaded to the storage destination.
4. The runner reports the result to the console and is shut down.

Runners are also started on demand when you browse the domain to set up filters, test the permissions or restore data. Starting a runner usually takes a minute or two.

Each managed backup shows up in the machine list as a machine named after the backup, followed by "Runner". This machine does not use a machine license, and is where the backup reports and activity for the managed backup are collected, so the existing dashboards, reports and alerts work for managed backups as well.

The backup data is encrypted with a passphrase that is generated automatically when the backup is created. The encryption settings cannot be changed afterwards.

## Managed backup users and storage

Managed backups are licensed per **managed backup user**, also referred to as a seat. Seats are purchased on the subscription page under **Settings → Subscription**, and the same seats can be used for both Google Workspace and Microsoft 365 backups.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.53.00.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.52.50.png" alt="The Managed Backup Users card on the subscription page"></picture><figcaption></figcaption></figure>

{% hint style="info" %}
Managed backup users are sold on yearly subscriptions. If you are on a monthly subscription, contact Duplicati support to add managed backup users.
{% endhint %}

### Storage

Each purchased managed backup user adds **100 GB** of Duplicati cloud storage to the account. The storage is pooled for the organization, so it does not matter how the data is distributed between the users in the domain. As an example, 25 managed backup users give 2.5 TB of storage that is shared by all backups in the organization.

If the storage quota is exceeded, the console raises a **Storage quota exceeded** alert, and new backups may fail until the usage is brought below the quota again or more storage is added.

### How seats are counted

When you create a managed backup, you assign a number of your managed backup users to it. A backup with a given number of seats can back up that many users, that many groups, that many shared drives and that many sites. The number of seats a backup uses is therefore the largest of the four counts, not the sum.

For Google Workspace, only **active users** count towards the seats. Suspended and archived users are included in the backup, but do not use a seat.

After each backup, the console shows how many seats the latest backup used, broken down into users, groups, sites and shared drives.

{% hint style="warning" %}
If the domain contains more users, groups, shared drives or sites than there are seats assigned to the backup, the items beyond the limit are **not backed up** and the backup log contains a warning. Make sure to assign enough seats, or use filters to leave out the parts of the domain that you do not want to back up. Restores are not limited by seats.
{% endhint %}

## Prepare the Google Workspace domain

The managed backup accesses the domain with a **service account** that uses **domain-wide delegation** to act on behalf of the users in the domain. Before creating the backup, a Google Workspace super administrator needs to set up the service account:

1. In the [Google Cloud console](https://console.cloud.google.com), create a project, or choose an existing one.
2. Under **APIs & Services**, enable the APIs for the data you want to back up: Admin SDK, Gmail, Google Drive, Google Calendar, People, Tasks, Google Keep, Google Chat and Groups Settings.
3. Under **IAM & Admin → Service Accounts**, create a service account, such as "duplicati-backup".
4. On the service account, create a new **key** of type JSON and download the file.
5. Note the **client ID** (unique ID) of the service account.
6. In the [Google Admin console](https://admin.google.com), go to **Security → Access and data control → API controls → Manage domain-wide delegation**.
7. Click **Add new**, enter the client ID of the service account and the list of scopes that are required. The full list is in the [API permissions reference](../detailed-descriptions/automation-and-integration/google-workspace-backup-and-restore.md#api-permissions-reference).

{% hint style="info" %}
The backup only needs the read-only scopes, while a restore needs the scopes with write access. For better security, you can grant only the backup scopes to the service account used for the backup, and supply a different service account with the restore scopes when you need to restore.
{% endhint %}

{% hint style="info" %}
Reading the access control lists for calendars requires the full `calendar` scope. If you prefer to grant only read-only scopes, add the advanced option `google-ignore-calendar-acl` to the source.
{% endhint %}

## Create a managed backup

In the Duplicati Console, go to **Machines** and choose the **Managed backups** tab. Click the **Managed backup** button to start the wizard.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.49.27.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.49.09.png" alt="The Managed backups tab on the Machines page"></picture><figcaption></figcaption></figure>

### General

On the first page, fill in the general settings:

* **Backup name**: A name for the backup, such as the name of the domain.
* **Managed backup users**: The number of seats to assign to this backup. The field shows how many seats are available.
* **Run the backup**: How often the backup runs. The options are **Daily**, **Every 2 days**, **Weekly** and **Monthly**.
* **Use own storage**: Leave this unchecked to use the Duplicati cloud storage. See [Using your own storage](managed-google-workspace-backup.md#using-your-own-storage) if you prefer to store the backups elsewhere.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.53.50.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.53.58.png" alt="The General step of the managed backup wizard"></picture><figcaption></figcaption></figure>

Scheduled backups are started around noon in your local time zone. It can take up to an hour from the scheduled time until the backup starts.

### Source

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.54.31.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.54.24.png" alt="Choosing the type of source to back up"></picture><figcaption></figcaption></figure>

On the source page, choose **Google Workspace** and enter the credentials:

* **Admin Email**: The email address of a Google Workspace administrator. The service account uses this account to list the users, groups and other resources in the domain.
* **Service account JSON**: The contents of the JSON key file that was downloaded for the service account.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.55.01.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.55.08.png" alt="The Source step with the Google Workspace credentials"></picture><figcaption></figcaption></figure>

The advanced options on the source can be used to control which types of data are included, and to exclude suspended or archived users. The options are described in [Google Workspace configuration options](../detailed-descriptions/automation-and-integration/google-workspace-backup-and-restore.md#google-workspace-configuration-options).

Click **Create backup** to save the backup. It now runs according to the schedule, and you can click **Run now** in the list to start the first backup right away.

{% hint style="info" %}
The source type cannot be changed after the backup is created. To back up a Microsoft 365 tenant, create a separate [managed Microsoft 365 backup](managed-microsoft-365-backup.md).
{% endhint %}

## Test permissions and leave out data

By default, everything in the domain is backed up. After the backup has been created, click **Edit** on the backup to get access to the **Filters** page. The console starts a runner that connects to the domain, and shows the contents of the domain as a tree.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.56.55.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.56.49.png" alt="The Filters step showing the contents of the domain"></picture><figcaption></figcaption></figure>

From this page you can:

* **Leave out items**: Click an item in the tree, such as a user or a shared drive, to leave it out of the backup. Click it again to include it. The resulting rules are shown under **Filter rules**, where you can also add rules manually.
* **Count items**: Shows the number of users, groups, shared drives and sites in the domain, with the users split into active, suspended and archived. This is useful for deciding how many seats to assign to the backup.
* **Test permissions**: Shows which of the scopes required for backup and restore are granted to the service account, and which are missing.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.57.15.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.57.23.png" alt="The Google Workspace items dialog" width="563"></picture><figcaption></figcaption></figure>

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.57.05.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.57.30.png" alt="The Google Workspace permissions dialog" width="563"></picture><figcaption></figcaption></figure>

## Using your own storage

By default, managed backups are stored in the Duplicati cloud storage, which requires no configuration. If you prefer to keep the backup data in your own storage, check **Use own storage** on the first page of the wizard. This adds a **Destination** page to the wizard where you choose and configure the storage destination.

All the [backup destinations supported by Duplicati](../backup-destinations/destination-overview.md) can be used, with the exception of the local file destination, as the runner has no persistent local storage. The destination must be reachable from the internet for the runner to connect to it.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 13.59.45.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 13.59.36.png" alt="The Destination step of the managed backup wizard"></picture><figcaption></figcaption></figure>

## Monitoring and maintenance

The list of managed backups shows the schedule for each backup and the current state, such as **Backing up** or **Restoring**, while a runner is active. Only one operation can run at a time for each backup.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 14.03.05.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 14.02.58.png" alt="The list of managed backups with a restore in progress"></picture><figcaption></figcaption></figure>

The results of each backup are reported to the console in the same way as for regular machines, and can be found in the backup reports for the runner machine. See [Using the Duplicati Console](using-the-duplicati-console.md) for details on reports and alerts.

The actions menu on each backup contains the following operations:

| Action              | Description                                                                                         |
| ------------------- | --------------------------------------------------------------------------------------------------- |
| Restore             | Restores data from the backup, see below                                                            |
| Repair              | Repairs inconsistencies between the local database and the storage destination                      |
| Purge broken files  | Removes files from the backup that can no longer be restored due to missing data. Cannot be undone  |
| Recreate database   | Rebuilds the local database from the storage destination. No backups run until it has finished      |
| Delete database     | Deletes the local database. Backups fail until the database is recreated                            |
| Archive             | Removes the managed backup and releases the seats assigned to it                                    |

{% hint style="info" %}
Running a backup manually, restoring and the maintenance operations require that you are an owner of the organization.
{% endhint %}

## Restore

To restore data, open the actions menu for the backup in the list of managed backups and choose **Restore**. The restore runs in three steps.

### What to restore

The console starts a runner to browse the backup. Choose the **version** of the backup to restore from, and then select the items to restore in the tree. You can select anything from a full user down to a single folder or item, and you can search for users, sites or files by name.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 14.00.57.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 14.01.03.png" alt="Selecting what to restore"></picture><figcaption></figcaption></figure>

### Where to restore to

Next, choose the credentials for the restore and the location to restore into:

* **Use the backup's credentials**: Restores into the same domain that was backed up. If the service account was only granted the read-only scopes, the restore fails, so use **Test permissions** to check before starting.
* **Use other credentials**: Enter an admin email and a service account key to use for the restore. This can be a service account with write access to the same domain, or a service account for a **different domain** for a cross-tenant restore.

Then select the target under **Restore into**, such as a user, a Gmail label, a Drive folder or a shared drive. The data does not need to go back to where it came from, so data from one user can be restored into another user.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 14.01.50.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 14.01.43.png" alt="Selecting where to restore to"></picture><figcaption></figcaption></figure>

By default, the restore checks if each item already exists in the target to avoid creating duplicates. The check can be skipped to make the restore faster, but items that already exist are then restored again.

Some types of data, such as Google Groups and Google Sites, can be backed up but not restored. See [Backup and restore details by type](../detailed-descriptions/automation-and-integration/google-workspace-backup-and-restore.md#backup-and-restore-details-by-type) for details on how each type of data is restored.

### Progress

Click **Start restore** to begin. The console starts a runner for the restore and shows the progress. You can leave the page while the restore is running, the restore continues in the background.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-09-28 at 14.02.39.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-09-28 at 14.02.45.png" alt="The progress of a restore"></picture><figcaption></figcaption></figure>
