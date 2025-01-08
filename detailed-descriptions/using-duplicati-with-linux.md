---
description: This page describes how to use Duplicati with Linux
---

# Using Duplicati with Linux

To use Duplicati on Linux, you first need to decide which kind of instance you want. You can use the following overview to decide:

* Home user, single desktop machine: [TrayIcon](../duplicati-programs/trayicon.md) or [Agent](../duplicati-programs/agent.md)
* Server backup or headless: [Server](../duplicati-programs/server.md), [CLI](../duplicati-programs/command-line-interface-cli.md) or [Agent](../duplicati-programs/agent.md)
* Multiple machines: [Server](../duplicati-programs/server.md), [CLI](../duplicati-programs/command-line-interface-cli.md) or [Agent](../duplicati-programs/agent.md)

Next step is checking what Linux distribution you are using. Duplicati supports running on most Linux distros, but does not yet support FreeBSD. &#x20;

If you are using a Debian-based operating system, such as Ubuntu or Mint, you can use the `.deb` package, and for RedHat-based operating system, such as Fedora or SUSE, you can use the `.rpm` packages. For  other operating systems you can use the zip package, or check if your package manager already carries Duplicati.

Finally you need to locate information on what CPU architecture you are using:

* x64: 64bit Intel or AMD based CPU. This is the most common CPU at this time.
* Arm64: 64bit ARM based CPU. Used in Raspberry Pi Model 4 and some Laptops and Servers.
* Arm7: 32bit ARM based CPU. Used in Raspberry Pi Model 3 and older, and some NAS devices.

## Installing the package

Once you have decided the on (type, distro, cpu) combination you are ready to download the package. The full list of packages can be obtained via the main [download page](https://duplicati.com/download), and then clicking "Other versions". Refer to the [installation page](../getting-started/installation.md) for details on how to install the packages, or simply use the package manager in your system.

## Using the TrayIcon

For users with a desktop environment and no special requirements, the [TrayIcon](../duplicati-programs/trayicon.md) instance is the recommended way to run Duplicati. If you are using either `.deb` or `.rpm` you should see Duplicati in the program menu, and you can launch it from there. If you do not see Duplicati in the program menu, you can start it with:

```
duplicati
```

When running the TrayIcon in a user context, it will create a folder in your home folder, typically `~/.config/Duplicati` where it stores the local databases and the Server database with the backup configurations.

## Using the Server

The [Server](../duplicati-programs/server.md) is a regular executable and can simply be invoked with:

```
duplicati-server
```

When invoked as a regular user, it will use the same folder, `~/.config/Duplicati`, as the [TrayIcon](../duplicati-programs/trayicon.md) and share the configuration.

Besides the configuration listed below, it is also possible to run [Duplicati in Docker](using-duplicati-from-docker.md).

### Using Server as a Service

If you would like to run the Server as a service the `.rpm` and `.deb` packages includes a regular systemd service. If you are installing from the `.zip` package, you can grab the [service file from the source code](https://github.com/duplicati/duplicati/tree/master/ReleaseBuilder/Resources/debian/systemd) and install it manually on your system.

If you need to pass options to the server, edit the settings file, usually at `/etc/default/duplicati`.  Make sure you only edit the configuration file and not the service file as it will be overwritten when a new version is installed. The settings file should look something like this:

```
# Defaults for duplicati initscript
# sourced by /etc/init.d/duplicati
# installed at /etc/default/duplicati by the maintainer scripts

#
# This is a POSIX shell fragment
#

# Additional options that are passed to the Daemon.
DAEMON_OPTS=""
```

You can use `DAEMON_OPTS` to pass arguments to `duplicati-server`, such as `--webservice-password=<passsword>`.

To enable the service to auto-start, reload configurations, start the service and report the status, run the following commands:

```sh
sudo systemctl enable duplicati.service
sudo systemctl daemon-reload
sudo systemctl start duplicati.service  
sudo systemctl status duplicati.service
```

The server is now running and will automatically start when you restart the machine.&#x20;

**Note:** the service runs in the `root` user context, so files will be stored in `/root/.config/Duplicati` on most systems, but in `/Duplicati` on other systems. Use the `DAEMON_OPTS` to add `--server-datafolder=<path to storage folder>` if you want a specific location.

To check the logs (and possibly obtain a signin link), the following command can usually be used:

```sh
sudo journalctl --unit=duplicati
```

## Using the Agent

With the [Agent](../duplicati-programs/agent.md) there is a minimal setup required, which is to register the machine with the Duplicati Console. When installing either the `.rpm` or `.deb` packages, it will automatically register the `duplicati-agent.service` for startup. If you are using the `.zip` installation, you can find the [agent service in the source code](https://github.com/duplicati/duplicati/tree/master/ReleaseBuilder/Resources/debian/systemd) and manually register it:

```sh
sudo systemctl enable duplicati-agent.service
sudo systemctl start duplicati-agent.service 
```

When the Agent starts, it will emit a registration link to the log, and you can usually see it with the following command:

```sh
sudo journalctl --unit=duplicati
```

If you are using a [pre-authenticated link](../duplicati-programs/agent.md#registering-the-machine), you can run the following command to activate the registration:

```sh
duplicati-agent run \ 
  --agent-registration-url="<pre-authorized url>" \
  --agent-register-only
```

After registration is complete, restart the service to pick up the new credentials:

```sh
sudo systemctl restart duplicati-agent
```

## Using the CLI

Using the CLI is simply a matter of invoking the binary:

```sh
duplicati-cli help
```

Since the CLI also needs a local database for each backup, it will use the same location as [described for the Server](using-duplicati-with-linux.md#using-the-server) above to place databases. In addition to this, it will keep a small file called `dbconfig.json` in the storage folder where it maps URLs to databases. The intention of this is to avoid manually specifying the `--dbpath` parameter on every invocation.

## Using the support programs

Each package of Duplicati contains a number of support utilities, such as the [RecoveryTool](../duplicati-programs/command-line-interface-cli-1/recoverytool.md). Each of these can be invoked from the commandline with a `duplicati-*` name and all contain built-in help. For example, to invoke [ServerUtil](../duplicati-programs/command-line-interface-cli-1/serverutil.md), run:

```
duplicati-server-util help
```
