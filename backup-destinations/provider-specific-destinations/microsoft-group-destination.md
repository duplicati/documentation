---
description: This page describes the Microsoft Group storage destination
---

# Microsoft Group Destination

Duplicati supports using [Microsoft Groups](https://learn.microsoft.com/en-us/microsoftteams/office-365-groups) as a storage destination.&#x20;

## User interface

<figure><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.39.05.png" alt="Microsoft Group destination configuration"><figcaption></figcaption></figure>

To configure the Microsoft Group destination you need to provide the group email. Then pick a unique folder name for the backups, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

## URL format for Commandline

To use the destination, use the following URL format:

```
msgroup://<folder>/<subfolder>
  ?authid=<authid>
  &group-id=<group-id>
```

To use MS Group you must first obtain an `AuthID` by using a Duplicati service to log in to Microsoft and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

You can either provide the group email via `--group-email` or the group id via `--group-id`. If you provide both, they must resolve to the same group id.

## Performance tuning options

If you need to gain more performance you can fine-tune the performance of chunked transfers with the options:

* `--fragment-size`
* `--fragment-retry-count`
* `--fragment-retry-delay`

For most uses, it is recommended that these are kept at their default settings and only changed after confirming that there is a gain to be made by changing them.
