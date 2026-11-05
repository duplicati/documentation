---
description: This page describes how to send the progress of running operations to a URL
---

# Sending live status reports

{% hint style="info" %}
Live status reports are available from Duplicati 2.4.0.0.
{% endhint %}

The other report modules send a message when an operation has finished. The live status report module instead posts the current state of a running operation to a URL at a regular interval, which makes it possible to show the progress of backups on a dashboard.

The module is inactive until a URL is set:

```
--http-report-status-url=https://dashboard.example.com/duplicati
```

Multiple URLs can be given, separated with semicolons.

While an operation is running, Duplicati posts a JSON document to the URL at most once per interval (`--http-report-status-interval`, default `30s`), and once more when the operation has finished. A report looks like this:

```json
{
  "operation": "Backup",
  "status": "Completed",
  "startedUtc": "2026-10-10T12:47:26.7842022Z",
  "reportedUtc": "2026-10-10T12:47:33.2555037Z",
  "isCompleted": true,
  "backendEvents": 18,
  "logEntries": 0,
  "progress": {
    "phase": "Complete",
    "progress": 1,
    "filesProcessed": 0,
    "fileSizeProcessed": 0,
    "fileCount": 0,
    "fileSize": 0,
    "countingFiles": false,
    "currentFileOffset": 0,
    "activeTransfers": 0
  },
  "recentLogLines": [],
  "metadata": {
    "duplicatiVersion": "2.4.0.1",
    "machineId": "<machine id>",
    "backupId": "<backup id>",
    "backupName": "My backup",
    "machineName": "<machine name>",
    "destinationType": "file",
    "installationType": "...",
    "operatingSystem": "Windows",
    "operatingSystemDetailed": "..."
  }
}
```

## Options

| Option | Default | Description |
| --- | --- | --- |
| `--http-report-status-url` | | The URL(s) to post the reports to, separated with semicolons. |
| `--http-report-status-interval` | `30s` | The minimum time between two reports. |
| `--http-report-status-max-log-lines` | `20` | The number of recent log lines included in each report. |
| `--http-report-status-allow-paths-in-log-messages` | `false` | Include file paths in the log lines. By default they are removed. |
| `--http-report-status-reduced-reporting` | `false` | Only send log message ids, counters and dates, without message text or file names. |
| `--http-report-status-accept-specified-ssl-hash` | | Accept a server certificate with this SHA1 hash, for self-signed certificates. |
| `--http-report-status-accept-any-ssl-certificate` | `false` | Accept any server certificate. Only for testing. |
| `--http-report-status-ignore-revocation-failure` | `false` | Ignore failures to check if the certificate is revoked. |

## Heartbeat monitoring

Because a report is posted when an operation starts and again when it finishes, the module can also feed a heartbeat monitoring service, which alerts you when a backup does not run on time or reports a failure. No scripts are needed. The reports to look for are:

* `"status":"Started"` is sent when the operation starts.
* `"status":"Completed"` is sent when it finishes.
* `"status":"Failed"` is sent with an `errorMessage` when the operation stops because of an error.

An operation that finishes with errors but keeps running to the end still reports `"status":"Completed"`, and adds an `errorMessage`, so treat any report that contains `"errorMessage"` as a failure. A run with only warnings is reported as completed without an `errorMessage`.

While the operation runs, reports with `"status":"Progress"` are sent as well. They match none of the keywords below, so the monitoring service can ignore them. If you only need the start and the end, a longer interval, for example `--http-report-status-interval=1h`, sends fewer of them.

This configuration was tested and works with [Watchgoose](https://watchgoose.com) monitoring service, but can also work with other platforms that are API compatible and can can classify a request by keywords in its body. For example:

* Start keyword: `"status":"Started"`
* Success keyword: `"status":"Completed"`
* Failure keywords: `"status":"Failed"` and `"errorMessage"`

The failure keywords must be checked before the success keyword, so that a completed run with errors is reported as a failure.
