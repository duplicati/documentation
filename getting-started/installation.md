---
description: >-
  This page describes how to install Duplicati on the various supported
  platforms
---

# Installation

## The Duplicati package types

For desktop and laptop users, the most common application type is called the "GUI" package, which is short for Graphical User Interface. The GUI package includes the core components, a webserver to show the user interface and a tray icon (also called a status bar icon).

For users installing in environments without a desktop or screen, there are also commandline only, remote management and Docker versions. Depending on your setup, you may also want to use one of those packages on a desktop or laptop.

This page covers only the GUI installation.

Jump to the section that is relevant to you:

* [Install on Windows](installation.md#install-duplicati-on-windows)
* [Install on Linux](installation.md#install-duplicati-on-linux)
* [Install on MacOS](installation.md#install-duplicati-on-macos)
* [Install on Synology](installation.md#install-duplicati-on-synology)

## Install Duplicati on Windows

The most common installation format on Windows is the MSI package. To install on Windows you need to know what kind of processor is on your system. If you are unsure, you are most likely using the 64-bit processor, also known as `x64`. There is also a version supporting `Arm64` processors, and a version for legacy 32-bit Windows called `x86`.

Simply head over to the [Duplicati download page](https://duplicati.com/download) and download the relevant MSI package. Once downloaded, double-click the installer. The installation dialog lets you adjust settings to your liking and will install Duplicati. The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

## Install Duplicati on MacOS

For MacOS the common installation method is to use a DMG file with the application file. Most modern MacOS machines are using the Apple Silicon which is called `Arm64` in Duplicati's packages. If you are on an older Mac that has a 64-bit Intel processor, you can use the `x64` package instead.

Once you know which kind of Mac you have, head over to the  [Duplicati download page](https://duplicati.com/download) and download the relevant DMG file. Open the file and drag Duplicati into the Application folder, and then you can start Duplicati.

The GUI package is also available as a `.pkg` installer, which also sets up Duplicati to start automatically when you log in. See [package options](../installation-details/package-options.md#macos) for details.

The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

## Install Duplicati on Linux

Most Linux distributions work well with Duplicati but there are only packages for Debian based distributions (Ubuntu, Mint, etc) and for RPM based distributions (Fedora, RHEL, SUSE, etc). For other distributions you may need to manually install some dependencies.

For Linux distributions there are packages for the most common 64-bit based system with `x64`, support for `Arm64` and the predecessor `Arm7` aka `ArmHF` which are commonly found in NAS boxes and the older Raspberry Pi series.

### Install on Debian-based Linux (Ubuntu, Mint, etc)

To install Duplicati on a Debian based system, first download the `.deb` package matching the system architecture, then run:

```sh
sudo apt install ./duplicati-version-arch.deb
```

This will install all dependencies and place Duplicati in the default location on the target system. The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

### Install on RPM-based Linux (Fedora, RHEL, SUSE, etc)

To install Duplicati on an RPM-based system, first download the `.rpm` package matching the system architecture, then run:

```sh
sudo dnf install ./duplicati-version-arch.rpm
```

On older RedHat-based systems use `sudo yum install ./duplicati-version-arch.rpm`, and on SUSE use `sudo zypper install ./duplicati-version-arch.rpm`.

This will install all dependencies and place Duplicati in the default location on the target system. The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

### Install on another Linux distribution

For other linux distributions you can use the `.zip` file that matches your system architecture. Inside the zip files are all the binaries that are needed, and you can simply place them in a folder that works for your system. Generally, all dependencies are included in the packages so unless you are using a very slimmed down setup, it should work without additional packages.

The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

### Install as an AppImage

The GUI package is also available as an AppImage, which runs on most distributions without installing anything. Download the `.appimage` file that matches your system architecture, make it executable and start it:

```sh
chmod +x duplicati-version-arch-gui.appimage
./duplicati-version-arch-gui.appimage
```

## Install Duplicati on Synology

For Synology NAS devices, download the `.spk` package that matches your device and install it with "Manual Install" in the DSM Package Center. See [package options](../installation-details/package-options.md#synology) for details on how the package works and which permissions it needs.

## The first start

The first time you open the user interface, it shows a welcome page that offers to connect Duplicati to the [Duplicati Console](https://app.gitbook.com/s/7cMJiGuXy340g9NzpxLY/duplicati-console). Choose "Connect to console" to connect, or "Proceed without connecting to console" and confirm with "Proceed without connecting" to use Duplicati on its own. You can also connect to the console later from the "Settings" page.

## Upgrading an existing installation

If you already have Duplicati installed, you can upgrade by installing the new version over the existing one; you do not need to uninstall the previous version first. Duplicati does not delete your configurations or backup databases when it is upgraded or uninstalled, so your settings and jobs will remain intact. For more details, see [Upgrading and downgrading](../installation-details/release-channels-and-versions/upgrading-and-downgrading.md).


