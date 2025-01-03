---
description: This page describes the Microsoft Group storage destination
---

# Microsoft Group Destination

Duplicati supports using [Microsoft Groups](https://learn.microsoft.com/en-us/microsoftteams/office-365-groups) as a storage destination. To use the destination, use the following URL format:

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
