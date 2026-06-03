---
description: The page describes the Service and WindowsService programs
---

# Service and WindowsService

The Service binary executable is a small helper program that simply runs the [Server](server.md) executable and restarts it if it exits. The purpose of this program is to assist in keeping the Server running, even in the face of errors. The Service binary is called `Duplicati.Service.exe` on Windows and `duplicati-service` on Linux and MacOS.

## WindowsService

The `Duplicati.WindowsService.exe` executable only exists for Windows and serves two purposes: to manage the Windows Service registration and running the server as a Windows Service.

### Install service

The registration of the Windows Service is done by executing the WindowsService binary:

```
Duplicati.WindowsService.exe INSTALL [arguments ...]
```

The arguments can be any of the arguments supported by the Server and will be passed on to the Server on startup. The service will be registered to automatically restart and start at login. These details can be changed from the Windows service manager.

From version 2.1.1.0 and forward, the service will automatically start after installation. The command can be changed to `INSTALL-ONLY` to avoid starting the service:

{% code overflow="wrap" %}
```
Duplicati.WindowsService.exe INSTALL-ONLY [arguments ...]
```
{% endcode %}

### Uninstall service

To remove the service, use the the `UNINSTALL` command:

```
Duplicati.WindowsService.exe UNINSTALL
```

### Manual installation considerations

{% hint style="danger" %}
Do not mix using the MSI service installation with manual service installation.&#x20;
{% endhint %}

The MSI keeps track of what it has changed on the system, and this might break if you any of the commands on this page. If you want to manually handle the service, use the MSI without asking it to install the service or handle certificates. When used this way, the MSI is similar to an xcopy-install.

If you are manually handling the service, you can also use the `.zip` package and avoid using the MSI installation step entirely.

### Agent management

By default, the agent is installed as a service, but it is also possible to manually handle the installation and uninstallation similar to how the regular service is done:

{% code overflow="wrap" %}
```
Duplicati.WindowsService.exe AGENT-INSTALL [arguments ...]
Duplicati.WindowsService.exe AGENT-INSTALL-ONLY [arguments ...]
Duplicati.WindowsService.exe AGENT-UNINSTALL [arguments ...]
```
{% endcode %}

### Manage TLS (https) certificates

{% hint style="info" %}
Avaliable from Canary 2.3.0.103
{% endhint %}

The [ConfigureTool](command-line-interface-cli-1/configuretool.md) can be used to install TLS certificates such that Duplicati will use https for the webservice part. But using ConfgureTool with a service can be a bit difficult because it needs to run in the service context and Windows does not provide simple tools for doing this. To remedy this, the `WindowsService.exe` can also be used to create and install the certificates:

{% code overflow="wrap" %}
```
Duplicati.WindowsService.exe INSTALL-CERTS
```
{% endcode %}

This will restart the service with instructions to invoke the ConfigureTool in the service context and then install a system-wide TLS certificate. As explained in the [ConfigureTool](command-line-interface-cli-1/configuretool.md) page, this will also enable automatic certificate renewal and rotation.

{% hint style="warning" %}
When issuing the add or remove command, the service will be restarted to apply the changes
{% endhint %}

To remove the certificate again, run:

{% code overflow="wrap" %}
```
Duplicati.WindowsService.exe REMOVE-CERTS
```
{% endcode %}

Note that both commands are safe to re-run and will not do anything if the setup is already correct (they are idempotent commands).

### Password management

{% hint style="info" %}
Available from Canary 2.3.0.103
{% endhint %}

There are multiple ways to get Duplicati to change the access password, but for a Windows Service this requires multiple steps to avoid logging the password. For this reason, the `WindowsService.exe` provides a helper command that allows you to reset the password with a single command:

{% code overflow="wrap" %}
```
Duplicati.WindowsService.exe RESET-PASSWORD
```
{% endcode %}

{% hint style="warning" %}
When using this method to reset the password, the service will restart
{% endhint %}

The command will prompt you for a new password. If you leave the new password blank, `WindowsService.exe` will generate a random password for you and print it to the console before proceeding.

### Scripting support

{% hint style="info" %}
Available from Canary 2.3.0.103
{% endhint %}

To support automated installation setups, the `WindowsService.exe` tool also supports setting an initial password. This is similar to `RESET-PASSWORD` as described above, but will only set the password if a password has not already been set. This avoids having a random unknown password, but avoids updating the password if it has already been set somehow. To avoid leaking the password via the commandline, the inital password must be passed as an environment variable:

{% code overflow="wrap" %}
```
SET DUPLICATI__INIT_PASSWORD=<initial-password>
Duplicati.WindowsService.exe SET-INIT-PASSWORD
```
{% endcode %}

{% hint style="warning" %}
The service must be (re-)started after running the SET-INIT-PASSWORD command.&#x20;
{% endhint %}

The command can be executed before installing the service and will be applied once the service starts.&#x20;

The communication works through a registry key that is intentionally locked down to only be accesible by the Administrator and Service accounts. If the key has too wide permissions, the service will refuse to read it and log a warning. If you need to ensure the key has the correct permissions you can use the `LOCK-SERVICE-KEY` command:

{% code overflow="wrap" %}
```
Duplicati.WindowsService.exe LOCK-SERVICE-KEY
```
{% endcode %}

Issuing this command will delete the key, then recreate it with the correct permissions. After this, your script can write values to the key if it has sufficient permissions.&#x20;

All commands that use the service key will automatically recreate the key before applying their own values, so this command is only needed for advanced scripting based installs.
