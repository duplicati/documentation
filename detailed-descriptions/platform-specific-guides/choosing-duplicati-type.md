---
description: This page describes the different ways to run Duplicati
---

# Choosing Duplicati Type

When using Duplicati, you need to decide on what type of instance you want to use. Duplicati is designed to be flexible and work with many different setups, but generally you can use this overview to decide what is best for you:

* Home user, single desktop machine: [TrayIcon](../../duplicati-programs/trayicon.md) or [Agent](../../duplicati-programs/agent.md)
* Server backup or headless: [Server](../../duplicati-programs/server.md), [CLI](../../duplicati-programs/command-line-interface-cli.md) or [Agent](../../duplicati-programs/agent.md)
* Multiple machines: [Server](../../duplicati-programs/server.md), [CLI](../../duplicati-programs/command-line-interface-cli.md) or [Agent](../../duplicati-programs/agent.md)

## The TrayIcon

The [TrayIcon](../../duplicati-programs/trayicon.md) is meant to be the simplest way to run Duplicati with the minimal amount of effort required. The TrayIcon starts as a single process, registers with the machine desktop environment and shows a small icon in the system status bar (usually to the right, either top or bottom of the screen).

When running, the TrayIcon gives a visual indication of the current status, and provides access to the visual user interface by opening a browser window.

## The Server

The [Server](../../duplicati-programs/server.md) mode is intended for users who want to run the full Duplicati with a user interface, but without a desktop connection. When running the Server it is usually running as a system service so it has elevated privileges and is started automatically with the system.

When running the server it will emit log messages to the system log and it will expose a web server that can be accessed via a browser. **Beware** that if you are running the Server as root/Administrator you are also running a web server with the same privileges that you need to protect.

When the Server is running it will lock down access to only listen on the loopback adapter and refuse connections not using an IP address as the hostname. If you need to access the Server from another machine, make sure you protect it and [enable remote access](../../duplicati-programs/server.md#external-access-to-the-server) and also add [HTTPS protection](../../duplicati-programs/server.md#ssl-tls-support).

When running the Server you also need to [configure a password](../configuration-and-management/duplicati-access-password.md), either by getting a [signing token from the logs](../configuration-and-management/duplicati-access-password.md#temporary-signin-token), [changing the password](../configuration-and-management/duplicati-access-password.md#change-password-with-serverutil), or [setting one explicitly](../../duplicati-programs/server.md#configuring-the-server-password).

## The Agent

The [Agent](../../duplicati-programs/agent.md) mode is intended for users who wants to run Duplicati with remote access through the [Duplicati Console](/broken/pages/mwwrECckpb1xj3XLM1fk). The benefit from this is that you do not need to provide any local access as all access is protected with HTTPS and additional channel encryption from the Agent to the browser you are using.

{% hint style="info" %}
The Agent mode is only available on the Enterprise plan
{% endhint %}

If you have multiple machines to manage, using the console enables you to access all the backups, settings, logs, controls, etc. from one place.

## The Command Line Interface (CLI)

The [CLI](../../duplicati-programs/command-line-interface-cli.md) mode is intended for advanced users who prefer to manage and configure each of the backups manually. The typical use for this is a server-like setup where the backups are running as cron scheduled tasks or triggered with some external tool.

## Mixing types

For some additional flexibility in configurations it is also possible to combine the different types in some ways.

### Combining Server and TrayIcon

It the server is used primarily to elevate privileges, it is possible to have the TrayIcon run in the local user desktop and connect to an already running Server. To do this, change the TrayIcon commandline and add additional arguments:

```
duplicati --no-hosted-server 
  --hosturl=http://localhost:8200 
  --webservice-password=<password>
```

The `--no-hosted-server` argument disables launching another (competing) server, and the two other arguments will give information on how to reach the running server.

### Triggering Server jobs externally

If you prefer to use the Server (or TrayIcon) but would like to trigger the backups with an external scheduler or event system, you can use the [ServerUtil](../../duplicati-programs/command-line-interface-cli-1/serverutil.md) to trigger a backup or pause/resume the server.

### Using the CLI for on Server backups

If you are using the Server (or TrayIcon) but you want to run a command that is not in the UI, it is possible to use the CLI to run commands on the backups defined in the Server. Note that the Server and CLI use different ways of keeping track of the [local database](../../database-and-storage/the-local-database.md), so you need to obtain the storage destination url and the database path from the Server and then run the CLI.

