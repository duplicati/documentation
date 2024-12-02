---
description: This page describes the AutoUpdater tool in Duplicati
---

# AutoUpdater

The AutoUpdater is intended to support automatic updating of Duplicati. In the current version, the name is a bit misleading as it only supports checking for a new version, it does not yet support actually installing a new version automatically.

The binary is called `Duplicati.CommandLine.AutoUpdater.exe` on Windows and `duplicati-autoupdater` on Linux and MacOS.

To use the AutoUpdater, simply invoke it from the commandline:

```
duplicati-autoupdater check
```

This will check if there is a newer version available and remote the running version number.

It is also possible to download an updated installer package:

```
duplicati-autoupdater download
```

The download feature checks what [package](../../installation-details/package-options.md) Duplicati is current installed with, and then obtains the most recent URL for that package and downloads it the the current directory. This feature only works if the installed package can be determined and there is an updated package available. If not, the download page is reported to the terminal for manual download.

## Environment variables

By default, Duplicati uses the domains `updates.duplicati.com` and `alt.updates.duplicati.com` to find updates. If you are running Duplicati within a controlled environment, you can use the environment variables to change where Duplicati is looking for the updates:

```
AUTOUPDATER_Duplicati_URLS=https://example.com/stable/latest.manifest
```

Duplicati will detect the `/stable/` part of the url and replace with the channel the user has chosen.

It is also possible to set the [channel](../../installation-details/release-channels-and-versions/) with an environment variable:

```
AUTOUPDATER_Duplicati_CHANNEL=canary
```
