---
description: This page describes how to import and export configurations from Duplicati
---

# Import and export backup configurations

While it is not required that you keep a copy of the backup configuration, it can sometimes be convenient to have all settings related to a backup stored in a single file.

## Export

To export from within the user interface, expand the backup configuration and click "Export ..."

<div><figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.14.44.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.14.32 (1).png" alt=""></picture><figcaption><p>Expanded backup configuration</p></figcaption></figure> <figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.15.07.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.15.15.png" alt=""></picture><figcaption><p>Choosing export options</p></figcaption></figure></div>

On this page you should select "To File", which is default. The option to export "As commandline..." is not covered here, but allows you to get a string that can be used with the [Duplicati CLI executable](../../duplicati-programs/command-line-interface-cli.md).

You then need to decide on how to handle secrets stored in the configuration. Since these secrets include both the credentials to connect to the remote destination as well as the encryption passphrase, it is important that the exported file is protected.

You can choose to not include any secrets by unchecking the "Export passwords" option. The resulting file will then not contain the secrets and you need to store them in a different place (credential vault, keychain, etc).

You can also choose to encrypt the file before exporting it. If you choose this option, make sure you choose a strong unique passphrase, and store that passphrase in a safe location.

After completing the export, you will get a file containing the backup configuration. The file is in JSON format and optionally encrypted with [AESCrypt](../../using-tools/encrypting-and-decrypting-files.md).

## Import configuration

With an exported configuration, you can delete an existing configuration and re-create it by importing the configuration. You can optionally edit the parameters so the re-created backup configuration differs from the original.

To import a configuration, go to the "Add backup" page and choose "Import from file":

<div><figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 13.11.15 (1).png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 13.07.32 (1).png" alt=""></picture><figcaption><p>Adding a backup</p></figcaption></figure> <figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.16.05.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.15.55.png" alt=""></picture><figcaption><p>Choosing import options</p></figcaption></figure></div>

Pick the file or drag-n-drop it on the file chooser. If the file is encrypted, provide the file encryption passphrase here as well.

The option to "Import metadata" will create the new backup configuration and restore the statistics, including backup size, number of versions, etc. from the data in the file. If not checked, these will not be filled, and will be updated when the first backup is executed.

If the option "Save immediately" is checked, the backup will be created when clicking import, skipping the option to edit the backup configuration.

When all is configured as desired, click the "Import" button. If you have not checked "Save immediately", the flow will look like it does when [setting up the initial backup](../../getting-started/set-up-a-backup-in-the-ui.md).

If the backup was created with the [store-task-config](import-and-export-backup-configurations.md#storing-configuration-with-the-backup) option enabled, you can also retrieve the configuration directly from the backup destination when restoring or setting up a new machine.

## Storing configuration with the backup

Duplicati can automatically store backup configurations together with the backup data. This makes it easier to recreate a backup job when restoring on a new machine or after losing the local Duplicati database.

{% hint style="info" %}
Avaliable from Duplicati 2.4
{% endhint %}

The behavior is controlled by the `--store-task-config` advanced option, which supports the following modes:

| Mode                         | Description                                                                                                                                            |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Auto` (default)             | For encrypted backups, behaves as `Self`. For unencrypted backups, behaves as `None`.                                                                  |
| `None`                       | No configuration is stored with the backup.                                                                                                            |
| `Self`                       | Stores the current backup configuration with all secrets unconditionally removed.                                                                      |
| `All`                        | Stores the configurations of **all** backups defined in Duplicati, with all secrets unconditionally removed.                                           |
| `SelfWithSecrets`            | Stores the current backup configuration with all secrets included. If the backup is not encrypted, Duplicati logs a warning and reverts to `Self`.     |
| `AllWithSecrets`             | Stores the configurations of **all** backups with all secrets included. If the backup is not encrypted, Duplicati logs a warning and reverts to `All`. |
| `SelfWithUnencryptedSecrets` | Stores the current backup configuration with all secrets included, even if the backup is not encrypted.                                                |
| `AllWithUnencryptedSecrets`  | Stores the configurations of **all** backups with all secrets included, even if the backup is not encrypted.                                           |

When a configuration is stored with the backup, it is saved as a control file named `task-setup.json` and uploaded alongside the backup data. You can later retrieve and import these stored configurations through the Duplicati user interface when connecting to the backup destination.

For backwards compatibility, the option also accepts boolean values: `true` is treated as `Self`, and `false` is treated as `None`.

### Security considerations

Storing backup configurations in the remote backup introduces security risks that should be carefully evaluated before changing the default settings.

**Leaked secrets:** Backup configurations can contain sensitive data such as destination credentials, encryption passphrases, API keys, SMTP credentials for notifications, and paths to remote sources. If these secrets are included in a stored configuration, anyone who gains access to the backup data can extract them. Even if secrets are removed, the configuration may still reveal server names, paths, and other infrastructure details that aid an attacker.

**Cross-contamination between backups:** If you use the `All`, `AllWithSecrets`, or `AllWithUnencryptedSecrets` mode, a single compromised backup destination can expose the complete set of backup configurations. An attacker who obtains access to one backup can extract the credentials for every other backup destination, notification service, and remote source configured in Duplicati. This cross-contamination means that one leak can cascade into a compromise of multiple systems.

**Additional targets and remote sources:** A backup configuration can reference additional storage targets, secondary destinations, SMTP servers for notifications, and remote sources. When secrets are included in the stored configuration—such as with `SelfWithSecrets` or any of the unencrypted-secrets modes—a single leaked backup can provide an attacker with credentials to access these connected systems. This means compromising one backup destination could allow an attacker to disable secondary targets, read notification credentials, or access remote source locations.

**Default protection:** The default `Auto` mode is designed to minimize these risks. When the backup is encrypted, it stores only the current job's configuration and unconditionally strips all secrets (`Self`). When the backup is unencrypted, it stores nothing (`None`). This default ensures that a leaked backup does not contain credentials that can be used to access other systems.

If you choose to override the defaults—especially by using `All`, `AllWithSecrets`, `AllWithUnencryptedSecrets`, `SelfWithUnencryptedSecrets`, or any mode that includes secrets in an unencrypted backup, ensure that the backup destination is strongly protected and that you fully understand the risk of cross-contamination across all your backups, sources, and notification services.
