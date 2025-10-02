---
description: This page describes the Agent executable
---

# Agent

The Duplicati Agent is one of the primary ways to run Duplicati, similar to the [Server](server.md) and [TrayIcon](trayicon.md). The Agent can be deployed in settings where there is no desktop or user interaction is not desired. The Agent needs to connect to a remote control destination from wher it can be controlled, and due to this, the Agent employs a number of additional settings that prevents applications from running on the same machine to interact with the Agent.

A benefit from using the Agent is that it will only communicate over TLS encrypted connections and does not require you to manually handle the configuration of [certificates for the Server](server.md#ssl-tls-support).

The Agent binary is called `Duplicati.Agent.exe` on Windows and `duplicati-agent` on Linux and MacOS.

## Registering the machine

When the Agent starts for the first time, it will attempt to register with the Duplicati Console. To do this, it will open a browser window where the user can accept the registration and add the machine to their account. If the Agent needs to be registered without user interaction, a pre-authorized link can be generated on the [Duplicati Console registration page](https://app.duplicati.com/app/settings/registered-machines):

<figure><img src="../.gitbook/assets/Screenshot 2024-12-03 at 15.26.17.png" alt=""><figcaption><p>The Duplicati Console with a pre-authorized link</p></figcaption></figure>

To register the Agent, run the following command:

```sh
duplicati-agent run \ 
  --agent-registration-url="<pre-authorized url>" \
  --agent-register-only
```

This will cause the Agent to register using the token from the url and the `--agent-register-only` option will cause it to exit after registration has completed. If the Agent is already registered, it will simply exit.

To remove the registration information, use the command:

```
duplicati-agent clear
```

After the settings are cleared, the agent can be registered again.

The Agent settings are stored in a file called `agent.json` in the same folder where the [Server database](../detailed-descriptions/the-server-database.md) is stored. The file path can be supplied with `--agent-settings-file` and the file can be encrypted with the setting `--agent-settings-file-passphrase`.

To protect the settings file passphrase, it is possible to use the [secret provider](../detailed-descriptions/using-the-secret-provider/).

## Configuring the hosted server

The Agent is not intended to be accessible locally and for that reason, it is locked down with a number of settings. If you need to configure the Server, most of the options can be given to the Agent and passed on to the server. This includes `--webservice-port` and `--settings-encryption-key`.

The hosted agent server will use the port `8210` by default, to not clash with the regular Duplicati instance on port `8200`.

## Opening the hosted server for local access

To make the hosted server fully accessible from the local machine that it is running on, add the following settings:

```
duplicati-agent \
  --disable-pre-shared-key \
  --webservice-api-only=false \
  --webservice-password=<password>
```

The first option, `--disable-pre-shared-key`, will disable the random key that is required for all requests to the webserver. This key is a random value that is generated on each start, and only kept in memory, preventing any requests to the Duplicati API.

The second option, `--webservice-api-only=false` will enable the access to the static `.html`, `.css`, and `.js` files that provide the UI.

The last option sets the UI password, which would otherwise be a randomly generated password.

You may also want to re-enable the signin tokens with `--webservice-disable-signin-tokens=false`.

## WindowsService support

The `Duplicati.WindowsService.exe` installer can also install the Agent as a service:

```
Duplicati.WindowsService.exe INSTALL-AGENT <options>
```

Note that since the Agent cannot open a browser from within the service context, it will instead emit the link that is used to claim the Agent in the Windows Event Log. You need to find the link from there and open it in a browser to claim the machine. Alternatively, use the [method outlined above to register the machine](agent.md#registering-the-machine), but beware that you need to run in the same context as the service, or the `agent.json` file will be placed in another folder.

{% hint style="info" %}
In Duplicati Stable 2.1.0.5, you need to add the "`run`" argument to the install commandline:

```
Duplicati.WindowsService.exe INSTALL-AGENT run <options>
```

This has been fixed in later releases
{% endhint %}

Similarly, you can uninstall the Agent service with:

```
Duplicati.WindowsService.exe UNINSTALL-AGENT
```

## Linux service

On Linux-based installations, the Agent installer will create the service files, which can be used to automatically start and run the Agent:

```
sudo systemctl enable duplicati-agent.service
sudo systemctl daemon-reload
sudo systemctl start duplicati-agent.service  
sudo systemctl status duplicati-agent.service
```

As is common for other services, additional start parameters can be added to `/etc/default/duplicati`.

Note that when running the service, the Agent does not have access to the desktop environment (if one even exists) and it cannot open the registration url in the browser. Instead, it will emit a url in the system logs that you need to open to register the machine. Alternatively, use the [method outlined above to register the machine](agent.md#registering-the-machine), but beware that you need to run in the same context as the service, or the `agent.json` file will be placed in another folder.

## MacOS support

When installing on MacOS, the packages will register a launchagent that will start the Agent on each login. The assumption here is that the desktop context contains a browser, so the Agent will open the registration url in the default browser.

To use a pre-authenticated url, use the [method outlined above to register the machine](agent.md#registering-the-machine), and then restart the service to have it pick up the updated `agent.json` file.
