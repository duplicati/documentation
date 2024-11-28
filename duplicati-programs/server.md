---
description: This page describes the Duplicati server component
---

# Server

The Duplicati server is the primary instance, and is usually hosted by the [TrayIcon](trayicon.md) in desktop environments. The server itself is intended to be a long-running process, usually running as a service-like process that starts automatically.

The server is responsible for saving backup configurations, starting scheduled backups, and provide the user interface. The user interface is provided by hosting a webserver inside the process. This webserver provides both the static files as well as the API that is needed to control the server.

The server will by default only listen to requests on the local machine., which is done to ensure that requests from the local network cannot access the Duplicati instance. However, any applications that are running on the same machine will be able to send commands to Duplicati. To prevent local privilege escalation attacks, Duplicati requires a [password](../detailed-descriptions/duplicati-access-password.md) and a [valid token](../technical-details/server-authentication-model.md) for all requests.

