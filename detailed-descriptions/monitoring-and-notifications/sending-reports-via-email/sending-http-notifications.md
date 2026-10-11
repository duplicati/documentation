---
description: This page describes how to send reports via the HTTP protocol
---

# Sending HTTP notifications

The most versatile reporting option is the ability to send messages via the HTTP(s) protocol. By default messages are sent as a [form url encoded](https://en.wikipedia.org/wiki/Percent-encoding#The\_application.2Fx-www-form-urlencoded\_type) body in a request with the [POST](https://en.wikipedia.org/wiki/POST\_\(HTTP\)) verb.

To use the option, you only need to provide the url to send to:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-20 at 14.50.14.png" alt=""><figcaption><p>Configuring a HTTP notification</p></figcaption></figure>

Besides the URL it is also possible to configure:

* The message body and type (JSON is supported)
* The HTTP verb used
* Conditions on when to send emails
* Conditions on what log elements to include

For details on how to customize the notification message, see the [section on customizing message content](custom-message-content.md).

### New in 2.0.9.106

You can now specify multiple urls, using the options:

```
--send-http-form-urls=
--send-http-json-urls=
```

These two options greatly simplify sending notifications to multiple destinations. Additionally, the options make it possible to send both the form-encoded result in text format as well as in JSON format.

### Retries and certificates

If the request fails, it is retried `--send-http-retries` times (default `3`), waiting `--send-http-retry-delay` (default `1s`) between attempts.

For servers with a self-signed certificate, use `--send-http-accept-specified-ssl-hash=<sha1 hash>` to accept that specific certificate. `--send-http-accept-any-ssl-certificate` accepts any certificate and should only be used for testing, and `--send-http-ignore-revocation-failure` ignores failures to check whether the certificate is revoked.

To send the progress of running operations instead of a report when they finish, see [Sending live status reports](sending-live-status-reports.md).
