---
description: >-
  This page describes how to install Duplicati on the various supported
  platforms
---

# Installation

## The Duplicati package types

For desktop and laptop users, the most common application type is called the "GUI" package, which is short for Graphical User Interface. The GUI package includes the core components, a webserver to show the user interface and a tray icon (also called a status bar icon).

For users installing in environments without a desktop or screen, there are also commandline-only, remote managent and Docker versions. Depending on your setup, you may also want to use one of those packages on a desktop or laptop.

This page covers only the GUI installation.

Jump to the section that is relevant to you:

* [Install on Windows](installation.md#install-duplicati-on-windows)
* [Install on Linux](installation.md#install-duplicati-on-linux)
* [Install on MacOS](installation.md#install-duplicati-on-macos)

## Install Duplicati on Windows

The most common installation format on Windows is the MSI package. To install on Windows you need to know what kind of processor is on your system. If you are unsure, you are most likely using the 64-bit processor, also known as `x64`. There is also a version supporting `Arm64` processors, and a version for legacy 32-bit Windows called `x86`.

Simply head over to the [Duplicati download page](https://duplicati.com/download) and download the relevant MSI package. Once downloaded, double-click the installer. The installation dialog lets you adjust settings to your liking and will install Duplicati. The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

## Install Duplicati on MacOS

For MacOS the common installation method is to use a DMG file with the application file. Most modern MacOS machines are using the Apple Silicon which is called `Arm64` in Duplicati's packages. If you are on an older Mac that has a 64-bit Intel processor, you can use the `x64` package instead.

Once you know which kind of Mac you have, header over to the  [Duplicati download page](https://duplicati.com/download) and download the relevant DMG file. Open the file and drag Duplicati into the Application folder, and then you can start Duplicati.

The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

## Install Duplicati on Linux

Most Linux distributions work well with Duplicati but there are only packages for Debian based distributions (Ubuntu, Mint, etc) and for RedHat based based distributions (Fedora, SUSE, etc). For other distributions you may need to manually install some dependencies.

For Linux distributions there are packages for the most common 64-bit based system with `x64`, support for `Arm64` and the predecessor `Arm7` aka `ArmHF` which are commonly found in NAS boxes and the older Raspberry Pi series.

### Install on Debian-based Linux (Ubuntu, Mint, etc)

To install Duplicati on a Debian based system, first download the `.deb` package matching the system architecture, then run:

```sh
dpkg -i duplicati-version-arch.deb
```

This will install all dependencies and place Duplicati in the default location on the target system. The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

### Install on RedHat-based Linux (Fedora, SUSE, etc)

To install Duplicati on a RedHat-based system, first download the `.rpm` package matching the system architecture, then run:

```sh
yum -i duplicati-version-arch.rpm
```

This will install all dependencies and place Duplicati in the default location on the target system. The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).

### Install on another Linux distribution

For other linux distributions you can use the `.zip` file that matches your system architecture. Inside the zip files are all the binaries that are needed, and you can simply place them in a folder that works for your system. Generally, all dependecies are inlcuded in the packages so unless you are using a very slimmed down setup, it should work without additional packages.

The first time Duplicati starts up, it will open the user interface in your browser. At this point you are ready to [set up a backup](set-up-a-backup-in-the-ui.md).



