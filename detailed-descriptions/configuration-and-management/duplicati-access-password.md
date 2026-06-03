---
description: >-
  This page describes how the authentication is working with Duplicati and how
  to regain access if the password is lost or unknown
---

# Duplicati Access Password

If you are starting Duplicati for the first time, it will ask you to pick a password. Picking a strong password is important to ensure unwanted access to Duplicati from other processes on the system. By default, Duplicati has chosen a strong random password and it is recommended for most users to not change the random password. It is not possible to extract the current password in any way and it is not possible to disable the password.

## Access from the TrayIcon

The [TrayIcon](../../duplicati-programs/trayicon.md) process will usually host the [Server](../../duplicati-programs/server.md) that presents the UI. Since the two parts are within the same process they can communicate securely, and this setup enables the TrayIcon to negotiate a short-term signin token with the server, even though it does not know the password.

This mechanism works for most default installations and is secure as long as the desktop is not compromised. This signin process is the reason that the default random password is prefered, because it is not possible to leak the password.

The downside is that you can bookmark the Duplicati page, but you may be asked for a password that you do not know when accessing the page. In this case, re-launching from the TrayIcon will log you in again.

If you prefer, it is possible to choose the password so you can enter it when asked. Optionally, you can also choose to disable the feature that allows the TrayIcon to sign in without a password, through the settings page.

Login with the TrayIcon is shown here (click to choose operating system):

{% tabs %}
{% tab title="Windows" %}
<figure><img src="../../.gitbook/assets/OpenTrayIconWindows.gif" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="MacOS" %}
<figure><img src="../../.gitbook/assets/OpenTrayIconMacOS.gif" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Linux (Ubuntu)" %}


<figure><img src="../../.gitbook/assets/OpenTrayIconLinux.gif" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

## Temporary signin token

When Duplicati starts up with the randomly generated password it will attempt to emit a temporary sign-in url. If you run either the [TrayIcon](../../duplicati-programs/trayicon.md) or [Server](../../duplicati-programs/server.md) in a terminal, most systems will show the link here.

If you are running Duplicati as a service with no console attached, the link will end up in the system logs. On Windows you can use the [Event Viewer](https://learn.microsoft.com/en-us/shows/inside/event-viewer) utility to find the message with a sign-in url. For Linux you can view the system logs, usually:

```bash
sudo journalctl --unit=duplicati | less
```

Note that the regular output from `journalctl` is capped in width, so you cannot see the whole token. Pipe to a file or another program as shown above to get the full output.

For MacOS you can use the [Console app](https://support.apple.com/lt-lt/guide/console/welcome/mac).

Once you have obtained the link, simply click it or paste it into a browser. Note that the sign-in token has a short lifetime to prevent it being used to gain unathorized access from someone who obtains the logs. If the link has expired, simply restart the service or application and a new link will be generated.

After a password has been set, the link will no longer be generated.

## Change password with ServerUtil

If you are not using the TrayIcon or you have disabled the signin feature, but lost the password somehow, you can change the password with [ServerUtil](../../duplicati-programs/command-line-interface-cli-1/serverutil.md) in some cases.

This works by reading the same database as the server is using and extracting the keys used to sign a sign-in token, and then creating a sign-in token. This sign-in token works the same way as the TrayIcon's signin feature. Note that the password itself cannot be extracted from the database, it can only be verified.

After obtaining a sign-in token, ServerUtil can then change the password in the running instance.

This only works if:

* The database is readable from the process running ServerUtil
* The database field encryption password is available to the process running ServerUtil

If these constraints are satisfied, it is possible to reset the server password by running:

```shell
> duplicati-server-util change-password
```

If ServerUtil is launched in a similar environment (i.e., same user, same environment variables) this would allow access in most cases. There are a number of commandline options that can be used to guide ServerUtil in case the environments are not entirely the same.

For Linux user, you can usually use `su` or `sudo`to enter the correct user context, but some additional environment variables may be needed. The default location for the database is described in the [data location section](../duplicati-programs/server.md#storing-data-in-different-places), and a different location can be provided with `--server-datafolder.`

### Example change with a different context

If you need to change the password for a Windows Service instance running in the service context, you can use a command such as this:

```
Duplicati.CommandLine.ServerUtil change-password \ 
  --server-datafolder "C:\Windows\System32\config\systemprofile\AppData\Local\Duplicati"
```

Similarly, if the service is running as root on Linux:

```
duplicati-server-util change-password \
  --server-datafolder=/root/.config/Duplicati
```

## Change password from the Server

If the other options are not available, it is possible to restart the [Server](../../duplicati-programs/server.md) process and supply the commandline option:

```
--webservice-password=<new password>
```

This will write a hashed ([PBKDF](https://en.wikipedia.org/wiki/PBKDF2)) version of the new password to the database and use this going forward. This process requires restarting the server, but is persisted in the database, so it is only required to start the server once with with the `--webservice-password` option and future starts can be done without the password.

Since commandline arguments and environment variables can be viewed through various system tools, it is recommended that the option is not set on every launch. A prefered way to set this would be to stop all running instances, start once with the new password from a commandline terminal, shut down, and then start again normally.

The option can also be supplied to the [TrayIcon](../../duplicati-programs/trayicon.md) and [Agent](../../duplicati-programs/agent.md) processes, which will pass it on to their internal instance of the Server.

### Change the password for a service

To change the password for a running service, you can grab the [temporary signin token from the logs as explained above](duplicati-access-password.md#temporary-signin-token). If that approach is not possible, and the [ServerUtil approach](duplicati-access-password.md#change-password-with-serverutil) is not an option either, you can also instruct the service to change the password. This works by passing the `--webservice-password` command to the service, instructing it to reset the password. To avoid leaking the actual password, it is recommended that you choose a temporary password and  run the following steps (example for Windows):

```
Duplicati.WindowsService.exe UNINSTALL
Duplicati.WindowsService.exe INSTALL --webservice-password=<temporary-password>
Duplicati.WindowsService.exe UNINSTALL
Duplicati.WindowsService.exe INSTALL
```

After this sequence, you can now log in to Duplicati using the temporary password, and change it to a real password. It is recommended that you use a temporary password because there is a chance that the password given to `INSTALL` will be written to a log. By using a temporary password you ensure that the real password is never persisted.

Since the `UNINSTALL` and `INSTALL` steps will automatically stop/start the service, this sequence will make the service start once with the `--webservice-password=<temporary-password>` option, and then restart without the option.

If you only run the first two steps, the service will continue to reset the password on each startup, and your password will be present in the service commandline and possibly also captured in logs.

## Disable sign-in tokens

It is possible to disable the use of sign-in tokens completely, which can increase security further. This is done by passing the option:

```sh
--webservice-disable-signin-tokens=true
```

This will make the [Server](../../duplicati-programs/server.md) reject any sign-in tokens and prevent the access from the TrayIcon and ServerUtil without explicitly passing the password. With this option, it will require write access to the database to create a new token, but it will also require handling the password in a safe manner from all instances where this is needed.

This option can also be supplied to the [TrayIcon](../../duplicati-programs/trayicon.md) process and is default enabled by the [Agent](../../duplicati-programs/agent.md).
