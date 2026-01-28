---
description: This page describes the different packages offered by Duplicati
---

# Package options

To better fit a number of different use-cases, Duplicati is packaged into different installers, with different purposes. The choice of which package makes most sense is related to where and how you intend yo use Duplicati. There are three parameters that define the packages:

* Operating system and processor type
* Usage type
* Installation type

The operating system and processor type are defined by the machine you run Duplicati on. If you are unsure about the processor type, it is usually `x64` for Windows and Linux, and `arm64` for MacOS. Starting from the operating system, you can then read about the options for each system.

## Windows

Because every Windows version includes a desktop component, there are only two options for the usage type: [GUI](../duplicati-programs/trayicon.md) and [Agent](../duplicati-programs/agent.md).

The most versatile option is the **GUI** package, which also includes all the commandline utilities. Due to the way Windows works, it is possible to install the GUI version and use only the [Server](../duplicati-programs/server.md) or [Commandline](../duplicati-programs/command-line-interface-cli-1/) components. This works beacuse each component is an executable that can be launched independently. The overhead for the unused executables is very low, usually less than 1 MiB.

The **Agent** form is a reduced version of Duplicati, usable for integrating with the [Duplicati Console](/broken/pages/mwwrECckpb1xj3XLM1fk) and is intended to run without any local access possible.

In terms of installation type there are two types offered for Windows: MSI and Zip. The contents of both are the same, so you can pick the installation type that best suits your needs. The MSI package is easier to integrate with some package managers and also offers the options to install shortcuts. The Zip package is just all the contents compressed and ready to use.

## Linux

It is common to have Linux installations that do not have any desktop components, meaning they are purely servers with no display attached. Since these setups do not have any destop packages installed, installing the GUI package on such a system would require installing a large set of desktop-related packages. For this reason, the Linux packages are available as [GUI](../duplicati-programs/trayicon.md), [CLI](../duplicati-programs/command-line-interface-cli-1/), and [Agent](../duplicati-programs/agent.md) forms.

The **GUI** package has the most dependencies as it requires a desktop environment. This package also includes all the [commandline components](../duplicati-programs/command-line-interface-cli-1/) together with the [TrayIcon](../duplicati-programs/trayicon.md) application that is shown in the system menu or activity area.

The **CLI** package has very few dependencies, as everything is bundled with the package. It is essentially the same as the GUI package, but without the TrayIcon executable.

The **Agent** form is a reduced version of Duplicati, usable for integrating with the [Duplicati Console](/broken/pages/mwwrECckpb1xj3XLM1fk) and is intended to run without any local access possible.

For installation, Duplicati offers `.deb` packages for the Debian based distros, such as Ubuntu and Mint, as well as `.rpm` packages for the Redhat based distros, such as CentOS. There are also `.zip` packages available, which allows you to fully self-manage the installation and dependencies. If you are using the `.zip` packages the only thin missing is the service files used to integrate with `launchctl`. Duplicati is also available as an AppImage for a self-contained, portable install.

If you are using [SELinux](https://en.wikipedia.org/wiki/Security-Enhanced_Linux), there is currently an extra step to ensure that the binaries, which are placed in `/usr/lib/duplicati`, are allowed to execute:

```
semanage fcontext -a -t bin_t '/usr/lib/duplicati/duplicati'
semanage fcontext -a -t bin_t '/usr/lib/duplicati/duplicati-*'
restorecon -r /usr/lib/duplicati/
```

## MacOS

For MacOS the **GUI** [TrayIcon](../duplicati-programs/trayicon.md) is bundled inside a MacOS app-bundle, similar to other applications on MacOS. Inside the app bundle are the [commandline components](../duplicati-programs/command-line-interface-cli-1/) and they can be invoked as binaries from within the app bundle. The GUI application is packaged as a `.dmg` file that supports drag-n-drop style installation, as well as a `.pkg` installer package. The contents of the two package formats are the same, but the `.pkg` also installs a launchagent which automatically starts Duplicati on login.

If the UI is not needed, there is also a **CLI** version which does not contain the TrayIcon. This version installs itself into the commandline path for easy use from the commandline. Since the CLI version does not need an app bundle, there is only a `.pkg` version of the CLI package. The package also installs a launchagent that can be edited to allow starting the [Server](../duplicati-programs/server.md) on login.

The **Agent** form is a reduced version of Duplicati, usable for integrating with the [Duplicati Console](/broken/pages/mwwrECckpb1xj3XLM1fk) and is intended to run without any local access possible. Since the agent does not need an app bundle it is only available as a `.pkg` file.

## Synology

Duplicati provides a Synology DSM `.spk` package to install on Synology NAS devices. After installation, make sure to grant Duplicati access to the shared folders you want to back up (or restore to); DSM permissions must allow Duplicati to read or write those shares for backups and restores to succeed.
