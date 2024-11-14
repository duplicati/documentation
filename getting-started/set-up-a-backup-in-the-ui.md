---
description: Describes how to configure a backup in Duplicati
---

# Set up a backup in the UI

Once Duplicati is running, you can set up a backup through the UI. If the UI is not showing, you can use the [TrayIcon](../duplicati-programs/trayicon.md) in your system menu bar and choose "Open". If you are asked for a password before logging in to the UI, see [how to access without a password](../detailed-descriptions/duplicati-access-password.md).

In the UI, start by clicking "Add backup", and choose the option "Configure a new backup":

<figure><img src="../.gitbook/assets/Screenshot 2024-11-11 at 09.27.52.png" alt=""><figcaption><p>Configuring a new backup</p></figcaption></figure>

If you have an existing backup configuration you want to load in, see the [section on import/export](../detailed-descriptions/import-and-export-backup-configurations.md).

To set up a new backup there are some details that are required, and these are divided into 5 steps:

1. [Basic configuration](set-up-a-backup-in-the-ui.md#basic-configuration) (descriptive name, passphrase)
2. [Storage destination](set-up-a-backup-in-the-ui.md#storage-destination) (where to store the backups)
3. [Source data](set-up-a-backup-in-the-ui.md#source-data) (what data should be backed up)
4. [Schedule](set-up-a-backup-in-the-ui.md#schedule) (automatically run backups)
5. [Retention and miscelaneous](set-up-a-backup-in-the-ui.md#retention-and-miscelaneous) (when to delete old backups and more)

## 1. Basic configuration

For the basic configuration, you need to provide a name and setup encryption:

<figure><img src="../.gitbook/assets/Screenshot 2024-11-11 at 09.28.15.png" alt=""><figcaption><p>Basic configuration page</p></figcaption></figure>

The name and description fields can be any text you like, and is only used to display the backup configuration in lists so you can differentiate if you have multiple backups.

The encryption setup allows you to choose an encryption method and a passphrase. Encryption generally adds a minor overhead to the processing, but is generally a good idea to add. If you opt out of encryption, make sure you control the storage destination and have adequate protections in place.

To avoid weak passphrases, Duplicati has a built-in passphrase generator as well as a passphrase strength measurer. **Be sure to store the chosen or generated passphrase in a safe location** as it is not possible to recover anything if this passphrase is lost!

## 2. Storage destination

The storage destination is arguably the most technical step because it is where you specify how to connect to the storage provider you want to hold your information. Some destinations require only a single setting, where others require multiple.

Due to the number of supported backends, this page does not contain the instructions. Instead, each of the supported destinations is described in detail on the [destination overview page](../backup-destinations/destination-overview/).

<figure><img src="../.gitbook/assets/Screenshot 2024-11-12 at 14.34.55.png" alt=""><figcaption><p>Storage destination list</p></figcaption></figure>

When the details are entered, it is recommended that you use the "Test" button which will perform some connection tests that helps reveal any issues with the entered information.

When the destination is configured as desired, click the "Next" button.

## 3. Source data

In the third step you need to define what data should be backed up. This part depends on your use. If you are a home user, you may want to back up images and documents. An IT professional may want to back up databases.

In the source picker view you can choose the files and folders you would like to back up. If you pick a folder, all subfolders and files in that folder will be included. You can use the UI to uncheck some items that you want to exclude, and they will show up with a red X.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-13 at 18.07.46.png" alt=""><figcaption><p>Selecting source folders</p></figcaption></figure>

For more advanced uses, you can also use the filters to set up rules for what to include and exclude. See the section on [how filters are evaluated in Duplicati](../detailed-descriptions/filters-in-duplicati.md) if your have advanced needs.

Once you are satisfied with the source view, click the "Next" button to continue to the schedule step.

## 4. Schedule

Having an outdated backup is rarely an ideal solution, but remembering to run backups is also tedious and easy to forget. To ensure you have up-to-date backups, there is a built-in scheduler in Duplicati that you can enable to have Duplicati run automatically.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-12 at 14.43.07.png" alt=""><figcaption><p>Choosing a schedule to run on</p></figcaption></figure>

If you prefer to run the backups manually, disable the scheduler, and you can use [ServerUtil](../duplicati-programs/command-line-interface-cli/serverutil.md) to trigger the backups as needed.

Once satisfied with the schedule, click "Next".

## 5. Retention and miscelaneous

Even though Duplicati has deduplication and compression to reduce the stored data, it is inevitable that old data is stored that will take up space, but is not needed for restore. In this final configuration step you can decide when old versions are removed and what size of files to store on the destination.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-12 at 14.44.06.png" alt=""><figcaption><p>Choosing backup retention</p></figcaption></figure>

The size of remote volumes is meant for a balanced size usable with cloud-storage and a limited network connection. If you have a fast connection or store files on a local network, consider increasing the size of the remote volumes. For more information see [this page on the tradeoffs between sizes](https://forum.duplicati.com/t/choosing-sizes-in-duplicati/17683).

For the retention setting, it is inveitable that the backups will grow as new and changed data is added to the backups. If nothing is ever deleted, the backup size will keep growing in size. With the retention settings you can choose how to automatically remove older versions.

The setting "Smart backup retention" is meant to be useful for most users where it keeps one daily backup and then gradually fewer versions going back in time.

Once you are satisfied with the settings, click the "Save" button.

You have now configured your backup! 🎉
