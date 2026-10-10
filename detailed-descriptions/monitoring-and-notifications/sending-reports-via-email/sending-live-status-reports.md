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
