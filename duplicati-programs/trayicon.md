---
description: This page describes the Duplicati TrayIcon executable
---

# TrayIcon

The main application in the Duplicati installation is the TrayIcon program, called `Duplicati.GUI.TrayIcon.exe` on Windows and simply `duplicati` on Linux and MacOS.

The TrayIcon executable is a fairly small program that has as the primary task to register with the operating system desktop environment, and place a status icon in the desktop tray, menu, or statusbar.

The TrayIcon is connected to the server and will change the displayed icon based on the server state. Opening the associated context menu, provides the option to quite, pause/resume, or open the UI.

<div><figure><img src="../.gitbook/assets/Screenshot 2024-11-28 at 13.44.56.png" alt="" width="157"><figcaption><p>TrayIcon on Windows</p></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2024-11-28 at 13.49.11.png" alt="" width="186"><figcaption><p>Status icon on Ubuntu</p></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2024-11-28 at 12.18.52.png" alt="" width="129"><figcaption><p>Statusbar icon on MacOS</p></figcaption></figure></div>

The second task the TrayIcon is usually responsible for, is to host the [Server component](server.md). The server is responsible for handling stored backup configurations, provide a user interface, run scheduled tasks and more. When launching the TrayIcon, it will also transparently launch and host the server. It uses this hosted instance to subscribe to changes, so it can change the icon and signal the server state.

## Server port

By default, Duplicati uses the port `8200` as the communication port with hosted server. Should that port be taken, usually because another instance of Duplicati is running in another user context, Duplicati will automatically try other ports from the sequence: `8200`, `8300`, `8400`, `...`, `8900`.

Once an available port is found, this port is stored in the server database and attempted first on next run.

## Default browser

By default, the Duplicati TrayIcon will use the operating systems standard method for opening the system-default browser. If this is not desired, it is possible to choose the binary that will be used to launch the webpage with the option:

```
--browser-command=<path to binary>
```

## Detached TrayIcon

In some cases it may be useful to run the server in one process and the TrayIcon in another. For this setup, the TrayIcon can run without a hosted server. To disabled the Server, start the TrayIcon application with the commandline option:

```
 --no-hosted-server=true
```

This will cause the TrayIcon to connect to a Server that is already running. If the Server is not running on the same machine, or using a different port, this can be specified with the commandline option:

```
--hosturl=<host url>
```

It may also be required to provide the password for the server in the detatched setup, as outlined in [Duplicati Access Password](../detailed-descriptions/duplicati-access-password.md). An alternative to providing the password is to use the option:

```
--read-config-from-db=true
```

The TrayIcon will then attempt to extract signing information from the local database, provided that the TrayIcon process also has read access to the database, and that signin tokens are not disabled.

It may be convienient to [use preload settings](../detailed-descriptions/preload-settings.md) to provide arguments to both the Server and TrayIcon when running in detached mode.

### Self-signed certificate

If the server is using a self-signed certificate (or a certificate not trusted by the OS), the connection will fail. To manually allow a certificate, obtain the certificate hash, and provide it with:

```
--host-cert-hash=<hash>
```

When the TrayIcon is hosting the server, or has access to the database settings, it will automatically extract the certificate hash, so that particular certificate is accepted. This technique is secure and very  similar to [certificate pinning](https://en.wikipedia.org/wiki/HTTP_Public_Key_Pinning).&#x20;

For testing and debugging purposes, the certificate hash `*` means "any certificate". Beware that this settings is very insecure and should not be used in production settings.

## Server settings

When hosting the server, the TrayIcon also accepts all the server settings and will forward any commandline options to the hosted server when starting it.&#x20;

It is possible to run Duplicati in "portable mode" where it can run from removable media, such as an USB-stick, see the [server data location](server.md#storing-data-in-different-places) section for more details.
