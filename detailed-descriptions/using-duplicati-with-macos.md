---
description: This page describes common scenarios for configuring Duplicati with MacOS
---

# Using Duplicati with MacOS

Before you can install Duplicati, you need to decide on two different parameters:

* The type you want: [GUI](../duplicati-programs/trayicon.md), [Server](../duplicati-programs/server.md), [Agent](../duplicati-programs/agent.md), [CLI](../duplicati-programs/command-line-interface-cli.md).&#x20;
* You machine CPU type: Arm64 or x64

## Deciding on type

To use Duplicati on MacOS, you first need to decide which kind of instance you want: GUI (aka [TrayIcon](../duplicati-programs/trayicon.md)), [Server](../duplicati-programs/server.md), [Agent](../duplicati-programs/agent.md), [CLI](../duplicati-programs/command-line-interface-cli.md). The section on [Choosing Duplicati Type](choosing-duplicati-type.md) has more details on each of the different types. For home users, the common choice is the GUI package in `.dmg`format. For enterprise rollouts, you can choose the `.pkg` packages.

## Determine CPU architecture

Your Mac is most likely using Arm64 with one of the M1, M2, M3, or M4 chips. If you have an older Mac, it may use the Intel x64 chipset. To see what CPU you have, click the Apple icon and choose "About this Mac". In the field labelled "Chip" it will either show Intel (x64) or M1, M2, M3, M4 (Arm64).

## Installing the package

The packages can be obtained via the [main download page](https://duplicati.com/download). The default package shown on the page is the MacOS Arm64 GUI package in `.dmg` format. If you need another version click the "Other versions" link at the bottom of the page.

If you are using the `.dmg` package the installation works similar to other application, simply open the `.dmg` file and drag Duplicati into Applications. Note that with the `.dmg` package, Duplicati is not set to start automatically with your Mac, but if you restart with the option to re-open running programs, Duplicati will start on login.

If you are using the `.pkg` package, Duplicati will install a `launchAgent` that ensures Duplicati starts on reboots. The CLI package installs a stub file that is not active, so you can edit the `launchAgent` and have it start the Server if you prefer.

## Using the TrayIcon

If you have installed the GUI package, you will have Duplicati installed in `/Applications` and it can be started like any other application. Once Duplicati is started, it will place itself in the menu bar near the clock and battery icons. Because Duplicati is meant to be a background program, there is no Duplicati icon in the dock.

&#x20;On the first start Duplicati will also open your browser and allow you to configure your backups. If you need access to the UI again later, locate the TrayIcon in the status bar, click it and click "Open". If you install the CLI or Agent packages, the Duplicati application is not available.

## Using the Server

If you install the CLI package, Duplicati binaries are placed in `/usr/local/duplicati` and symlinked into `/usr/local/bin` and you can start the server simply by running:

```sh
duplicati-server
```

When invoked as a regular user, it will use the same folder, `~/Library/Application Support/Duplicati`, as the [TrayIcon](../duplicati-programs/trayicon.md) and share the configuration.

**Note**: If you install the GUI package or install from homebrew, Duplicati's binaries are not symlinked into the paths searched by MacOS. You can invoke the binaries by supplying the full path:

```sh
/Applications/Duplicati.app/Contents/MacOS/duplicati-server
```

## Using the Agent

With the [Agent](../duplicati-programs/agent.md) there is a minimal setup required, which is to register the machine with the Duplicati Console. When installing the Agent package, it will automatically register the Duplicati agent with a `launchAgent` that starts Duplicati in an Agent mode.

If the Agent is not registered with the Console, it will open the default browser and ask to be registered. Once registered, it will run in the background and be avilable on the Duplicati Console for management.

If you have a [pre-authenticated link](../duplicati-programs/agent.md#registering-the-machine) for registering the machine, you can place a file in `/usr/local/share/Duplicati/preload.json` with content similar to:

```json
{
  "args": {
    "agent": [ "--agent-registration-url=<registration-url>" ]
  }
}
```

## Using the CLI

Using the CLI is simply a matter of invoking the binary:

```sh
duplicati-cli help
```

Since the CLI also needs a local database for each backup, it will use the same location as [described for the Server](using-duplicati-with-macos.md#using-the-server) above to place databases. In addition to this, it will keep a small file called `dbconfig.json` in the storage folder where it maps URLs to databases. The intention of this is to avoid manually specifying the `--dbpath` parameter on every invocation.

If you specify the `--dbpath` parameter, it will not use the `dbconfig.json` file and it will not store anything in the local datafolder.

**Note**: If you install the GUI package or install from homebrew, Duplicati's binaries are not symlinked into the paths searched by MacOS. You can invoke the binaries by supplying the full path:

```sh
/Applications/Duplicati.app/Contents/MacOS/duplicati-cli help
```

## Using the support programs

Each package of Duplicati contains a number of support utilities, such as the [RecoveryTool](../duplicati-programs/command-line-interface-cli-1/recoverytool.md). Each of these can be invoked from the commandline with a `duplicati-*` name and all contain built-in help. For example, to invoke [ServerUtil](../duplicati-programs/command-line-interface-cli-1/serverutil.md), run:

```sh
duplicati-server-util help
```

**Note**: If you install the GUI package or install from homebrew, Duplicati's binaries are not symlinked into the paths searched by MacOS. You can invoke the binaries by supplying the full path:

```sh
/Applications/Duplicati.app/Contents/MacOS/duplicati-server-util help
```
