---
description: Describes the how to configure sending notifications via Jabber/XMPP
---

# Sending Jabber/XMPP notifications

One of the supported notification methods in Duplicati is the open-source [XMPP protocol](https://en.wikipedia.org/wiki/XMPP), supported by a variety of projects, including commercial enterprise offerings.&#x20;

To send a notification via XMPP you need to supply one or more recipientes, an XMPP username and a password.&#x20;

In the UI you can configure these mandatory values as well as the optional values.

The basic options for sending XMPP notifications can be entered into the general settings, which will then apply to each backup. It is also possible to apply or change the settings for the individual backups by editing the advanced options. Here is how it looks when editing it in the user interface:

<div>

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-20 at 14.46.45.png" alt=""><figcaption><p>Set up XMPP notifications with the default options editor</p></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-20 at 14.46.35.png" alt=""><figcaption><p>Set up XMPP option with a text field</p></figcaption></figure>

</div>

You can toggle between the two views using the "Edit as list" and "Edit as text" links.

Besides the mandatory options, it is also possible to configure:

* The notification message and format
* Conditions on when to send emails
* Conditions on what log elements to include

For details on how to customize the notification message, see the [section on customizing message content](custom-message-content.md).
