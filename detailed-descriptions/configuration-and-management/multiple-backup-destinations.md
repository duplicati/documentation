---
description: This page describes how to store a backup in more than one destination
---

# Multiple backup destinations

{% hint style="info" %}
Multiple backup destinations are available from Duplicati 2.3.0.0.
{% endhint %}

A backup job can have more than one destination. The backup is made to the first destination as usual, and after each successful backup, Duplicati copies the backup files from the first destination to each of the other destinations. This makes it possible to follow a 3-2-1 backup strategy (3 copies of the data, on 2 different media, 1 of them offsite) with a single backup job, for instance with a local drive as the first destination and a cloud provider as the second.

The other destinations hold exact copies of the backup files, so each of them can be used on its own to [restore files](../../getting-started/restoring-files.md) if the first destination is lost.

## Adding a destination in the UI

On the "Destination" step of the backup setup, configure the first destination as usual. Then click "Add another destination" to add more. The menu next to each destination has "Edit target URL", "Copy URL to clipboard" and "Remove", and a new destination can be stored for reuse with "Save as connection string".

The copy to the other destinations runs after every successful backup. By default it also runs after a backup that completed with warnings.

## Configuring from the commandline

The extra destinations are configured with the `--remote-sync-json-config` option. The value is either a JSON document or the path to a file that contains it:

```json
{
  "sync-on-warnings": true,
  "destinations": [
    {
      "url": "s3://my-bucket/backup?auth-username=...&auth-password=...",
      "mode": "inline"
    },
    {
      "url": "ssh://offsite.example.com/backup?auth-username=...",
      "mode": "interval",
      "interval": "7D"
    }
  ]
}
```

The `mode` decides when a destination is updated:

* `inline` (default): after every successful backup.
* `interval`: when the given `interval` has passed since the last copy, for instance `7D`.
* `counting`: after the given `count` of backups.

If `mode` is left out, it is guessed from whether `interval` or `count` is set. A destination with `interval` or `counting` mode but no `interval` or `count` falls back to `inline`, with a warning.

Each destination also accepts the settings of the [SyncTool](../../duplicati-programs/command-line-interface-cli-1/synctool.md), written in lowercase with dashes, for instance `"retention": true` to rename files on the destination instead of deleting them, `"verify-get-after-put": true` to download and check each uploaded file, or `"dst-options": ["key=value"]` to pass options to the destination. Set `"sync-on-warnings": false` to skip the copy when the backup has warnings.
