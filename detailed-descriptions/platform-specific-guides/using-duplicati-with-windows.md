---
description: This page describes common scenarios for configuring Duplicati with Windows
---

# Using Duplicati with Windows

Before you can install Duplicati, you need to decide on three different parameters:

* The type you want: [GUI](../duplicati-programs/trayicon.md), [Server](../duplicati-programs/server.md), [Agent](../duplicati-programs/agent.md), [CLI](../duplicati-programs/command-line-interface-cli.md).&#x20;
* You machine CPU type: x64, Arm64, or x86 (32 bit)

## Deciding on type

To use Duplicati on Windows, you first need to decide which kind of instance you want: GUI (aka [TrayIcon](../duplicati-programs/trayicon.md)), [Server](../duplicati-programs/server.md), [Agent](../duplicati-programs/agent.md), [CLI](../duplicati-programs/command-line-interface-cli.md). The section on [Choosing Duplicati Type](./choosing-duplicati-type.md) has more details on each of the different types.

## Determine CPU architecture

Finally you need to locate information on what CPU architecture you are using:

* x64: 64bit Intel or AMD based CPU. This is the most common CPU at this time.
* Arm64: 64bit ARM based CPU. Some laptops, tablets and servers use it.
* x86: 32bit Intel or AMD based CPU. Note that Windows 10 was the last version to support 32 bit processors.

If you are in doubt, you can try the x64 version, or use [Microsofts guide for determining the CPU](https://support.microsoft.com/en-us/windows/32-bit-and-64-bit-windows-frequently-asked-questions-c6ca9541-8dce-4d48-0415-94a3faa2e13d).

## Installing the package

Once you have decided the on package you want, you are ready to download the package. The default version shown on the main [download page](https://duplicati.com/download) is the x64 GUI version in `.msi` format. The full list of packages can be obtained via the main [download page](https://duplicati.com/download), and then clicking "Other versions".&#x20;

## Using the TrayIcon

For users with a desktop environment and no special requirements, the [TrayIcon](../duplicati-programs/trayicon.md) instance is the recommended way to run Duplicati. If you are using the `.msi` package to install Duplicati, you will see an option to automatically start Duplicati, as well as create a shortcut on your desktop and in the start menu. If you need to manually start Duplicati, you can find the executable in:

```
C:\Program Files\Duplicati 2\Duplicati.GUI.TrayIcon.exe
```

When running the TrayIcon in a user context, it will create a folder in your home folder, typically `C:\Users\<username>\AppData\Local\Duplicati` where it stores the local databases and the Server database with the backup configurations.

## Using the Server

The [Server](../duplicati-programs/server.md) is a regular executable and can simply be invoked with:

```
C:\Program Files\Duplicati 2\Duplicati.Server.exe
```

When invoked as a regular user, it will use the same folder, `C:\Users\<username>\AppData\Local\Duplicati`, as the [TrayIcon](../duplicati-programs/trayicon.md) and share the configuration.

### Running the Server as a Windows Service

If you want to run Duplicati as a Windows Service, you can use the bundled service tool to install/uninstall the service:

```
C:\Program Files\Duplicati 2\Duplicati.WindowsService.exe INSTALL
C:\Program Files\Duplicati 2\Duplicati.WindowsService.exe UNINSTALL
```

When installing the Service it will automatically start, and likewise, uninstalling it will stop the service. If you need to pass options to the server, you can provide them to the INSTALL command:

{% code overflow="wrap" %}
```
C:\Program Files\Duplicati 2\Duplicati.WindowsService.exe INSTALL --webservice-port=8100 --server-datafolder=<path>
```
{% endcode %}

You can also use the [preload.json](preload-settings.md) file to pass settings to the Server when running as a service, which allows you to change the settings without the uninstall/install cycle (you still need to restart the service).

**Note**: When running the Windows Service it will default to use port 8200 and fail it that port is not available. If you are running the TrayIcon, that will run a **different** instance, usually at port 8300. If you want to connect the TrayIcon to the Windows Service, edit the shortcut to Duplicati:

{% code overflow="wrap" %}
```
C:\Program Files\Duplicati 2\Duplicati.GUI.TrayIcon.exe --no-hosted-server --host-url=http://localhost:8200 --webservice-password=<password>
```
{% endcode %}

## Using the Agent

With the [Agent](../duplicati-programs/agent.md) there is a minimal setup required, which is to register the machine with the Duplicati Console. The default installation is to install the Agent as a Windows Service, meaning it will run in the LocalService system account, instead of the local user. Due to this, it will not be able to open the browser and start the registration process for you. Instead, you must look into the Windows Event Viewer and extract the registration link from there.

You can also register the Agent, using the Agent executable:

```sh
C:\Program Files\Duplicati 2\Duplicati.Agent.exe register <registration url>
```

After the Agent has been registered, restart the service and it will now be available on the Duplicati Console.

If you have  a [pre-authenticated link](../duplicati-programs/agent.md#registering-the-machine) for registering the machine, and would like to automate the process, you can place a file in `C:\ProgramData\Duplicati\preload.json` with content similar to:

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
C:\Program Files\Duplicati 2\Duplicati.CommandLine.exe help
```

Since the CLI also needs a local database for each backup, it will use the same location as [described for the Server](using-duplicati-with-windows.md#using-the-server) above to place databases. In addition to this, it will keep a small file called `dbconfig.json` in the storage folder where it maps URLs to databases. The intention of this is to avoid manually specifying the `--dbpath` parameter on every invocation.

If you specify the `--dbpath` parameter, it will not use the `dbconfig.json` file and it will not store anything in the local datafolder.

## Using the support programs

Each package of Duplicati contains a number of support utilities, such as the [RecoveryTool](../duplicati-programs/command-line-interface-cli-1/recoverytool.md). Each of these can be invoked from the commandline with their executable name and all contain built-in help. For example, to invoke [ServerUtil](../duplicati-programs/command-line-interface-cli-1/serverutil.md), run:

```sh
 C:\Program Files\Duplicati 2\Duplicati.CommandLine.ServerUtil.exe help
```

## Handling locked files

On Windows, file locking is an integrated part of how programs expect the system to work. This is problematic for backup systems that want to read all the files, locked or not, to ensure they are backed up.

Windows offers two primary ways to read locked files: VSS and BackupRead. Both ways require that the caller has the SeBackupPrivilege enabled and will then permit the caller to read the files. Unfortunately, this is implemented with a permission override as well, meaning that a caller with the SeBackupPrivilege has access to read any file on the system.

In other words: it is not possible to read the user's own locked files without also granting full read access to the system. Since it is a security issue to have a regular account that has access to all files, the two options are: run as a user with elevated privileges (e.g., Administrator) or ignore locked files.&#x20;

For simplicity, most users prefer to [run Duplicati as a service](using-duplicati-with-windows.md#running-the-server-as-a-windows-service), which will run in the LocalSystem account that already has the SeBackupPrivilege set.

### Volume Snapshot Service (VSS)

The most robust and heavy handed way of making a backup on Windows is to use VSS to create a snapshot of the current disk, and then read this. The implementation on Windows signals all programs that want to write to disk that a snapshot is being prepared, waits for them to flush to disk, makes the snapshot and lets all programs continue. This approach ensures that the disk snapshot is in a consistent state so there are not partial writes being backed up.&#x20;

To enable VSS, set the advanced option `--snapshot-policy=required` . If you are using Duplicati 2.1.1.0 or later, VSS will be enabled by default if the user context has the SeBackupPrivilege.

### BackupRead

The BackupRead method does not create a snapshot and instead relies on a Windows API call that allows a program to read files for backup purposes. The benefit from this is that you do not need to create disk snapshots, which requires extra disk space and co-operation from other programs.

To enable BackupRead, set `--backupread-policy=required` and `--snapshot-policy=off` to ensure you are only using BackupRead. Note that the `--backupread-policy` option is currently only available in the [canary builds](../installation-details/release-channels-and-versions/).
