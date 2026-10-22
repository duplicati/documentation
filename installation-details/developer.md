---
description: Instructions to build from source
---

# Developer

Duplicati is an open-source backup software that provides encrypted, incremental backups for files and folders to cloud storage services or local drives - while pre-built binaries are available for all major platforms, you can also build it from source following these instructions.

```
git clone https://github.com/duplicati/duplicati
dotnet build Duplicati.slnx
```

The build artifacts & executables of other components, such as the [Tray Icon](../duplicati-programs/trayicon.md), [Server](../duplicati-programs/server.md), [CLI utilities](../duplicati-programs/command-line-interface-cli-1/) will be located under `Executables/<Project>/bin/Debug/net10.0/` (for example `Executables/Duplicati.GUI.TrayIcon/bin/Debug/net10.0/`).

To run the tray icon:

```
dotnet run --project Executables/Duplicati.GUI.TrayIcon/Duplicati.GUI.TrayIcon.csproj
```

Optionally to run all Unit tests:

```
dotnet test Duplicati.slnx
```
