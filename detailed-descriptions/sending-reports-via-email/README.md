---
description: Describes how to send reports with Duplicati
---

# Sending reports

Duplicati strives to make it as easy as possible to [set up backups](../../getting-started/set-up-a-backup-in-the-ui.md), and using the built-in scheduler makes it easy to ensure that backups are running regularly. Because it is easy to set up a backup and forget about, it is possible to have a backup running with little interaction.

Despite all efforts to make Duplicati as robust as possible against failures, it is not possible to handle every possible problem that may arise after the initial setup. Common failure causes is revoked credentials, filled storage, missing provider updates, etc.

To avoid discovering too late that the backup had stopped working for some reason, it is highly recommended to set up automated monitoring of backups. Duplicati has a number of ways that you can use to send reports into a monitoring solution:

* [Duplicati Console](monitoring-with-duplicati-console.md)
* [Send emails](sending-reports-with-email.md)
* [Send Jabber/XMPP](sending-jabber-xmpp-notifications.md)
* [Send HTTP message](sending-http-notifications.md)
* [Send Telegram message](sending-telegram-notifications.md)
