---
description: This page describes the Movistar Cloud storage destination
---

# Movistar Cloud Destination

{% hint style="info" %}
This destination is marked as untested, as it can only be used by Movistar customers and the Duplicati team does not have an account to test it with. It is hidden from the destination list in the user interface, see [deprecated and untested destinations](../destination-overview.md#deprecated-and-untested-destinations).
{% endhint %}

Duplicati supports storing backups in Movistar Cloud (also known as MiCloud or Zefiro), the cloud storage offered by the Spanish provider Movistar. The destination uses an unofficial REST integration and was contributed by the community. It is available from canary 2.3.0.103 and stable 2.4.0.0.

## URL format for Commandline

To use Movistar Cloud, use the following URL format:

```
movistarcloud://<folder>/<subfolder>
  ?email=<account email>
  &password=<account password>
  &deviceID=<device id>
```

All three options are required:

* `--email`: The email address of the MiCloud account.
* `--password`: The password of the MiCloud account.
* `--deviceID`: The device ID used by the web client. You can find it by logging in to the Movistar Cloud web interface, opening the browser developer tools, and copying the value of the `X-Deviceid` request header.

## Advanced options

* `--list-limit`: The maximum number of items returned per listing call. Defaults to `2000`.
* `--ignore-validation-result`: After an upload, Movistar Cloud validates the file on the server. By default Duplicati waits for this validation; set this option to continue immediately after the upload.
* `--validation-timeout`: The maximum time to wait for the server-side validation. Defaults to `10m`.
* `--validation-poll-interval`: How often the validation status is checked. Defaults to `2s`.
* `--diagnostics`: Log storage space information when testing the connection. Defaults to `false`.
* `--diagnostics-level`: Either `basic` (storage space only) or `trash` (also lists trash entries). Defaults to `basic`.
* `--trash-page-size`: The number of trash entries to list when `--diagnostics-level=trash`. Defaults to `50`.
