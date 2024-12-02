---
description: This page describes the Duplicati server component
---

# Server

The Duplicati server is the primary instance, and is usually hosted by the [TrayIcon](trayicon.md) in desktop environments. The server itself is intended to be a long-running process, usually running as a service-like process that starts automatically. The binary executable is called `Duplicati.Server.exe` on Windows and `duplicati-server` on Linux and MacOS.

The server is responsible for saving backup configurations, starting scheduled backups, and provide the user interface. The user interface is provided by hosting a webserver inside the process. This webserver provides both the static files as well as the API that is needed to control the server.

When the server runs any operation, such as a backup or restore, it will configure an environment from various settings, primarily the backup configuration. The actual implementation is the same code that is executed by the [command line interface](command-line-interface-cli.md), but runs within the server process.

Unlike the command line interface, the Server keeps track of the local database to ensure the database is present for all operations. This is possible because the server has additional state in the [server database](../detailed-descriptions/the-server-database.md) and the path to the local database is kept there.

During the operation, the server will report progress and log messages, which can be viewed if a client is attached during the run. After the run, the Server will record metadata and log data in the database, to assist in troubleshooting later.

## Configuring the server password

As described in the [access password](../detailed-descriptions/duplicati-access-password.md) section, it is possible to set or reset the server password by starting the server with the option:

```
--webservice-password=<new password>
```

This new password is stored in the [server database](../detailed-descriptions/the-server-database.md) and does not need to be supplied on future launches. Note that changing the password does not invalidate [tokens](../technical-details/server-authentication-model.md) that are already issued. To clear any issued tokens, which should be done if there is a suspicion that the signing keys are leaked, start with the following option:

```
--webservice-reset-jwt-config=true
```

This will generate new [token signing keys](../technical-details/server-authentication-model.md) and immediately invalidate any previously issued tokens. You can start the server with this parameter on each launch if you do not rely on a refresh token stored in the browser.

It is also possible to disable the use of signin tokens, which are used in some cases in favor of requiring the password. This can be set with the option:

```
--webservice-disable-signin-tokens=true
```

## Configuring the server encryption

Since the [server database is a critical resource to protect](../detailed-descriptions/the-server-database.md), it is possible to set a field-level encryption password:

```
--settings-encryption-key<encryption key>
```

If the server starts without a settings encryption key, it will emit a warning in the logs explaining the problem. If any fields are already encrypted, Duplicati will refuse to start without the encryption key. If no fields are encrypted, but an encryption key is supplied, the fields will be encrypted.

If you need to remove the encryption key for some reason, provide the key as above, and additionally supply the option:

```
--disable-db-encryption=true
```

If this flag is supplied, Duplicati will not emit a warning that the database is not encrypted. If the database was encrypted, it will be decrypted. After the database is decrypted, it can be re-encrypted with a different password.

To prevent ever starting the Server without an encryption key, provide the option:

```
--require-db-encryption-key
```

Note that this is exclusive with `--disable-db-encryption` and that the server will not start if the fields are encrypted and no encryption key is provided.

## External access to the server

The server will by default only listen to requests on the local machine., which is done to ensure that requests from the local network cannot access the Duplicati instance. However, any applications that are running on the same machine will be able to send commands to Duplicati. To prevent local privilege escalation attacks, Duplicati requires a [password](../detailed-descriptions/duplicati-access-password.md) and a [valid token](../technical-details/server-authentication-model.md) for all requests.

To activate access from the local network, the server must be started with:

```
--webservice-interface=any
```

It is also possible to specify `loopback` (the default value) or the IP address to listen on.

When accessing the server from an external machine, it will only respond to requests that use an IP address as the hostname. This security mechanism is meant to combat fast-flux DNS attacks that could expose the local API to a website. If you need to access Duplicati from an external machine, you need to explicitly allow the hostname(s) that you will be using, by starting the server with:

```
--webservice-allowed-hostnames=<hostname>
```

Multiple hostnames can be supplied with semicolons: `host1;host2.example.com;host3`.

The server will attempt to use port `8200` and terminate if that port is not available. Use the commandline option to set a specific port:

```
--webservice-port=<port number>
```

## SSL/TLS support

To ensure all communication is secure, Duplicati supports adding a TLS certificate. The certificate can be a self-signed certificate, but in this case the browser will not accept it, and extra tweaks must be made.

To create a trusted certificate, it is easiest to use one of the many tools to manage it, such as [mkcert](https://github.com/FiloSottile/mkcert). which can generate the various components and configure your system to trust these certificates. Beware that this requires good operational security, as the generated certificate authority can issue certificates for **ANY** website, including ones you do not own, and eavesdrop on your traffic.

Once you have the desired certificate, in `.pfx` aka `.p12` format, you can provide it to the Server on startup:

```
--webservice-sslcertificatefile=<path to certificate file>
--webservice-sslcertificatepassword=<password to ssl file>
```

After starting the server with an SSL certificate, the certificate is stored in the [server database](../detailed-descriptions/the-server-database.md) with a randomly generated password. Any subsequent launches of the server will then use the certificate and the server will only communicate over https.

To change the certificate, exit all running instances, then run again once with the new certificate path, as shown above, and the internally stored certificate will be replaced.

If you need to revert to unencrypted http communication, you can use the option:

```
--webservice-remove-sslcertificate=true
```

It is also possible to temporarily disable the use of the certificate, without removing it, with:

```
--webservice-disable-https
```

## Serving a different UI

If you are developing a new UI for Duplicati, or prefer to use a customized UI, it is possible to configure the server to serve another UI, or none at all. If you want to use the Server component and only manipulate it with another tool, such as the [ServerUtil](command-line-interface-cli-1/serverutil.md), start with this option:

```
--webservice-api-only=true
```

This option will fully disable the serving of static files and only leave the API available.&#x20;

If instead, you would like to serve a different folder, you can use the option to set the webroot:

```
--webservice-webroot=<path-to-webroot>
```

To better support SPA type applications, the Server can be started with:

```
--webservice-spa-paths=<path to SPA>
```

For the SPA enabled path, any attempt to access a non-existing page will serve the `index.html` file, which can then render the appropriate view. Multiple paths can be supplied with semicolons.

## Timezone

Internally, all time operations are recorded in UTC to avoid issues with daylight savings and changes caused by changing the machine timezone. The only difference to this rule is the scheduler, which is timezone aware.

The scheduler needs to be timezone aware so scheduled backups run at the same local time, even during daylight savings time. On the initial startup, the system timezone is detected and stored in the server database. It is possible to change the timezone from the UI, but it can also be set with the commandline option:

```
--webservice-timezone=<timezone>
```

## Configuring logging

Duplicati will log various messages to the server database, but it is possible to also log these messages to a log file for better integration with monitoring tools or manual inspection. To configure file-based logging, provide the two options:

```
--log-file=<path to logfile>
--log-level=<loglevel>
```

By default, the `--log-level` parameter is set to only log warnings, but can be configured to any of the log levels: `Error`, `Warning`, `Information`, `Verbose`, and `Profiling`.

The log data that is stored in the database is by default kept for 30 days, but this period can be defined with the option:

```
--log-retention=<time to keep logs>
```

On Windows, it is also possible to log data to the Windows Eventlog. To activate this, set the options:

```
--windows-eventlog=true
--windows-eventlog-level=<loglevel>
```

## Storing data in different places

By default, Duplicati will use the location that is recommended by the operating system to store "Application Support Files" or "Application Data":

* Windows: `%LOCALAPPDATA%\Duplicati`
* Linux: `~/.config/Duplicati`
* MacOS: `~/Library/Application Support`

These paths are sensitive to the user context, meaning that the actual paths will change based on the user that is running the Server. This is especially important when running the server with elevated privileges, because this usually causes it to run in a different user context, resulting in different paths.

To force a specific folder to be used, set the option:

```
--server-datafolder=<path to storage folder>
```

This can also be supplied with the environment variable:

```
DUPLICATI_HOME=<path to storage folder>
```

If both are supplied, the commandline options are used.

## Environment variables

For the server options, it is also possible to supply them as environment variables. This makes it easier to toggle options from Docker-like setups where is is desirable to have then entire service config in a single file, and setting commandline arguments may be error prone.

Any of the commandline options for the server an be applied by transforming the option name to an environment variable name. The transformation is to upper-case the option, change hyphen, `-`, to underscore, `_`, and prepend `DUPLICATI__`.&#x20;

For example, to set the commandline option `--webservice-api-only=true` with an environment variable:

```
DUPLICATI__WEBSERVICE_API_ONLY=true
```

Any arguments supplied on the commandline will take precedence over an environment variable, as commandline arguments are considered more "local".
