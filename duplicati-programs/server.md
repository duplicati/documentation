---
description: This page describes the Duplicati server component
---

# Server

The Duplicati server is the primary instance, and is usually hosted by the [TrayIcon](trayicon.md) in desktop environments. The server itself is intended to be a long-running process, usually running as a service-like process that starts automatically. The binary executable is called `Duplicati.Server.exe` on Windows and `duplicati-server` on Linux and MacOS.

The server is responsible for saving backup configurations, starting scheduled backups, and provide the user interface. The user interface is provided by hosting a webserver inside the process. This webserver provides both the static files as well as the API that is needed to control the server.

The server will by default only listen to requests on the local machine., which is done to ensure that requests from the local network cannot access the Duplicati instance. However, any applications that are running on the same machine will be able to send commands to Duplicati. To prevent local privilege escalation attacks, Duplicati requires a [password](../detailed-descriptions/duplicati-access-password.md) and a [valid token](../technical-details/server-authentication-model.md) for all requests.

When the server runs any operation, such as a backup or restore, it will configure an environment from various settings, primarily the backup configuration. The actual implementation is the same code that is executed by the [command line interface](command-line-interface-cli.md), but runs within the server process.

Unlike the command line interface, the Server keeps track of the local database to ensure the database is present for all operations. This is possible because the server has additional state in the [server database](../detailed-descriptions/the-server-database.md) and the path to the local database is kept there.

During the operation, the server will report progress and log messages, which can be viewed if a client is attached during the run. After the run, the Server will record metadata and log data in the database, to assist&#x20;



