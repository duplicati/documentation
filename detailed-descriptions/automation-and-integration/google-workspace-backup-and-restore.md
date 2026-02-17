---
description: This page describes how Google Workspace backup and restore works with Duplicati
---

# Google Workspace backup and restore

Duplicati supports backing up and restoring Google Workspace data through the Google APIs. The backup workflow captures content in native formats (for example, RFC 2822 for email and JSON for structured objects), and restore operations re-create items through the appropriate Google API endpoints.

{% hint style="info" %}
Google Workspace backup and restore was added in Canary 2.2.0.105
{% endhint %}

{% hint style="warning" %}
The Google Workspace backup feature has a limit that covers up to **5 users, groups, shared drives, or sites**. The Google Workspace backup feature is source-available, but **not open-source** like the rest of Duplicati. There are no limitations on restore.

A license is required to use Google Workspace backup in production. Contact Duplicati Inc. support or sales to obtain a license.
{% endhint %}

### Overview

**Key characteristics**

- **Format preservation**: Data is stored in native formats such as RFC 2822 (EML), JSON, and standard document formats. Drive documents are stored in their exported formats, such as PDF, DOCX, or XLSX.
- **Metadata retention**: Original timestamps, identifiers, and properties are preserved where possible.
- **Restore to local disk**: It is possible to restore all data to a local destination for forensics or manual investigation.
- **Cross-user support**: Data from one user can be restored into another user account.
- **Domain-wide backup**: Supports backing up all users in a Google Workspace domain via domain-wide delegation.

### Google Workspace configuration options

To use the Google Workspace backup source you must supply credentials via either a service account with domain-wide delegation or OAuth 2.0 user credentials. When choosing what to back up, it is possible to filter on root types (users, groups, shared drives, sites, organizational units), and possible to apply filters to obtain fine-grained exclusion of data.

When making backups of a Google Workspace domain, the advanced option `--store-metadata-content-in-database` must be activated.

#### Authentication methods

| Method                        | Description                          | Use case                                                          |
| ----------------------------- | ------------------------------------ | ----------------------------------------------------------------- |
| Service Account (Recommended) | JSON key with domain-wide delegation | Automated or unattended backup for entire domain                  |
| OAuth 2.0 User Credentials    | Client ID, secret, and refresh token | Single user or personal Google accounts (not currently supported) |

**Service Account Configuration**

```json
{
  "type": "service_account",
  "project_id": "your-project-id",
  "private_key_id": "...",
  "private_key": "-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n",
  "client_email": "backup-service@your-project.iam.gserviceaccount.com",
  "client_id": "...",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token"
}
```

#### Connection configuration settings

| Parameter                       | Description                       | Required for                                |
| ------------------------------- | --------------------------------- | ------------------------------------------- |
| `--google-client-id`            | OAuth Client ID                   | OAuth authentication                        |
| `--google-client-secret`        | OAuth Client Secret               | OAuth authentication                        |
| `--google-refresh-token`        | OAuth Refresh Token               | OAuth authentication                        |
| `--google-service-account-json` | Service Account JSON content      | Service account authentication              |
| `--google-service-account-file` | Path to Service Account JSON file | Service account authentication              |
| `--google-admin-email`          | Admin email for impersonation     | Service account with domain-wide delegation |

#### Additional settings

| Parameter                      | Description                                                                                                      |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| `--google-included-root-types` | Root types to backup: `Users`, `Groups`, `SharedDrives`, `Sites`, `OrganizationalUnits`. Default: all types      |
| `--google-included-user-types` | User data types to backup: `Gmail`, `Drive`, `Calendar`, `Contacts`, `Tasks`, `Keep`, `Chat`. Default: all types |
| `--google-ignore-calendar-acl` | Skip reading calendar ACLs if the backup account does not have write permission. Default: false                  |
| `--google-ignore-existing`     | When restoring, skip items that already exist instead of updating them. Default: false                           |

### Supported data types

#### Root-level components

| Data type            | Backup | Restore | Notes                                          |
| -------------------- | ------ | ------- | ---------------------------------------------- |
| Users                | ✅     | ✅      | Individual user accounts and their data        |
| Groups               | ✅     | ❌      | Google Groups and their settings (backup only) |
| Shared Drives        | ✅     | ✅      | Team/Shared drives                             |
| Sites                | ✅     | ❌      | Google Sites (backup only)                     |
| Organizational Units | ✅     | ⚠️      | OU structure and hierarchy (limited restore)   |

#### Per-user data types

| Data type       | Backup | Restore | Notes                                               |
| --------------- | ------ | ------- | --------------------------------------------------- |
| Gmail           | ✅     | ✅      | Emails, labels, settings, filters                   |
| Google Drive    | ✅     | ✅      | Files, folders, permissions, comments               |
| Google Calendar | ✅     | ✅      | Events, calendars, ACLs                             |
| Google Contacts | ✅     | ✅      | Contact information and groups                      |
| Google Tasks    | ✅     | ✅      | Task lists and individual tasks                     |
| Google Keep     | ✅     | ✅      | Notes and attachments (limited attachement restore) |
| Google Chat     | ✅     | ⚠️      | Spaces and messages (limited restore)               |

### Backup and restore details by type

#### Gmail

**Backup**

- Emails exported as RFC 2822 (`.eml`) format with full headers and attachments.
- Folder hierarchy captured and preserved as labels.
- Label definitions, colors, and visibility settings backed up.
- Email filters backed up as JSON.
- Forwarding settings, vacation responder, signatures, and IMAP settings captured.

**Restore**

- Messages imported with a "Restored" label by default.
- Original folder structure recreated within the target.
- Duplicate detection via `Message-ID` header.
- Labels recreated with original colors and visibility.
- Settings restored to their original values.

#### Google Drive

**Backup**

- Files downloaded with original content.
- Google Workspace native files exported to standard formats (Docs → DOCX, Sheets → XLSX, Slides → PPTX, Drawings → PNG).
- Folder structure and metadata captured.
- Sharing permissions and access controls recorded.
- File comments and replies backed up.
- Previous file versions backed up as binary data.

**Restore**

- Files restored to a "Restored" folder by default.
- Folder hierarchy is recreated.
- Binary files uploaded as new revisions when duplicates exist.
- Permissions restored (except owner permissions which cannot be set via API).
- Comments recreated on restored files.
- Restores between shared and personal drives supported both ways.

**Duplicate detection**

- Name + path matching
- Size comparison
- Hash comparison

#### Google Calendar

**Backup**

- Events exported as JSON and ICS formats.
- Recurrence patterns captured.
- Event attachments backed up as binary files.
- Calendar sharing permissions (ACLs) captured.

{% hint style="info" %}
Calendar ACLs require write permissions to read. If your backup account does not have write permissions, use the `--google-ignore-calendar-acl` option to skip ACL backup.
{% endhint %}

**Restore**

- Events restored to a "Restored" calendar by default.
- Duplicate detection based on `iCalUID`.
- Attachments uploaded to Drive and linked to events.
- ACLs recreated (except owner permissions).

#### Google Contacts

**Backup**

- Contacts exported as JSON and vCard formats.
- Contact photos backed up as binary files.
- Contact groups and membership captured.
- Folder hierarchy preserved.

**Restore**

- Contacts created with duplicate detection based on email address.
- Contact photos uploaded separately after contact creation.
- Contacts added to restored groups.

#### Google Tasks

**Backup**

- Task lists enumerated with all properties.
- Tasks captured with full details.
- Task relationships and completion status preserved.

**Restore**

- Task lists recreated with original titles.
- Tasks restored to their respective lists.
- Orphaned tasks placed in a "Restored" task list.
- Duplicate detection based on task title.

#### Google Keep

**Backup**

- Notes captured with content, title, and metadata.
- Attachments (images and other files) backed up as binary data (including Drive files).

**Restore**

- Notes recreated with original titles and content.
- Attachments uploaded to Drive and linked in the note.
- Duplicate detection based on note title.
- Note body includes links to restored attachments.

{% hint style="info" %}
The Keep API does not support direct attachment uploads. Attachments are uploaded to Drive and referenced in the restored note.
{% endhint %}

#### Google Chat

**Backup**

- Chat spaces enumerated with settings.
- Messages captured with content.
- Message attachments backed up as binary files.
- Membership information preserved.

**Restore**

- Spaces can be recreated with original settings.
- Messages can be posted to spaces (with limitations).

{% hint style="warning" %}
Chat restore has significant limitations:

- Messages appear as being created by the impersonated user, not the original sender
- Use of the Import API is not currently supported
  {% endhint %}

#### Google Groups (backup only)

**Backup**

- Group metadata and settings captured.
- Membership lists backed up.
- Group email aliases preserved.
- Group email conversations backed up.

**Restore**

Groups cannot be restored through the API. Group data is backed up for reference purposes only.

#### Google Sites (backup only)

**Backup**

- Site metadata captured.
- Content backed up where API permits.

{% hint style="warning" %}
Sites cannot be restored through the API. The Sites API has limited export capabilities, and Sites data is backed up for reference purposes only.
{% endhint %}

#### Shared Drives

**Backup**

- Shared drive metadata captured.
- Drive-level permissions recorded.
- Files and folders backed up (same as user Drive).

**Restore**

- Shared drive permissions restored (with role limitations).
- Content restored maintaining folder structure.
- File organizer and organizer roles only valid for shared drives.

#### Organizational Units

**Backup**

- Organizational unit hierarchy captured.
- OU-specific settings backed up.

**Restore**

OU data is backed up for reference purposes only.
Manual configuration is required for restores.

### Technical limitations

#### API limitations

| Limitation               | Description                                       | Impact                                   |
| ------------------------ | ------------------------------------------------- | ---------------------------------------- |
| Chat message attribution | Messages appear as sent by impersonated user      | Original sender context lost             |
| Sites export             | No comprehensive export API available             | Only metadata backed up                  |
| Google Forms             | Form responses require separate API               | Structure backed up via Drive only       |
| Revision history         | Native file revisions cannot be directly restored | Only current version restorable          |
| Calendar attachments     | Direct attachment upload not supported            | Attachments uploaded to Drive and linked |
| Keep attachments         | Direct attachment upload not supported            | Attachments uploaded to Drive and linked |

#### Permission limitations

| Feature                  | Limitation                                              | Notes                         |
| ------------------------ | ------------------------------------------------------- | ----------------------------- |
| Owner permissions        | Cannot be restored via API                              | Must be manually reassigned   |
| External sharing         | Some external sharing permissions may not be restorable | Depends on domain settings    |
| Domain-specific settings | Some settings require domain admin privileges           | May need manual configuration |

#### Data fidelity limitations

{% hint style="info" %}
For restores into Google Workspace, data is technically "created again" as the API does not support true restoration. Visually the data looks the same but internal values, such as timestamps or references, may be different in the restored version.
{% endhint %}

| Data type     | Limitation                                                    |
| ------------- | ------------------------------------------------------------- |
| Email         | Original Message-ID preserved, but internal Google ID changes |
| Calendar      | Online meeting links preserved but may not be recreated       |
| Contacts      | Contact ID changes on restore                                 |
| Files         | File ID changes; content hash and timestamps preserved        |
| Chat messages | Original sender/timestamp lost on restore                     |

#### Rate limiting

Google APIs have usage quotas. The implementation includes exponential backoff for:

- HTTP 429 (Too Many Requests)
- HTTP 503 (Service Unavailable)

| API          | Default Quota               | Recommendation            |
| ------------ | --------------------------- | ------------------------- |
| Gmail API    | 250 quota units/user/second | Increase for bulk backup  |
| Drive API    | 12,000 requests/minute      | Increase for large drives |
| Calendar API | 500 requests/100 seconds    | Usually sufficient        |
| People API   | 90 requests/minute          | May need increase         |

### API permissions reference

Permissions are divided by operation (backup vs restore). All permissions require admin consent for domain-wide delegation when using service accounts.

#### Backup permissions

| Scope                                                              | Permission  | Description                                             |
| ------------------------------------------------------------------ | ----------- | ------------------------------------------------------- |
| `https://www.googleapis.com/auth/gmail.readonly`                   | Read-only   | Access to read all Gmail messages, labels, and settings |
| `https://www.googleapis.com/auth/drive.readonly`                   | Read-only   | Read files, folders, and metadata                       |
| `https://www.googleapis.com/auth/calendar.readonly`                | Read-only   | Read calendar events and metadata                       |
| `https://www.googleapis.com/auth/contacts.readonly`                | Read-only   | Read contacts and contact groups                        |
| `https://www.googleapis.com/auth/tasks.readonly`                   | Read-only   | Read task lists and tasks                               |
| `https://www.googleapis.com/auth/keep.readonly`                    | Read-only   | Read notes and attachments                              |
| `https://www.googleapis.com/auth/chat.spaces.readonly`             | Read-only   | Read chat spaces                                        |
| `https://www.googleapis.com/auth/chat.messages.readonly`           | Read-only   | Read chat messages                                      |
| `https://www.googleapis.com/auth/chat.memberships.readonly`        | Read-only   | Read chat memberships                                   |
| `https://www.googleapis.com/auth/admin.directory.group.readonly`   | Read-only   | Read group information                                  |
| `https://www.googleapis.com/auth/admin.directory.user.readonly`    | Read-only   | Read user information                                   |
| `https://www.googleapis.com/auth/apps.groups.settings`             | Settings    | Read group settings                                     |
| `https://www.googleapis.com/auth/admin.directory.orgunit.readonly` | Read-only   | Read OU structure                                       |
| `https://www.googleapis.com/auth/calendar`                         | Full access | Read calendar ACLs (optional)                           |

#### Restore permissions

| Scope                                                     | Permission  | Description                                     |
| --------------------------------------------------------- | ----------- | ----------------------------------------------- |
| `https://www.googleapis.com/auth/gmail.modify`            | Modify      | Import messages, create labels, modify settings |
| `https://www.googleapis.com/auth/drive`                   | Full access | Create, modify, and delete files                |
| `https://www.googleapis.com/auth/calendar`                | Full access | Create and modify calendars and events          |
| `https://www.googleapis.com/auth/contacts`                | Full access | Create and modify contacts                      |
| `https://www.googleapis.com/auth/tasks`                   | Full access | Create and modify tasks                         |
| `https://www.googleapis.com/auth/keep`                    | Full access | Create notes                                    |
| `https://www.googleapis.com/auth/chat.messages`           | Write       | Create messages (limited)                       |
| `https://www.googleapis.com/auth/chat.spaces`             | Write       | Create spaces                                   |
| `https://www.googleapis.com/auth/admin.directory.orgunit` | Write       | Modify OU structure                             |
| `https://www.googleapis.com/auth/admin.directory.user`    | Write       | Modify user information                         |
| `https://www.googleapis.com/auth/admin.directory.group`   | Write       | Modify group information                        |
| `https://www.googleapis.com/auth/apps.groups.settings`    | Write       | Modify group settings                           |

#### Permissions by data type

| Data type            | Backup (read)                                                                 | Restore (write)                |
| -------------------- | ----------------------------------------------------------------------------- | ------------------------------ |
| Gmail                | `gmail.readonly`                                                              | `gmail.modify`                 |
| Google Drive         | `drive.readonly`                                                              | `drive`                        |
| Google Calendar      | `calendar.readonly`                                                           | `calendar`                     |
| Google Contacts      | `contacts.readonly`                                                           | `contacts`                     |
| Google Tasks         | `tasks.readonly`                                                              | `tasks`                        |
| Google Keep          | `keep.readonly`                                                               | `keep`                         |
| Google Chat          | `chat.spaces.readonly`, `chat.messages.readonly`, `chat.memberships.readonly` | `chat.messages`, `chat.spaces` |
| Groups               | `admin.directory.group.readonly`, `apps.groups.settings`                      | Not restorable                 |
| Users                | `admin.directory.user.readonly`                                               | `admin.directory.user`         |
| Shared Drives        | `drive.readonly`                                                              | `drive`                        |
| Organizational Units | `admin.directory.orgunit.readonly`                                            | `admin.directory.orgunit`      |
| Sites                | `drive.readonly`                                                              | Not restorable                 |

### Data format and storage

#### Backup data formats

| Data type       | Format          | Extension               |
| --------------- | --------------- | ----------------------- |
| Email content   | RFC 2822        | `.eml`                  |
| Email metadata  | JSON            | `.json`                 |
| Calendar events | JSON/ICS        | `.json`/`.ics`          |
| Contacts        | JSON/vCard      | `.json`/`.vcf`          |
| Contact photos  | Binary          | `.photo`                |
| Files           | Original/Export | (original or converted) |
| Tasks           | JSON            | `.json`                 |
| Keep notes      | JSON            | `.json`                 |
| Chat messages   | JSON            | `.json`                 |
| All metadata    | JSON            | `.json`                 |

#### Export formats for Google Workspace files

| Google Format   | Export type                         |
| --------------- | ----------------------------------- |
| Google Docs     | DOCX                                |
| Google Sheets   | XLSX                                |
| Google Slides   | PPTX                                |
| Google Drawings | PNGß                                |
| Google Forms    | Metadata only (via Drive API)       |
| Google Sites    | Metadata only (no export available) |

#### Storage requirements

- Email: RFC 2822 content + JSON metadata per message
- Files: Original file size + metadata overhead
- Calendar: JSON per event (typically 1–10 KB)
- Contacts: JSON per contact (typically 1–5 KB)
- Tasks: JSON per task (typically 0.5–2 KB)

### Best practices

#### Backup recommendations

1. Enable metadata storage with `--store-metadata-content-in-database`.
2. Use service account authentication with domain-wide delegation for organizational backups.
3. Schedule regular backups during off-peak hours.
4. Verify required permissions before backup.
5. Monitor API quota usage and request increases if needed.

#### Restore recommendations

1. Test restore procedures regularly.
2. Verify target user exists before restore.
3. Use `--google-ignore-existing` carefully; default behavior skips duplicates.
4. Review restored sharing permissions (owner permissions cannot be restored).
5. Be aware of limitations when restoring chat messages and sites.

#### Security considerations

1. Store service account JSON and OAuth tokens securely.
2. Use least privilege principle - only request scopes that are actually needed.
3. Enable audit logging in Google Workspace Admin Console.
4. Encrypt backup data at rest.
5. Limit who can configure and run backups.
6. Rotate service account keys regularly.

### Troubleshooting

#### Common issues

| Issue                                              | Solution                                                                     |
| -------------------------------------------------- | ---------------------------------------------------------------------------- |
| "Access to calendar ACLs was denied"               | Use `--google-ignore-calendar-acl` option or grant Calendar write permission |
| "Licensed Google Workspace feature seats exceeded" | Purchase additional licenses or reduce scope of backup                       |
| "Missing credentials"                              | Verify service account JSON or OAuth credentials are correct                 |
| "Domain-wide delegation not enabled"               | Enable domain-wide delegation in Google Cloud Console and authorize scopes   |
| "Rate limit exceeded"                              | Enable exponential backoff or request quota increases                        |

### References

- [Google Workspace Admin SDK Directory API](https://developers.google.com/admin-sdk/directory)
- [Gmail API Reference](https://developers.google.com/gmail/api/reference/rest)
- [Google Drive API Reference](https://developers.google.com/drive/api/reference/rest/v3)
- [Google Calendar API Reference](https://developers.google.com/calendar/api/v3/reference)
- [Google People API Reference](https://developers.google.com/people/api/rest)
- [Google Tasks API Reference](https://developers.google.com/tasks/reference/rest)
- [Google Keep API Reference](https://developers.google.com/keep/api/reference/rest)
- [Google Chat API Reference](https://developers.google.com/chat/api/reference/rest)
- [Google Cloud Console](https://console.cloud.google.com/)
- [Google Admin Console](https://admin.google.com/)
