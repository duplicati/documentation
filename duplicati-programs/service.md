---
description: The page describes the Service and WindowsService programs
---

# Service and WindowsService

The Service binary executable is a small helper program that simply runs the [Server](server.md) executable and restarts it if it exits. The purpose of this program is to assist in keeping the Server running, even in the face of errors. The Service binary is called `Duplicati.Service.exe` on Windows and `duplicati-service` on Linux and MacOS.

## WindowsService

The `Duplicati.WindowsService.exe` executable only exists for Windows and serves two purposes: to manage the Windows Service registration and running the server as a Windows Service.&#x20;

The registration of the Windows Service is done by executing the WindowsService binary:

```
Duplicati.WindowsService.exe INSTALL [arguments ...]
```

The arguments can be any of the arguments supported by the Server and will be passed on to the Server on startup. The service will be registered to automatically restart and start at login. These details can be changed from the Windows service manager.

From version 2.1.1.0 and forward, the service will automatically start after installation. The command can be changed to `INSTALL-ONLY` to avoid starting the service.

To remove the service, use the the `UNINSTALL` command:

```
Duplicati.WindowsService.exe UNINSTALL
```

