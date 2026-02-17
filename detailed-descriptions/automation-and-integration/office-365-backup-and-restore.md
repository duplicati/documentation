---
description: This page describes how Office 365 backup and restore works with Duplicati
---

# Office 365 backup and restore

Duplicati supports backing up and restoring Microsoft Office 365 (Microsoft 365) data through the Microsoft Graph API. The backup workflow captures content in native formats (for example, MIME for email and JSON for structured objects), and restore operations re-create items through the appropriate Graph API endpoints.

{% hint style="info" %}
Office 365 backup and restore was added in Canary 2.2.0.104
{% endhint %}

{% hint style="warning" %}
The Office 365 backup feature has a limit that covers up to **5 mailboxes or sites**. The Office 365 backup feature is source-available, but **not open-source** like the rest of Duplicati. There are no limitations on restore.

A license is required to use Office 365 backup in production. Contact Duplicati Inc. support or sales to obtain a license.
{% endhint %}

### Overview

**Key characteristics**

* **Format preservation**: Data is stored in native formats such as MIME, JSON, and HTML.
* **Metadata retention**: Original timestamps, identifiers, and properties are preserved where possible.
* **Restore to local disk**: It is possible to restore all data to a local destination for forensics or manual investigation.
* **Cross-tenant support**: Data can be restored into different tenants.
* **Cross-target support**: Data from one user/group/site can be restored into another.

### Office 365 configuration options

To use the Office 365 backup source you must supply a tenant ID and either a client secret or a certificate. When choosing what to back up, it is possible to filter on types (users, groups, etc), and possible to apply filters to obtain fine-grained exclusion of data.

When making backups of an Office 365 tenant, the advanced option `--store-metadata-content-in-database` must be activated.

#### Authentication methods

| Method                                   | Description                                         | Use case                       |
| ---------------------------------------- | --------------------------------------------------- | ------------------------------ |
| Client secret                            | App client credentials with secret                  | Automated or unattended backup |
| Certificate                              | OAuth 2.0 client credentials with X.509 certificate | Higher security environments   |
| Delegated user (not currently supported) | OAuth 2.0 credentials for an AD super admin         | Some APIs require this         |

#### Connection configuration settings

<table><thead><tr><th width="299.38671875">Parameter</th><th width="321.6640625">Description</th><th>Required for</th></tr></thead><tbody><tr><td><code>--office365-tenant-id</code></td><td>Azure AD tenant ID (GUID or domain)</td><td>All</td></tr><tr><td><code>--office365-client-id</code></td><td>Azure AD application (client) ID</td><td>Client secret</td></tr><tr><td><code>--office365-client-secret</code></td><td>Client secret for authentication</td><td>Client secret</td></tr><tr><td><code>--office365-certificate-path</code></td><td>Path to X.509 certificate (alternative to secret)</td><td>Certificate</td></tr><tr><td><code>--office365-certificate-password</code></td><td>Certificate password if encrypted</td><td>Certificate</td></tr><tr><td><code>--office365-graph-base-url</code></td><td>Graph API base URL (default: <code>https://graph.microsoft.com</code>)</td><td>Sovereign clouds</td></tr><tr><td><code>--office365-scope</code></td><td>Set the permission scope (default: <code>https://graph.microsoft.com/.default</code></td><td>Custom permissions</td></tr></tbody></table>

#### Additional settings

| Parameter                          | Description                                                                                                                                                                                                                                                                                                                                         |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--office365-included-root-types`  | The different root types to include for backups. The default setting is to include all types: `Users`, `Groups` and `Sites`                                                                                                                                                                                                                         |
| `--office365-included-user-types`  | <p>The data types to include from users in backups. The default settings include: <code>Profile</code>, <code>Mailbox</code>, <code>Calendar</code>, <code>Contacts</code>, <code>Planner</code> and <code>Chats</code>. <br><br>If delegated permissions are used, these types can also be included: <code>Tasks</code> and <code>Notes</code></p> |
| `--office365-included-group-types` | <p>The data types to include from groups in backups. The default settings include: <code>Mailbox</code>, <code>Files</code>, <code>Planner</code> and <code>Teams</code>.<br><br>If delegated permissions are used, these types can also be included: <code>Calendar</code> and <code>Notes</code></p>                                              |
| `--office365-ignore-existing`      | When restoring data into a tenant the default is to check for existing data to avoid creating duplicates. Use this option to always recreate data in the destination.                                                                                                                                                                               |

#### Permission types

* **Application permissions**: Used for most backup/restore operations and require admin consent.
* **Delegated permissions**: Required for certain features (Tasks, Notes, Group Calendar).

### Supported data types

| Data type             | Backup | Restore (\*) | Notes                                                                                        |
| --------------------- | ------ | ------------ | -------------------------------------------------------------------------------------------- |
| User email            | ✅      | ✅            | MIME format with metadata                                                                    |
| Email folders         | ✅      | ✅            | Hierarchy preserved                                                                          |
| Mailbox rules         | ✅      | ✅            | Inbox rules and filters                                                                      |
| Mailbox settings      | ✅      | ✅            | Auto-replies, signatures                                                                     |
| OneDrive files        | ✅      | ✅            | Large file upload sessions supported                                                         |
| OneDrive folders      | ✅      | ✅            | Structure preserved                                                                          |
| File permissions      | ✅      | ✅            | Sharing restored via invite endpoint                                                         |
| Calendar events       | ✅      | ✅            | Including recurrence patterns                                                                |
| Calendar attachments  | ✅      | ✅            | File attachments on events                                                                   |
| Contacts              | ✅      | ✅            | Including contact photos                                                                     |
| Contact folders       | ✅      | ✅            | Folder hierarchy preserved                                                                   |
| Planner plans         | ✅      | ⚠️           | Plans cannot be created via API                                                              |
| Planner buckets       | ✅      | ✅            | Restored to existing plans                                                                   |
| Planner tasks         | ✅      | ✅            | Full task details and assignments                                                            |
| OneNote notebooks     | ⚠️     | ⚠️           | Requires delegated permissions                                                               |
| OneNote sections      | ⚠️     | ⚠️           | Including section groups                                                                     |
| OneNote pages         | ⚠️     | ⚠️           | HTML content                                                                                 |
| To-Do task lists      | ⚠️     | ⚠️           | Requires delegated permissions                                                               |
| To-Do tasks           | ⚠️     | ⚠️           | Including checklist items                                                                    |
| User profile          | ✅      | ✅            | Photo and editable properties                                                                |
| User chats            | ⚠️     | ⚠️           | <p>Backup needs delegated permissions<br>Restore has API limitations, needs MS whitelist</p> |
| Chat messages         | ⚠️     | ⚠️           | <p>Backup needs delegated permissions<br>Restore has API limitations, needs MS whitelist</p> |
| Teams channels        | ✅      | ✅            | Standard and private channels                                                                |
| Channel messages      | ✅      | ⚠️           | <p>Including replies<br>Restore requires MS whitelist</p>                                    |
| Channel tabs          | ✅      | ✅            | Tab configuration preserved                                                                  |
| Team apps             | ✅      | ✅            | App installation restored                                                                    |
| Group conversations   | ✅      | ⚠️           | <p>Threads and posts<br>Restore requires MS whitelist</p>                                    |
| Group calendar        | ✅      | ✅            | Requires delegated permissions                                                               |
| Group members         | ✅      | ✅            | Membership restored                                                                          |
| Group owners          | ✅      | ✅            | Ownership restored                                                                           |
| Group settings        | ✅      | ✅            | Configuration properties                                                                     |
| SharePoint sites      | ✅      | ✅            | Document libraries                                                                           |
| SharePoint lists      | ✅      | ✅            | Including list items                                                                         |
| List item attachments | ✅      | ✅            | File attachments                                                                             |

### Backup and restore details by type

#### Email (Exchange Online)

**Backup**

* Emails exported as MIME (`.eml`) plus JSON metadata.
* Folder hierarchy captured and preserved.
* Attachments included in the MIME content.

**Restore**

* Emails restored to a **Restored** folder by default, a target folder can be specified.
* Original folder structure recreated within the target.
* Duplicate detection via `InternetMessageId`.
* Large emails handled via chunked upload.

#### OneDrive for Business

**Backup**

* Files downloaded with original content.
* Folder structure and metadata captured.
* Sharing permissions recorded.

**Restore**

* Files uploaded to the target drive.
* Large files (>4 MB) use upload sessions.
* Timestamps restored via `fileSystemInfo`.
* Permissions restored via the invite endpoint.

**Duplicate detection**

* Name + path matching
* Size comparison
* Hash comparison (QuickXorHash or SHA1)

#### Calendar (Outlook)

**Backup**

* Events exported as JSON.
* Recurrence patterns captured.
* Attachments backed up separately.

**Restore**

* Events created in a **Restored** calendar by default, a target calendar can be specified.
* Series master events restored first.
* Exception events linked to restored masters.

#### Contacts (Outlook)

**Backup**

* Contacts exported as JSON.
* Contact photos backed up separately.
* Folder hierarchy captured.

**Restore**

* Contacts created in a **Restored** folder by default, a target folder can be specified.
* Original folder structure recreated.
* Photos restored after contact creation.
* Duplicate detection by email/name.

#### Planner

**Backup**

* Plans, buckets, and tasks captured.
* Task details (description, checklist) included.
* Assignments and labels preserved.

**Restore**

* Plans must already exist (Graph API limitation).
* Buckets created in the target plan.
* Tasks restored with full details.

#### OneNote

{% hint style="danger" %}
All OneNote items require delegated permissions
{% endhint %}

**Backup**

* Notebooks, section groups, sections enumerated.
* Pages exported as HTML content.
* Requires delegated permissions.

**Restore**

* Notebooks created in the user’s OneDrive.
* Sections created within notebooks.
* Pages restored as HTML.

#### To-Do tasks

**Backup**

* Task lists enumerated.
* Tasks with all properties captured.
* Checklist items and linked resources included.

**Restore**

* Task lists created.
* Tasks restored with properties.
* Checklist items restored.
* Linked resources restored.

#### Teams chats

**Backup**

* Chat conversations enumerated.
* Messages captured with content.
* Hosted content (images) backed up.

**Restore**

* New chats created with members.
* Messages sent to new chats.
* **Limitation**: Original sender context lost.
* **Limitation**: Requires an App registration whitelisted by Microsoft

#### Teams channels

**Backup**

* Standard and private channels enumerated.
* Channel properties captured.
* Messages and replies backed up.

**Restore**

* Channels created if not existing.
* Existing channels reused by name match.
* Messages posted to channels if App is whitelisted

#### SharePoint

**Document libraries**

* Files and folders captured and restored similarly to OneDrive.

**Lists**

* Lists enumerated with schema.
* List items captured with attachments.
* Lists and items restored with field values and attachments.

### Technical limitations

#### API limitations

| Limitation            | Description                                         | Impact                                                                    |
| --------------------- | --------------------------------------------------- | ------------------------------------------------------------------------- |
| Chat messages restore | Chat messages needs an App whitelisted by Microsoft | Requires an application to Microsoft before chat messages can be restored |
| Chat message restore  | Cannot preserve original sender                     | Messages appear from application                                          |
| Chat hosted content   | Cannot restore inline images                        | Images lost on restore                                                    |
| Planner plan creation | Plans cannot be created via API                     | Plans must pre-exist                                                      |
| File versions         | Only current version backed up                      | Historical versions not available                                         |
| Soft-deleted items    | Not captured in backup                              | Recently deleted items excluded                                           |
| Rate limiting         | Graph API throttling (429 responses)                | Automatic retry with backoff                                              |

#### Permission limitations

| Feature         | Permission type | Notes                  |
| --------------- | --------------- | ---------------------- |
| Tasks (To-Do)   | Delegated       | Requires user context  |
| Notes (OneNote) | Delegated       | Requires user context  |
| Group calendar  | Delegated       | Requires user context  |
| All others      | Application     | Admin consent required |

#### Data fidelity limitations

{% hint style="info" %}
For restores into an Office 365 tenant, data is technically "created again" as the API does not support restoring content. Visually the data looks the same but internal values, such as timestamps or references may be different in the restored version.
{% endhint %}

| Data type | Limitation                                                   |
| --------- | ------------------------------------------------------------ |
| Email     | Original message ID preserved, but internal Graph ID changes |
| Calendar  | Online meeting links preserved but not recreated             |
| Contacts  | Contact ID changes on restore                                |
| Files     | File ID changes; hash and timestamps preserved               |
| Messages  | Original sender/timestamp lost on restore                    |

### API permissions reference

Permissions are divided by operation (backup vs restore) and permission model (application vs delegated). Application permissions require admin consent.

#### Backup permissions

**Application permissions**

```
Mail.Read
MailboxSettings.Read
Calendars.Read
Contacts.Read
Files.Read.All
Sites.Read.All
User.Read.All
Group.Read.All
ChannelMessage.Read.All
Channel.ReadBasic.All
Team.ReadBasic.All
TeamsTab.Read.All
TeamsAppInstallation.ReadForTeam.All
Chat.Read.All
```

**Delegated permissions (Planner, OneNote, To-Do, Group Calendar)**

```
Tasks.Read
Notes.Read
Group.Read.All
```

#### Restore permissions

**Application permissions**

```
Mail.ReadWrite
MailboxSettings.ReadWrite
Calendars.ReadWrite
Contacts.ReadWrite
Files.ReadWrite.All
Sites.ReadWrite.All
User.ReadWrite.All
Group.ReadWrite.All
ChannelMessage.Send
Channel.Create
TeamsTab.ReadWrite.All
TeamsAppInstallation.ReadWriteForTeam.All
Chat.ReadWrite.All
ChatMessage.Send
```

**Delegated permissions (Planner, OneNote, To-Do, Group Calendar)**

```
Tasks.ReadWrite
Notes.ReadWrite.All
Group.ReadWrite.All
```

#### Permissions by data type

| Data type            | Backup (read)                          | Restore (write)                             | Permission model |
| -------------------- | -------------------------------------- | ------------------------------------------- | ---------------- |
| User email           | `Mail.Read`                            | `Mail.ReadWrite`                            | Application      |
| Email folders        | `Mail.Read`                            | `Mail.ReadWrite`                            | Application      |
| Mailbox rules        | `MailboxSettings.Read`                 | `MailboxSettings.ReadWrite`                 | Application      |
| Mailbox settings     | `MailboxSettings.Read`                 | `MailboxSettings.ReadWrite`                 | Application      |
| User calendar        | `Calendars.Read`                       | `Calendars.ReadWrite`                       | Application      |
| Calendar attachments | `Calendars.Read`                       | `Calendars.ReadWrite`                       | Application      |
| User contacts        | `Contacts.Read`                        | `Contacts.ReadWrite`                        | Application      |
| Contact folders      | `Contacts.Read`                        | `Contacts.ReadWrite`                        | Application      |
| Contact photos       | `Contacts.Read`                        | `Contacts.ReadWrite`                        | Application      |
| OneDrive files       | `Files.Read.All`                       | `Files.ReadWrite.All`                       | Application      |
| OneDrive folders     | `Files.Read.All`                       | `Files.ReadWrite.All`                       | Application      |
| File permissions     | `Files.Read.All`                       | `Files.ReadWrite.All`                       | Application      |
| User profile         | `User.Read.All`                        | `User.ReadWrite.All`                        | Application      |
| User photo           | `User.Read.All`                        | `User.ReadWrite.All`                        | Application      |
| SharePoint sites     | `Sites.Read.All`                       | `Sites.ReadWrite.All`                       | Application      |
| SharePoint lists     | `Sites.Read.All`                       | `Sites.ReadWrite.All`                       | Application      |
| List items           | `Sites.Read.All`                       | `Sites.ReadWrite.All`                       | Application      |
| Teams channels       | `Channel.ReadBasic.All`                | `Channel.Create`                            | Application      |
| Channel messages     | `ChannelMessage.Read.All`              | `ChannelMessage.Send`                       | Application      |
| Channel tabs         | `TeamsTab.Read.All`                    | `TeamsTab.ReadWrite.All`                    | Application      |
| Team apps            | `TeamsAppInstallation.ReadForTeam.All` | `TeamsAppInstallation.ReadWriteForTeam.All` | Application      |
| User chats           | `Chat.Read.All`                        | `Chat.ReadWrite.All`                        | Application      |
| Chat messages        | `Chat.Read.All`                        | `ChatMessage.Send`                          | Application      |
| Group membership     | `Group.Read.All`                       | `Group.ReadWrite.All`                       | Application      |
| Group settings       | `Group.Read.All`                       | `Group.ReadWrite.All`                       | Application      |
| Planner plans        | `Tasks.Read`                           | `Tasks.ReadWrite`                           | Delegated        |
| Planner buckets      | `Tasks.Read`                           | `Tasks.ReadWrite`                           | Delegated        |
| Planner tasks        | `Tasks.Read`                           | `Tasks.ReadWrite`                           | Delegated        |
| OneNote notebooks    | `Notes.Read`                           | `Notes.ReadWrite.All`                       | Delegated        |
| OneNote sections     | `Notes.Read`                           | `Notes.ReadWrite.All`                       | Delegated        |
| OneNote pages        | `Notes.Read`                           | `Notes.ReadWrite.All`                       | Delegated        |
| To-Do task lists     | `Tasks.Read`                           | `Tasks.ReadWrite`                           | Delegated        |
| To-Do tasks          | `Tasks.Read`                           | `Tasks.ReadWrite`                           | Delegated        |
| Group calendar       | `Group.Read.All`                       | `Group.ReadWrite.All`                       | Delegated        |

### Data format and storage

#### Backup data formats

| Data type       | Format   | Extension            |
| --------------- | -------- | -------------------- |
| Email content   | MIME     | `.eml`               |
| Email metadata  | JSON     | `.json`              |
| Calendar events | JSON     | `.json`              |
| Contacts        | JSON     | `.json`              |
| Contact photos  | Binary   | `.photo`             |
| Files           | Original | (original extension) |
| OneNote pages   | HTML     | `.html`              |
| All metadata    | JSON     | `.json`              |

#### Metadata structure

Each backed-up item includes metadata with common properties:

```json
{
    "o365:Type": "SourceItemType",
    "o365:Id": "Graph API ID",
    "o365:Name": "Display name",
    "o365:CreatedDateTime": "ISO 8601 timestamp",
    "o365:LastModifiedDateTime": "ISO 8601 timestamp"
}
```

#### Storage requirements

* Email: MIME content + JSON metadata per message
* Files: Original file size + metadata overhead
* Calendar: JSON per event (typically 1–10 KB)
* Contacts: JSON per contact (typically 1–5 KB)

### Cross-tenant restore

#### Supported scenarios

| Scenario                         | Supported | Notes                           |
| -------------------------------- | --------- | ------------------------------- |
| Same user, same tenant           | ✅         | Default restore behavior        |
| Different user, same tenant      | ✅         | Use `--restore-path`            |
| Same user, different tenant      | ✅         | Requires credentials for target |
| Different user, different tenant | ✅         | Full cross-tenant restore       |

#### Cross-tenant considerations

1. **User mapping**: Users referenced in data (assignments, sharing) must exist in the target tenant.
2. **Group mapping**: Groups must exist in the target tenant for group-related restores.
3. **App availability**: Teams apps must be available in the target tenant’s app catalog.
4. **Permission scope**: Application must have permissions in the target tenant.

#### Restore path syntax

If not using the UI to pick the destination, the commandline syntax looks like this:

```
--restore-path="@office365://users/{target-user-id}?office365-tenant-id=..."
--restore-path="@office365://groups/{target-group-id}?office365-tenant-id=..."
--restore-path="@office365://sites/{target-site-id}?office365-tenant-id=..."
```

### Best practices

#### Backup recommendations

1. Enable metadata storage with `--store-metadata-content-in-database`.
2. Schedule regular backups.
3. Verify required permissions before backup.

#### Restore recommendations

1. Test restore procedures regularly.
2. Verify target user/group exists before restore.
3. Use `--office365-ignore-existing` carefully; default behavior skips duplicates.
4. Review restored sharing permissions.
5. Be aware of limitations when restoring into a tenant.

#### Security considerations

1. Store client secrets securely.
2. Prefer certificates over secrets in production.
3. Request only the permissions you need.
4. Monitor backup/restore operations with audit logging.

### References

* [Microsoft Graph Permissions Reference](https://docs.microsoft.com/en-us/graph/permissions-reference)
* [Azure AD App Registration](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app)
