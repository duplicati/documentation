# Table of contents

* [Duplicati Documentation](README.md)

## Getting Started

* [Installation](getting-started/installation.md)
* [Set up a backup in the UI](getting-started/set-up-a-backup-in-the-ui.md)
* [Running a backup](getting-started/running-a-backup.md)
* [Restoring files](getting-started/restoring-files.md)

## Detailed descriptions

* [Choosing Duplicati Type](detailed-descriptions/choosing-duplicati-type.md)
* [Using the secret provider](detailed-descriptions/using-the-secret-provider/README.md)
  * [Local providers](detailed-descriptions/using-the-secret-provider/local-providers.md)
  * [Cloud providers](detailed-descriptions/using-the-secret-provider/cloud-providers.md)
  * [Advanced configurations](detailed-descriptions/using-the-secret-provider/advanced-configurations.md)
* [Using remote management](detailed-descriptions/using-remote-management/README.md)
  * [Using remote control with agent](detailed-descriptions/using-remote-management/using-remote-control-with-agent.md)
* [Migrating Duplicati to a new machine](detailed-descriptions/migrating-duplicati-to-a-new-machine.md)
* [Scripts](detailed-descriptions/scripts.md)
* [Sending reports](detailed-descriptions/sending-reports-via-email/README.md)
  * [Monitoring with Duplicati Console](detailed-descriptions/sending-reports-via-email/monitoring-with-duplicati-console.md)
  * [Sending reports with email](detailed-descriptions/sending-reports-via-email/sending-reports-with-email.md)
  * [Sending Jabber/XMPP notifications](detailed-descriptions/sending-reports-via-email/sending-jabber-xmpp-notifications.md)
  * [Sending HTTP notifications](detailed-descriptions/sending-reports-via-email/sending-http-notifications.md)
  * [Sending Telegram notifications](detailed-descriptions/sending-reports-via-email/sending-telegram-notifications.md)
  * [Custom message content](detailed-descriptions/sending-reports-via-email/custom-message-content.md)
* [Duplicati Access Password](detailed-descriptions/duplicati-access-password.md)
* [Import and export backup configurations](detailed-descriptions/import-and-export-backup-configurations.md)
* [Filters in Duplicati](detailed-descriptions/filters-in-duplicati.md)
* [The local database](detailed-descriptions/the-local-database.md)
* [The server database](detailed-descriptions/the-server-database.md)
* [Preload settings](detailed-descriptions/preload-settings.md)
* [Retention settings](detailed-descriptions/retention-settings.md)
* [Using Duplicati with Linux](detailed-descriptions/using-duplicati-with-linux.md)
* [Using Duplicati from Docker](detailed-descriptions/using-duplicati-from-docker.md)
* [Using Duplicati with MacOS](detailed-descriptions/using-duplicati-with-macos.md)
* [Using Duplicati with Windows](detailed-descriptions/using-duplicati-with-windows.md)
* [Running a self-hosted OAuth Server](detailed-descriptions/running-a-self-hosted-oauth-server.md)
* [Telemetry collection](detailed-descriptions/telemetry-collection.md)

## Using tools

* [Encrypting and decrypting files](using-tools/encrypting-and-decrypting-files.md)
* [Using Duplicati from the Command Line](using-tools/using-duplicati-from-the-command-line.md)
* [Recovering from failure](using-tools/recovering-from-failure.md)
* [Disaster recovery](using-tools/disaster-recovery.md)

## Backup destinations

* [Destination overview](backup-destinations/destination-overview.md)
* [Standard based destinations](backup-destinations/standard-based-destinations/README.md)
  * [File Destination](backup-destinations/standard-based-destinations/file-destination.md)
  * [S3-compatible Destination](backup-destinations/standard-based-destinations/s3-compatible-destination.md)
  * [FTP Destination](backup-destinations/standard-based-destinations/ftp-destination.md)
  * [SFTP (SSH) Destination](backup-destinations/standard-based-destinations/sftp-ssh-destination.md)
  * [WebDAV Destination](backup-destinations/standard-based-destinations/webdav-destination.md)
  * [OpenStack Destination](backup-destinations/standard-based-destinations/openstack-destination.md)
  * [Rclone Destination](backup-destinations/standard-based-destinations/rclone-destination.md)
  * [CIFS (aka SMB) Destination](backup-destinations/standard-based-destinations/cifs-aka-smb-destination.md)
* [Provider specific destinations](backup-destinations/provider-specific-destinations/README.md)
  * [Backblaze B2 Destination](backup-destinations/provider-specific-destinations/backblaze-b2-destination.md)
  * [Box.com Destination](backup-destinations/provider-specific-destinations/box.com-destination.md)
  * [Rackspace CloudFiles Destination](backup-destinations/provider-specific-destinations/rackspace-cloudfiles-destination.md)
  * [IDrive e2 Destination](backup-destinations/provider-specific-destinations/idrive-e2-destination.md)
  * [Mega.nz Destination](backup-destinations/provider-specific-destinations/mega.nz-destination.md)
  * [Aliyun OSS Destination](backup-destinations/provider-specific-destinations/aliyun-oss-destination.md)
  * [Tencent COS Destination](backup-destinations/provider-specific-destinations/tencent-cos-destination.md)
  * [Jottacloud Destination](backup-destinations/provider-specific-destinations/jottacloud-destination.md)
  * [pCloud Destination](backup-destinations/provider-specific-destinations/pcloud-destination.md)
  * [Azure Blob Storage Destination](backup-destinations/provider-specific-destinations/azure-blob-storage-destination.md)
  * [Google Cloud Storage Destination](backup-destinations/provider-specific-destinations/google-cloud-storage-destination.md)
  * [Microsoft Group Destination](backup-destinations/provider-specific-destinations/microsoft-group-destination.md)
  * [SharePoint Destination](backup-destinations/provider-specific-destinations/sharepoint-destination.md)
  * [SharePoint v2 (Graph API)](backup-destinations/provider-specific-destinations/sharepoint-v2-graph-api.md)
  * [Amazon S3 destination](backup-destinations/provider-specific-destinations/amazon-s3-destination.md)
  * [Filen.io](backup-destinations/provider-specific-destinations/filen.io.md)
  * [Filejump](backup-destinations/provider-specific-destinations/filejump.md)
* [File synchronization providers](backup-destinations/file-synchronization-providers/README.md)
  * [Dropbox Destination](backup-destinations/file-synchronization-providers/dropbox-destination.md)
  * [Google Drive Destination](backup-destinations/file-synchronization-providers/googledrive-destination.md)
  * [OneDrive Destination](backup-destinations/file-synchronization-providers/onedrive-destination.md)
  * [OneDrive For Business Destination](backup-destinations/file-synchronization-providers/onedrive-for-business-destination.md)
* [Decentralized providers](backup-destinations/decentralized-providers/README.md)
  * [Sia Destination](backup-destinations/decentralized-providers/sia-destination.md)
  * [Storj Destination](backup-destinations/decentralized-providers/storj-destination.md)
  * [TahoeLAFS destination](backup-destinations/decentralized-providers/tahoelafs-destination.md)

## Duplicati Programs

* [TrayIcon](duplicati-programs/trayicon.md)
* [Server](duplicati-programs/server.md)
* [Command Line Interface CLI](duplicati-programs/command-line-interface-cli.md)
* [Service and WindowsService](duplicati-programs/service.md)
* [Command Line Tools](duplicati-programs/command-line-interface-cli-1/README.md)
  * [AutoUpdater](duplicati-programs/command-line-interface-cli-1/autoupdater.md)
  * [BackendTester](duplicati-programs/command-line-interface-cli-1/backendtester.md)
  * [BackendTool](duplicati-programs/command-line-interface-cli-1/backendtool.md)
  * [RecoveryTool](duplicati-programs/command-line-interface-cli-1/recoverytool.md)
  * [SecretTool](duplicati-programs/command-line-interface-cli-1/secrettool.md)
  * [ServerUtil](duplicati-programs/command-line-interface-cli-1/serverutil.md)
  * [SharpAESCrypt](duplicati-programs/command-line-interface-cli-1/sharpaescrypt.md)
  * [Snapshots](duplicati-programs/command-line-interface-cli-1/snapshots.md)
  * [SyncTool](duplicati-programs/command-line-interface-cli-1/synctool.md)
* [Agent](duplicati-programs/agent.md)
* [LICENSE](duplicati-programs/license/README.md)
  * [Duplicati Inc & Open Source](duplicati-programs/license/duplicati-inc-and-open-source.md)
  * [License Agreement](duplicati-programs/license/license-agreement.md)
* [OAuth Server](duplicati-programs/oauth-server.md)

***

* [SUPPORT](support.md)

## Installation details

* [Release channels and versions](installation-details/release-channels-and-versions/README.md)
  * [Upgrading and downgrading](installation-details/release-channels-and-versions/upgrading-and-downgrading.md)
  * [Downgrade from 2.1.0.2 to 2.0.8.1](installation-details/release-channels-and-versions/downgrade-from-2.1.0.2-to-2.0.8.1.md)
* [Package options](installation-details/package-options.md)
* [Developer](installation-details/developer.md)

## Duplicati Console

* [Organizations and sub-organizations](duplicati-console/organizations-and-sub-organizations.md)
* [Single Sign-On (SSO)](duplicati-console/single-sign-on-sso.md)

***

* [TECHNICAL DETAILS](technical-details/README.md)
  * [Architecture Premises](technical-details/architecture-premises.md)
  * [Understanding Backup](technical-details/understanding-backup/README.md)
    * [How Backup Works](technical-details/understanding-backup/how-backup-works.md)
    * [Encryption Algorithms](technical-details/understanding-backup/encryption-algorithms.md)
    * [Backup size parameters](technical-details/understanding-backup/backup-size-parameters.md)
  * [Understanding Restore](technical-details/understanding-restore/README.md)
    * [How Restore Works](technical-details/understanding-restore/how-restore-works.md)
    * [Disaster Recovery](technical-details/understanding-restore/disaster-recovery.md)
  * [Database versions](technical-details/database-versions.md)
  * [Server authentication model](technical-details/server-authentication-model.md)
  * [Option formats](technical-details/option-formats.md)

## Community docs

* [Community docs: home](community-docs/community-docs-home.md)
* [Community docs: introduction](community-docs/community-docs-introduction.md)
* [Community docs: installation](community-docs/community-docs-installation.md)
* [Community docs: using the graphical user interface](community-docs/community-docs-using-the-graphical-user-interface.md)
* [Community docs: using duplicati from the commandline](community-docs/community-docs-using-duplicati-from-the-commandline.md)
* [Community docs: storage providers](community-docs/community-docs-storage-providers.md)
* [Community docs: advanced options](community-docs/community-docs-advanced-options.md)
* [Community docs: other commandline utilities](community-docs/community-docs-other-commandline-utilities.md)
* [Community docs: disaster recovery](community-docs/community-docs-disaster-recovery.md)
* [Community docs: how the backup process works](community-docs/community-docs-how-the-backup-process-works.md)
* [Community docs: how the restore process works](community-docs/community-docs-how-the-restore-process-works.md)
* [Community docs: choosing sizes in Duplicati](community-docs/community-docs-choosing-sizes-in-duplicati.md)
* [Community docs: filters](community-docs/community-docs-filters.md)
* [Community docs: how we get along with oauth](community-docs/community-docs-how-we-get-along-with-oauth.md)
* [Community docs: example scripts](community-docs/community-docs-example-scripts.md)
