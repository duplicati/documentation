---
description: This page describes how to restore files using the Duplicati user interface
---

# Restoring files

The most important reason to make a backup is the ability to recover the data at a later stage, usually due to some unforseen incident. Depending on the incident, the original configuration may not be available.

To start a restore process in Duplicati, start on the "Restore" page.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-11-03 at 13.39.31.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-11-03 at 13.39.41.png" alt=""></picture><figcaption><p>Choosing a restore approach</p></figcaption></figure>

If the backup configuration is already existing on the machine, you can choose it from the list. In this case you can click "Restore" and skip to the section on [choosing the files to restore](restoring-files.md#choosing-files-to-restore).&#x20;

The restore and browsing process are fastest when using a configured backup, because Duplicati can query a local database with information. If the local database is not present, Duplicati needs to fetch enough information from the remote storage to build a partial database when performing the restore.

If you have exported the backup configuration and have the configuration available, you can click the "Start" button on "Restore from configuration" and skip to the [restore from configuration section](restoring-files.md#restore-from-configuration). You can also read up on how to [import and export configurations](../detailed-descriptions/import-and-export-backup-configurations.md).

## Direct restore from backup files

To restore files from the backup, Duplicati needs only to know how to access the files and the encryption passphrase (if any). If you do not have the passphrase, it is not possible to restore.&#x20;

{% hint style="info" %}
This option is useful restoring from another machine than the one that made the backup originally, but is not recommended for regular operations due to the need to download and process more data.
{% endhint %}

To restore directly from the backup files, the first step is to provide the destination details. These details are the same as you supplied initially when creating the backup. if you are using a cloud provider, you can usually get the needed information via your account on the vendors website. You can see a list of the [destinations that are supported by Duplicati](../backup-destinations/destination-overview.md).

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-11-03 at 13.44.55.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-11-03 at 13.44.45.png" alt=""></picture><figcaption><p>Supply storage destination details</p></figcaption></figure>

Once the details are entered, it is recommended to use the "Test connection" button to ensure that the connection is working correctly. Then click the "Continue" button.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-11-03 at 13.49.24.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-11-03 at 13.49.31.png" alt=""></picture><figcaption><p>Supply the encryption passphrase</p></figcaption></figure>

If the backup is not encrypted, leave the field empty. When ready, click "Continue" and Duplicati will examine the remote destination and figure out what backups are present. After working through the information, you can [choose files to restore](restoring-files.md#choosing-files-to-restore).

## Restore from configuration

If you have a configuration file you can use the information in that file to avoid entering it manually. If you need to restore more than once, it may be faster to [import the configuration](../detailed-descriptions/import-and-export-backup-configurations.md) and [rebuild the local database](../detailed-descriptions/the-local-database.md). After the database is built, you can choose the configuration from the list and skip to [choosing files to restore](restoring-files.md#choosing-files-to-restore).

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-11-03 at 13.51.29.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-11-03 at 13.51.19.png" alt=""></picture><figcaption><p>Restoring with a backup configuration</p></figcaption></figure>

In the dialog, provide the exported configuration file. If the file is encrypted, you will be asked to enter the file passphrase.&#x20;

{% hint style="info" %}
The passphrase the configuration file is encrypted with is not neccesarily the same as the passphrase used to encrypt the backup with.
{% endhint %}

Once the configuration is correct, click the "Restore" button and you will be ready to choose files to restore.

## Choosing files to restore

Once Duplicati has a connection to the remote destination it will find all the backups that were made. It will then choose the most recent version and list the files from within that version. Use the dropdown to select the version to restore from, and then choose the files that you want to restore.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-11-03 at 13.52.05.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-11-03 at 13.52.13.png" alt=""></picture><figcaption><p>Choosing files to restore</p></figcaption></figure>

Click the files or folders that you want to restore and then click "Continue".

## Choosing restore options

When restoring there are a few options that control how the files are restored.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-11-03 at 13.53.03.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-11-03 at 13.52.51.png" alt=""></picture><figcaption><p>Choosing restore options</p></figcaption></figure>

If you want to restore a file to a previous state, you can leave the settings to their defaults. If you are unsure if you want to revert, or need to examine the files before replacing the current versions, you can choose to restore to another destination. If the folder you are restoring to is not empty, you can choose to store multiple versions of the files by appending the restore timestamp to the filename. This is especially useful if you are restoring multiple versions into a target folder for comparison.

Duplicati will not restore permissions by default because the users and groups that were present on the machine that made the backup may not be present on the machine being restored to. Restoring the permissions can cause you to be unable to access the restored files, if your user does not have the necessary permissions.

When satisfied with the settings, click the "Submit" button and the restore process will restore the files.

Once the restore has completed, you will see the restore summary page:

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-11-03 at 13.53.52.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-11-03 at 13.54.02.png" alt=""></picture><figcaption></figcaption></figure>
