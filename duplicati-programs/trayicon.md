---
description: This page describes the Duplicati TrayIcon executable
---

# TrayIcon

The main application in the Duplicati installation is the TrayIcon program, called `Duplicati.GUI.TrayIcon.exe` on Windows and simply `duplicati` on Linux and MacOS.

The TrayIcon executable is a fairly small program that has as the primary task to register with the operating system desktop environment, and place a status icon in the desktop tray, menu, or statusbar.

The TrayIcon is connected to the server and will change the displayed icon based on the server state. Opening the associated context menu, provides the option to quite, pause/resume, or open the UI.

<div><figure><img src="../.gitbook/assets/Screenshot 2024-11-28 at 13.44.56.png" alt="" width="157"><figcaption><p>TrayIcon on Windows</p></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2024-11-28 at 13.49.11.png" alt="" width="186"><figcaption><p>Status icon on Ubuntu</p></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2024-11-28 at 12.18.52.png" alt="" width="129"><figcaption><p>Statusbar icon on MacOS</p></figcaption></figure></div>

The second task the TrayIcon is usually responsible for, is to host the [Server component](server.md). The server is responsible for handling stored backup configurations, provide a user interface, run scheduled tasks and more. When launching the TrayIcon, it will also transparently launch and host the server. It uses this hosted instance to subscribe to changes, so it can change the icon and signal the server state.

## Detached TrayIcon

In some cases it may be useful to run the server in one process and the TrayIcon in another. For this setup, the TrayIcon can run without a hosted server. Start the TrayIcon application with the commandline option `--no-hosted-server`, and the TrayIcon will instead try to connect to the Server. If the Server is not running on the same machine, or using a different port, this can be specified with the commandline option `--hosturl`.

It may also be required to provide the password for the server in the detatched setup, as outlined in [Duplicati Access Password](../detailed-descriptions/duplicati-access-password.md).

It may be convienient to [use preload settings](../detailed-descriptions/preload-settings.md) to provide arguments to both the Server and TrayIcon when running in detached mode.
