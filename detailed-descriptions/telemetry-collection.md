---
description: This page describes the telemetry collected by Duplicati and how to share it
---

# Telemetry collection

When Duplicat is running, it collects some basic non-identifying telemetry, such as what version of Duplicati is running, what operating system it is running on and similar values. When running a backup it also collects the type of connection, the duration, the size of the remote and local filesets.

The purpose of this data collection is to give us an insight into how Duplicati is used and determine how we can best focus on making Duplicati better. We are sharing the aggregated data that we collect on a public page: [https://usage-reporter.duplicati.com](https://usage-reporter.duplicati.com/)

## Opting out

Even though we try very hard to only collect non-sensitive information, you are in control and can choose to opt out of data collection if you want to.

The easy way to opt out when using the UI is to visit the settings page, and simply choose "None / disabled":

<figure><img src="../.gitbook/assets/Screenshot 2025-10-03 at 15.33.13.png" alt=""><figcaption></figcaption></figure>

However, since Duplicati also collects the event of starting Duplicati, you will still be sending at least one telemetry data point. If you prefer not to send anything at all, you need to set an environment variable before starting Duplicati:

```bash
USAGEREPORTER_Duplicati_LEVEL=none
```

If this is set, no telemetry is sent, and it is not possible to enable telemetry from within the UI.
