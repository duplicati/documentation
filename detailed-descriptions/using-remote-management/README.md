---
description: >-
  This page describes how to configure Duplicati to connect to the Duplicati
  Console and manage the backups from within the console.
---

# Using remote management

{% hint style="info" %}
## This page is for setting up remote management with a TrayIcon or Server installation. For Agent based installations, see the subpage on [Agent remote mangement](using-remote-control-with-agent.md)
{% endhint %}

## Register the local installation

In a default installation, Duplicati will serve up a UI using an internal webserver. This setup works well for workstations and laptops but can be challenging when the machine is not always connected to a display. To securely connect the instance to the Duplicati Console, go to the settings page and find the "Remote access control" section.

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-25 at 14.27.59.png" alt="" width="375"><figcaption><p>The remote control setup step</p></figcaption></figure>

Click the button "Register for remote control" to start the registration process. After a short wait, the machine will obtain a registration link:

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-25 at 14.29.23.png" alt="" width="375"><figcaption><p>Machine ready to be enrolled</p></figcaption></figure>

## Registering on the Console

Click the registration link to open a browser and claim the machine in the Duplicati Console:

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-25 at 14.31.21.png" alt="" width="375"><figcaption><p>Registrering the machine</p></figcaption></figure>

Click "Register machine" to add it to your account, then return to the Duplicati Settings page where the machine is now registered and ready to connect:

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-25 at 14.34.06.png" alt="" width="375"><figcaption><p>Machine is registered and ready to connect</p></figcaption></figure>

Click the "Enable remote control" button and see the machine is now connected to the Duplicati Console:

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-25 at 14.35.57.png" alt="" width="375"><figcaption><p>Machine is connected</p></figcaption></figure>

## Connecting to the machine

Now that the machine is connected to the Duplicati Console, return to the [Duplicati Console and visit Settings -> Registered Machines](https://app.duplicati.com/app/settings/registered-machines):

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-25 at 14.39.05.png" alt="" width="375"><figcaption><p>Machine is listed in Console</p></figcaption></figure>

You can now click "Connect" to access the machine directly from the portal!
