---
description: >-
  This page describes confidential processing in the Duplicati Console, which
  limits what the console stores about an organization that handles
  confidential or protected information.
---

# Confidential processing

Confidential processing is a mark on an organization in the Duplicati Console for organizations that handle confidential or protected information, such as health records or special categories of personal data. When it is set, the console limits what it stores about the organization, and the features that would require storing backup contents or log text in the console are turned off.

{% hint style="warning" %}
Enabling confidential processing does not by itself permit storing HIPAA protected health information or GDPR special categories of personal data in the console. Storing such data requires a signed contract with Duplicati. Contact Duplicati before storing such data in the console.
{% endhint %}

## What changes

For an organization with confidential processing, the following applies:

- **Reduced backup reports.** Machines send reports with log message ids only, and not the log text. The console rejects reports that contain full log text, so a machine that is set up to send full reports has its reports refused until it is reconfigured.
- **No backup configurations.** [Backup configurations](backup-configurations.md) cannot be created or assigned, and the **Configurations** tab is hidden. Connection links do not offer configurations to apply.
- **No managed backups.** [Managed backups](managed-backups.md) cannot be created, and the **Managed backups** tab is hidden.
- **No AI summaries and summary reports.** The AI analysis of backup reports and the summary reports are not available, and the settings for them are hidden.

Monitoring, alerts, restore tests, remote access, [Duplicati Storage](duplicati-storage.md) and the other console features work as usual. The backend enforces the same limits as the console, so the features cannot be reached through the API either.

Organizations with confidential processing are marked with a shield icon in the list of organizations.

## Where it applies

Confidential processing applies to the organization it is set on and to all of its [sub-organizations](organizations-and-sub-organizations.md). A sub-organization of a confidential organization is confidential as well, and this cannot be changed on the sub-organization.

The setting is found under **Settings → General** for the organization, where the description says whether the organization is confidential, and whether the mark is set on the organization itself or inherited from a parent organization.

## Enabling confidential processing

An owner can set the mark when creating a sub-organization, by checking **Processes confidential or protected information** in the dialog. The sub-organization is then confidential from the start, so no full reports are stored for it at any time.

For an existing organization, and for a top-level organization, confidential processing is enabled by Duplicati support. Contact support with the name of the organization.

{% hint style="info" %}
Once enabled, confidential processing can only be disabled by Duplicati support. The reduced reports that were received while the mark was set are not expanded afterwards.
{% endhint %}

## Preparing the machines

The machines in a confidential organization keep running their backups as before, and the reports arrive in the console with the log message ids. The report pages in the console show the messages that correspond to the ids, so the status of a backup, the number of files and the warnings and errors are still visible. The free text from the log, such as file names that failed, is only available in the log on the machine itself.

If a machine was connected before the organization became confidential, no change is needed on the machine: the console tells the machine to send reduced reports when it connects.
