---
description: This page describes how to import and export configurations from Duplicati
---

# Import and export backup configurations

While it is not required that you keep a copy of the backup configuration, it can sometimes be convenient to have all settings related to a backup stored in a single file.&#x20;

## Export

To export from within the user interface, expand the backup configuration and click "Export ..."

<div>

<figure><img src="../.gitbook/assets/Screenshot 2024-11-20 at 16.24.37.png" alt=""><figcaption><p>Expanded backup configuration</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/Screenshot 2024-11-20 at 16.25.02.png" alt=""><figcaption><p>Choosing export options</p></figcaption></figure>

</div>

On this page you should select "To File", which is default. The option to export "As commandline..." is not covered here, but allows you to get a string that can be used with the [Duplicati CLI executable](../duplicati-programs/command-line-interface-cli.md).

You then need to decide on how to handle secrets stored in the configuration. Since these secrets include both the credentials to connect to the remote destination as well as the encryption passphrase, it is important that the exported file is protected.

You can choose to not include any secrets by unchecking the "Export passwords" option. The resulting file will then not contain the secrets and you need to store them in a different place (credential vault, keychain, etc).

You can also choose to encrypt the file before exporting it. If you choose this option, make sure you choose a strong unique passphrase, and store that passphrase in a safe location.

If you choose to export with passwords but without encryption, you will be warned that this is insecure:

<figure><img src="../.gitbook/assets/Screenshot 2024-11-20 at 16.45.32.png" alt="" width="375"><figcaption><p>Warning shown when exporting without encryption</p></figcaption></figure>

After completing the export, you will get a file containing the backup configuration. The file is in JSON format and optionally encrypted with [AESCrypt](../using-tools/encrypting-and-decrypting-files.md).

## Import configuration

With an exported configuration, you can delete an existing configuration and re-create it by importing the configuration. You can optionally edit the parameters so the re-created backup configuration differs from the original.

To import a configuration, go to the "Add backup" page and choose "Import from file":

<div>

<figure><img src="../.gitbook/assets/Screenshot 2024-11-20 at 16.58.53.png" alt=""><figcaption><p>Adding a backup</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/Screenshot 2024-11-20 at 16.58.39.png" alt=""><figcaption><p>Choosing import options</p></figcaption></figure>

</div>



Pick the file or drag-n-drop it  on the file chooser. If the file is encrypted, provide the file encryption passphrase here as well.

The option to "Import metadata" will create the new backup configuration and restore the statistics, including backup size, number of versions, etc. from the data in the file. If not checked, these will not be filled, and will be updated when the first backup is executed.

If the option "Save immediately" is checked, the backup will be created when clicking import, skipping the option to edit the backup configuration.

When all is configured as desired, click the "Import" button. If you have not checked "Save immediately", the flow will look like it does when [setting up the initial backup](../getting-started/set-up-a-backup-in-the-ui.md).
