---
description: This page describes how to use the remote agent to connect with remote control
---

# Using remote control with agent

The [Agent](../../duplicati-programs/agent.md) is designed to be deployed in a way that is more secure and easier to manage at scale than the regular [TrayIcon](../../duplicati-programs/trayicon.md) or [Server](../../duplicati-programs/server.md) instances. When the agent is running, it does not have any way to interact with it from the local machine.

On the very first run, the Agent will attempt to register itself with the Duplicati Console. If there is a desktop environment and a browser on the system, the Agent will attempt to open this with [the registration link](./#registering-on-the-console). In case there is no such option, the Agent will print out the link in the console or Event Viewer on Windows. The Agent will repeatedly poll the Console to find out when it is claimed.

As long as the Agent is not registered, restarting it will make it attempt to connect again.

Once the agent is registered, it immediately enables the connection and will be listed as a registered machine in the [portal settings](https://app.duplicati.com/app/settings/registered-machines).

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-25 at 14.39.05.png" alt="" width="375"><figcaption><p>The machine is registered</p></figcaption></figure>

## Simplified registration

To skip the registration step and have the agent connect directly to the console without any user intervention, it is required to first create a link that is pre-authorized on the Console. To do this head to the [portal settings](https://app.duplicati.com/app/settings/registered-machines) and click the "Add registration url" button.

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-25 at 14.55.30.png" alt="" width="375"><figcaption><p>Added a registration URL</p></figcaption></figure>

Any machine can now use this pre-authorized url to add machines to your organization in the Console. You can click the "Copy" button to get the link to your clipboard and paste it in when registering a machine. Do not share this link with anyone as it could allow them to add machines to your account.&#x20;

To revoke a link, simply delete it from within the portal. This will prevent new machines from registering, but existing registered machines will remain there.

With the registration link, start the Agent with a commandline such as:

```sh
duplicati-agent --registration-url=<copied-url>
```

This will cause the Agent to immediately show up in the Console. Future invocations of the agent will not require the registration url, but should the Agent somehow be de-registered, it will re-reregister if the url is set and the link is still valid.
