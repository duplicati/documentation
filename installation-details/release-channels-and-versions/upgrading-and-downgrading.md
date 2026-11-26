---
description: >-
  This page describes how to downgrade Duplicati from a newer version to an
  older version
---

# Upgrading and downgrading

## Upgrading

To upgrade Duplicati, install the new version over the existing one. You do not need to uninstall the previous version first:

* Windows: run the new MSI package. It replaces the installed version and keeps the installation folder.
* Linux: install the new `.deb` or `.rpm` package in the same way as the first installation.
* MacOS: open the new `.dmg` file and replace the application, or run the new `.pkg` installer.

Duplicati does not delete your configurations or backup databases when it is upgraded or uninstalled, so your settings and jobs will remain intact.

The server regularly checks for new versions and shows "New update found" in the user interface when one is available. You can also check from the "About Duplicati" page with "Check for updates", and download a new version from the "Download update" link on the same page. From the commandline, the [AutoUpdater](../../duplicati-programs/command-line-interface-cli-1/autoupdater.md) can check for a new version with `duplicati-autoupdater check` and download the package that matches your system with `duplicati-autoupdater download`.

From canary 2.4.1.103 and stable 2.4.1.1, the "About" page shows the result of the last update check, so it is possible to see why a check failed, for example because the update server could not be reached. The notification about an available update can also show the changelog of the new version.

### Installing updates from the user interface

From canary 2.4.1.101, an update can be installed directly from the new user interface. When an update is available, the "About" page shows an "Install update" button with the new version, and the update notification has an "Install" button.

The install is handled by a small helper program. The running Duplicati starts the helper and exits. The helper checks the signature of the downloaded package, installs it with the method for the system (MSI, macOS pkg or DMG, deb, rpm, AppImage or zip), removes the old version where needed, and starts the new version. If the install needs administrator permissions, the helper asks for them.

Docker, Synology and QNAP installations cannot be updated this way, and do not show the install button. Update these by installing the new image or package as usual.

{% hint style="warning" %}
This is the first version of the new updater. When Duplicati runs as a service with a separate TrayIcon, the TrayIcon is stopped during the update so no files are locked, but it is not started again afterwards. The service itself is restarted. Start the TrayIcon again manually after the update.
{% endhint %}

### Database upgrades

When Duplicati starts and discovers an older version of its configuration, it will automatically update the configuration and databases to the new version.

Installing new versions of Duplicati is part of the test process so any upgrade is intended to keep things working the same as before. In some cases the updates will start to give a warning on backups that were previously running without a warning. These warnings will describe what has changed and explain what to do to remove the warning.

Such warnings generally relate to a feature that will be removed or renamed but is not yet removed. The warnings give you a heads-up to avoid issues in the future and are generally simple to implement by editing a backup.

In rare cases a feature can no longer be supported, such as when a storage provider stops offering a service. For these, the feature will be removed and this will be mentioned in the release notes.

## Downgrading

Downgrades are usually not supported automatically because the old version was created before the current version; the code inside the old version cannot know what was changed. To avoid data loss, this process is controlled by version numbers inside the database. If you want to downgrade, you must use the [DatabaseTool](../../duplicati-programs/command-line-interface-cli-1/databasetool.md) manually **before** installing the older version.

Each update to the data will increment the version number of the database such that when an older version is running it will detect a higher number than it knows and stop there.

From canary 2.4.1.100, the upgrade and downgrade scripts are stored in the databases themselves. This means the DatabaseTool bundled with an older version can downgrade a database written by a newer version, so the downgrade no longer has to be done before the newer version is uninstalled. See the [DatabaseTool](../../duplicati-programs/command-line-interface-cli-1/databasetool.md) for details.

When a version upgrades the database, it will create a backup of the current database before upgrading. You can look for the database and backups in:

* `~/.config/Duplicati` on Linux
* `~/Library/Application Support/Duplicati` on MacOS
* `%LOCALAPPDATA%\Duplicati` on Windows.

If you have been using the new version you may have changes in the current database that would be lost by restoring the pre-upgrade database. In that case, you can [ask on the forum](https://forum.duplicati.com) for advice on how to downgrade.
