---
description: This page describes how Microsoft 365 backup and restore works with Duplicati
---

# Microsoft 365 backup and restore

Duplicati supports backing up and restoring Microsoft 365 (Office 365) data through the Microsoft Graph API. The backup workflow captures content in native formats (for example, MIME for email and JSON for structured objects), and restore operations re-create items through the appropriate Graph API endpoints.

{% hint style="info" %}
Microsoft 365 backup and restore was added in Stable 2.3
{% endhint %}

{% hint style="warning" %}
The Microsoft 365 backup feature has a limit that covers up to **5 mailboxes or sites**. The license counter excludes unlicensed shared mailboxes. The Office 365 backup feature is source-available, but **not open-source** like the rest of Duplicati. There are no limitations on restore.

A license is required to use Microsoft 365 backup in production. Contact Duplicati Inc. support or sales to obtain a license.
{% endhint %}

### Overview

**Key characteristics**

* **Format preservation**: Data is stored in native formats such as MIME, JSON, and HTML.
* **Metadata retention**: Original timestamps, identifiers, and properties are preserved where possible.
* **Restore to local disk**: It is possible to restore all data to a local destination for forensics or manual investigation.
* **Cross-tenant support**: Data can be restored into different tenants.
* **Cross-target support**: Data from one user/group/site can be restored into another.
* **Shared mailboxes**: Shared mailboxes within the tenant are detected and enumerated automatically. Unlicensed shared mailboxes do not count toward the license limit.
* **SharePoint subsites**: Subsites beneath a site collection are included in the backup.
* **Classification filtering**: Users, sites and groups can be filtered at the top level based on their classification using `--office365-included-user-classifications`, `--office365-included-site-classifications`, and `--office365-included-group-classifications`.

### Configure Microsoft 365 backup

To add a Microsoft 365 tenant to the backup, [set up a backup following the regular steps](../../getting-started/set-up-a-backup-in-the-ui.md). Then on the source page, click "Add remote path" in the top right:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-05-25 at 11.00.52.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-05-25 at 11.00.14.png" alt=""></picture><figcaption></figcaption></figure>

Type "365" in the search bar, or scroll down and find "Microsoft 365" in the list and click "Choose":

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-05-25 at 11.01.06.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-05-25 at 11.00.29.png" alt="" width="375"></picture><figcaption></figcaption></figure>

Fill in the information to set up the configuration. You need a Tenant Id, Client Id and Client Secret for the regular setup. See [Microsoft 365 credentials setup](microsoft-365-credentials-setup.md) for how to create them:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-05-25 at 11.01.13.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-05-25 at 11.00.40.png" alt="" width="563"></picture><figcaption></figcaption></figure>

Remember to test the configuration before clicking "Use remote source". You can also add the advanced options if you want to limit the content that is backed up. By default, everything is backed up:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-05-25 at 11.06.29.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-05-25 at 11.06.08.png" alt="" width="563"></picture><figcaption></figcaption></figure>

### Microsoft 365 configuration options

To use the Microsoft 365 backup source you must supply a tenant ID and either a client secret or a certificate. When choosing what to back up, it is possible to filter on types (users, groups, etc), and possible to apply filters to obtain fine-grained exclusion of data.

When making backups of a Microsoft 365 tenant, the advanced option `--store-metadata-content-in-database` must be activated.

#### Authentication methods

| Method                                   | Description                                         | Use case                       |
| ---------------------------------------- | --------------------------------------------------- | ------------------------------ |
| Client secret                            | App client credentials with secret                  | Automated or unattended backup |
| Certificate                              | OAuth 2.0 client credentials with X.509 certificate | Higher security environments   |
| Delegated user (not currently supported) | OAuth 2.0 credentials for an AD super admin         | Not available, all access uses application permissions |

#### Connection configuration settings

<table><thead><tr><th width="299.38671875">Parameter</th><th width="321.6640625">Description</th><th>Required for</th></tr></thead><tbody><tr><td><code>--office365-tenant-id</code></td><td>Azure AD tenant ID (GUID or domain)</td><td>All</td></tr><tr><td><code>--office365-client-id</code></td><td>Azure AD application (client) ID</td><td>Client secret</td></tr><tr><td><code>--office365-client-secret</code></td><td>Client secret for authentication</td><td>Client secret</td></tr><tr><td><code>--office365-certificate-path</code></td><td>Path to X.509 certificate (alternative to secret)</td><td>Certificate</td></tr><tr><td><code>--office365-certificate-password</code></td><td>Certificate password if encrypted</td><td>Certificate</td></tr><tr><td><code>--office365-graph-base-url</code></td><td>Graph API base URL (default: <code>https://graph.microsoft.com</code>)</td><td>Sovereign clouds</td></tr><tr><td><code>--office365-scope</code></td><td>Set the permission scope (default: <code>https://graph.microsoft.com/.default</code></td><td>Custom permissions</td></tr></tbody></table>

#### Additional settings

| Parameter                          | Description                                                                                                                                                                                                                                                                                                                                         |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--office365-included-root-types`            | The different root types to include for backups. The default setting is to include all types: `Users`, `Groups` and `Sites`                                                                                                                                                                                                                         |
| `--office365-included-user-types`            | <p>The data types to include from users in backups. The default settings include: <code>Profile</code>, <code>Mailbox</code>, <code>Calendar</code>, <code>Contacts</code>, <code>Planner</code> and <code>Chats</code>. <br><br>Not included by default, but can be added: <code>Tasks</code> and <code>Notes</code></p> |
| `--office365-included-group-types`           | <p>The data types to include from groups in backups. The default settings include: <code>Mailbox</code>, <code>Files</code>, <code>Planner</code> and <code>Teams</code>.<br><br>Not included by default, but can be added: <code>Calendar</code> and <code>Notes</code></p>                                              |
| `--office365-included-user-classifications`  | <p>Top-level filter for users based on classification. A comma-separated list (flags) of: <code>Licensed</code>, <code>Unlicensed</code>, <code>SharedMailboxWithStorage</code>, <code>SharedMailboxWithoutStorage</code>. When set, only users matching the classification are included.</p>                                                      |
| `--office365-included-group-classifications` | Top-level filter for groups based on classification. A comma-separated list (flags) of: `Unified` (Microsoft 365 group), `NotUnified` (security group or distribution list). When set, only groups matching the classification are included.                                                                                                       |
| `--office365-included-site-classifications`  | Top-level filter for sites based on classification. A comma-separated list (flags) of: `Group` (group-connected team site), `Classic` (non-group team site), `Communication` (modern communication site), `Personal` (OneDrive for Business site), `Other`. When set, only sites matching the classification are included.                       |
| `--office365-ignore-existing`                | When restoring data into a tenant the default is to check for existing data to avoid creating duplicates. Use this option to always recreate data in the destination.                                                                                                                                                                               |

#### Permission types

* **Application permissions**: All backup and restore operations use Microsoft Graph application permissions, obtained through the OAuth 2.0 client credentials flow. Application permissions require admin consent.
* **Delegated permissions**: Not used. Duplicati does not sign in as a user, so no delegated permissions need to be granted.

The full list of required permissions is in the [API permissions reference](office-365-backup-and-restore.md#api-permissions-reference).

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
| OneNote notebooks     | ✅      | ✅            | Not included by default, add `Notes` to the included user/group types                        |
| OneNote sections      | ✅      | ✅            | Including section groups                                                                     |
| OneNote pages         | ✅      | ✅            | HTML content                                                                                 |
| To-Do task lists      | ✅      | ✅            | Not included by default, add `Tasks` to the included user types                              |
| To-Do tasks           | ✅      | ✅            | Including checklist items                                                                    |
| User profile          | ✅      | ✅            | Photo and editable properties                                                                |
| User chats            | ✅      | ❌            | Backup only. Chats can be restored to a local disk, but not into a tenant                    |
| Chat messages         | ✅      | ❌            | Backup only, including hosted content (images)                                               |
| Teams channels        | ✅      | ✅            | Standard and private channels                                                                |
| Channel messages      | ✅      | ✅            | Including replies. Restored in migration mode, preserving sender and timestamp               |
| Channel tabs          | ✅      | ✅            | Tab configuration preserved                                                                  |
| Team apps             | ✅      | ✅            | App installation restored                                                                    |
| Group conversations   | ✅      | ⚠️           | <p>Threads and posts<br>Restore requires MS whitelist</p>                                    |
| Group calendar        | ❌      | ❌            | Not supported. The Graph API only allows group calendar access with delegated permissions    |
| Group members         | ✅      | ✅            | Membership restored                                                                          |
| Group owners          | ✅      | ✅            | Ownership restored                                                                           |
| Group settings        | ✅      | ✅            | Configuration properties                                                                     |
| SharePoint sites      | ✅      | ✅            | Document libraries, including subsites beneath site collections                |
| SharePoint lists      | ✅      | ✅            | Including list items                                                         |
| List item attachments | ✅      | ✅            | File attachments                                                             |
| Shared mailboxes      | ✅      | ✅            | Detected and enumerated within the tenant; excluded from license count unless licensed |

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

{% hint style="info" %}
OneNote is not included in backups by default. Add `Notes` to `--office365-included-user-types` and/or `--office365-included-group-types` to include it.
{% endhint %}

**Backup**

* Notebooks, section groups, sections enumerated.
* Pages exported as HTML content.

**Restore**

* Notebooks created in the user’s OneDrive.
* Sections created within notebooks.
* Pages restored as HTML.

#### To-Do tasks

{% hint style="info" %}
To-Do is not included in backups by default. Add `Tasks` to `--office365-included-user-types` to include it.
{% endhint %}

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

* Chat conversations and members enumerated.
* Messages captured with content.
* Hosted content (images) backed up.
* Requires `Chat.Read.All`.

**Restore**

* Restoring chats into a tenant is not supported.
* Chats can be restored to a local disk for inspection.

#### Teams channels

**Backup**

* Standard and private channels enumerated.
* Channel properties captured.
* Messages and replies backed up.

**Restore**

* Channels created if not existing. Requires `Channel.Create`.
* Existing channels reused by name match.
* Messages and replies are imported in migration mode, which preserves the original sender and timestamp. Requires `Teamwork.Migrate.All`.
* The target channel is put into migration mode for the duration of the restore, and migration is completed when the restore finishes.
* Existing messages are read to avoid duplicates, matching on sender and timestamp. Requires `ChannelMessage.Read.All`.
* If the restore target is a team rather than a specific channel, and the original channel cannot be determined, messages are placed in a channel named "Restored".
* Hosted content (inline images) is not restored.

#### SharePoint

**Document libraries**

* Files and folders captured and restored similarly to OneDrive.
* Subsites beneath a site collection are included in the backup.

**Lists**

* Lists enumerated with schema.
* List items captured with attachments.
* Lists and items restored with field values and attachments.

### Technical limitations

#### API limitations

| Limitation            | Description                                         | Impact                                                                    |
| --------------------- | --------------------------------------------------- | ------------------------------------------------------------------------- |
| Chat restore          | Chats cannot be restored into a tenant              | Chats are backup only, restore to local disk instead                      |
| Channel message media | Cannot restore inline images                        | Hosted content lost on restore                                            |
| Group calendar        | Graph only supports delegated permissions           | Group calendars cannot be backed up or restored                           |
| Planner plan creation | Plans cannot be created via API                     | Plans must pre-exist                                                      |
| File versions         | Only current version backed up                      | Historical versions not available                                         |
| Soft-deleted items    | Not captured in backup                              | Recently deleted items excluded                                           |
| Rate limiting         | Graph API throttling (429 responses)                | Automatic retry with backoff                                              |

#### Types excluded by default

| Feature                  | Default  | How to enable                                              |
| ------------------------ | -------- | ---------------------------------------------------------- |
| To-Do (user `Tasks`)     | Excluded | Add `Tasks` to `--office365-included-user-types`           |
| OneNote (user `Notes`)   | Excluded | Add `Notes` to `--office365-included-user-types`           |
| OneNote (group `Notes`)  | Excluded | Add `Notes` to `--office365-included-group-types`          |
| Group calendar           | Excluded | Not supported. The Graph API only allows delegated permissions |
| All others               | Included | Remove the type from the included types to exclude it      |

#### Data fidelity limitations

{% hint style="info" %}
For restores into a Microsoft 365 tenant, data is technically "created again" as the API does not support restoring content. Visually the data looks the same but internal values, such as timestamps or references may be different in the restored version.
{% endhint %}

| Data type | Limitation                                                   |
| --------- | ------------------------------------------------------------ |
| Email     | Original message ID preserved, but internal Graph ID changes |
| Calendar  | Online meeting links preserved but not recreated             |
| Contacts  | Contact ID changes on restore                                |
| Files     | File ID changes; hash and timestamps preserved               |
| Messages  | Sender and timestamp preserved, but message ID changes       |

### API permissions reference

All permissions are Microsoft Graph **application permissions** granted to the app registration, and they require admin consent. Read permissions are only needed for backup. Restore requires the corresponding write permissions, and a granted write permission also covers its read counterpart, so an app registration used for both backup and restore only needs the restore list plus the backup-only Teams permissions.

Duplicati reads the granted permissions from the `roles` claim of the access token, so no additional Graph calls are needed to verify them.

#### Backup permissions

```
User.Read.All
Group.Read.All
Mail.Read
MailboxSettings.Read
Calendars.Read
Contacts.Read
Files.Read.All
Sites.Read.All
Notes.Read.All
Tasks.Read.All
TeamMember.Read.All
ChannelMessage.Read.All
Chat.Read.All
```

#### Restore permissions

```
User.ReadWrite.All
Group.ReadWrite.All
Mail.ReadWrite
MailboxSettings.ReadWrite
Calendars.ReadWrite
Contacts.ReadWrite
Files.ReadWrite.All
Sites.ReadWrite.All
Notes.ReadWrite.All
Tasks.ReadWrite.All
Channel.Create
ChannelMessage.Read.All
Teamwork.Migrate.All
```

{% hint style="info" %}
The Teams-specific permissions `Team.ReadBasic.All`, `Channel.ReadBasic.All`, `TeamsTab.Read.All`, `TeamsAppInstallation.ReadForTeam.All`, `TeamsTab.ReadWrite.All`, and `TeamsAppInstallation.ReadWriteForTeam.All` are the least-privileged permissions for reading teams, channels, tabs, and installed apps, and for creating tabs and installing apps. They are not required, because Microsoft Graph also accepts `Group.Read.All` / `Group.ReadWrite.All` for those endpoints, and the group permissions are already needed for group backup and restore.
{% endhint %}

{% hint style="info" %}
Channel messages are restored in migration mode, which requires `Teamwork.Migrate.All`. `ChannelMessage.Read.All` is required for both backup and restore, because the restore reads existing messages to detect duplicates. `Chat.Read.All` is only needed for backup, as user chats cannot be restored into a tenant.
{% endhint %}

#### Permission details

| Permission                  | Backup | Restore | Used for                                                                       |
| --------------------------- | ------ | ------- | ------------------------------------------------------------------------------ |
| `User.Read.All`             | ✅      |         | Read user accounts, profiles, photos, and license assignments                  |
| `User.ReadWrite.All`        |        | ✅       | Update user profiles, such as restoring the profile photo                      |
| `Group.Read.All`            | ✅      |         | Read groups, their members, owners, and conversations                          |
| `Group.ReadWrite.All`       |        | ✅       | Create groups, and manage members, owners, and conversations                   |
| `Mail.Read`                 | ✅      |         | Read mailbox folders, messages, attachments, and inbox rules                   |
| `Mail.ReadWrite`            |        | ✅       | Create and update mailbox folders, messages, attachments, and inbox rules      |
| `MailboxSettings.Read`      | ✅      |         | Read user mailbox settings                                                     |
| `MailboxSettings.ReadWrite` |        | ✅       | Update user mailbox settings                                                   |
| `Calendars.Read`            | ✅      |         | Read user calendars and events                                                 |
| `Calendars.ReadWrite`       |        | ✅       | Create and update calendars, events, and event attachments                     |
| `Contacts.Read`             | ✅      |         | Read contact folders and contacts                                              |
| `Contacts.ReadWrite`        |        | ✅       | Create and update contact folders, contacts, and contact photos                |
| `Files.Read.All`            | ✅      |         | Read OneDrive drives, files, and sharing permissions                           |
| `Files.ReadWrite.All`       |        | ✅       | Create and update OneDrive files and sharing permissions                       |
| `Sites.Read.All`            | ✅      |         | Read SharePoint sites, lists, and list items                                   |
| `Sites.ReadWrite.All`       |        | ✅       | Create and update SharePoint sites, lists, and list items                      |
| `Notes.Read.All`            | ✅      |         | Read OneNote notebooks, sections, and pages                                    |
| `Notes.ReadWrite.All`       |        | ✅       | Create and update OneNote notebooks, sections, and pages                       |
| `Tasks.Read.All`            | ✅      |         | Read Planner plans, buckets, tasks, and To Do lists                            |
| `Tasks.ReadWrite.All`       |        | ✅       | Create and update Planner plans, buckets, tasks, and To Do lists               |
| `TeamMember.Read.All`       | ✅      |         | Read team memberships                                                          |
| `Channel.Create`            |        | ✅       | Create channels when restoring                                                 |
| `ChannelMessage.Read.All`   | ✅      | ✅       | Read Teams channel messages and replies. Restore reads messages to detect duplicates |
| `Chat.Read.All`             | ✅      |         | Read Teams chat messages                                                       |
| `Teamwork.Migrate.All`      |        | ✅       | Restore Teams channels and channel messages in migration mode                  |

#### Permissions by data type

| Data type            | Backup (read)             | Restore (write)                                     |
| -------------------- | ------------------------- | --------------------------------------------------- |
| User email           | `Mail.Read`               | `Mail.ReadWrite`                                    |
| Email folders        | `Mail.Read`               | `Mail.ReadWrite`                                    |
| Mailbox rules        | `Mail.Read`               | `Mail.ReadWrite`                                    |
| Mailbox settings     | `MailboxSettings.Read`    | `MailboxSettings.ReadWrite`                         |
| User calendar        | `Calendars.Read`          | `Calendars.ReadWrite`                               |
| Calendar attachments | `Calendars.Read`          | `Calendars.ReadWrite`                               |
| User contacts        | `Contacts.Read`           | `Contacts.ReadWrite`                                |
| Contact folders      | `Contacts.Read`           | `Contacts.ReadWrite`                                |
| Contact photos       | `Contacts.Read`           | `Contacts.ReadWrite`                                |
| OneDrive files       | `Files.Read.All`          | `Files.ReadWrite.All`                               |
| OneDrive folders     | `Files.Read.All`          | `Files.ReadWrite.All`                               |
| File permissions     | `Files.Read.All`          | `Files.ReadWrite.All`                               |
| User profile         | `User.Read.All`           | `User.ReadWrite.All`                                |
| User photo           | `User.Read.All`           | `User.ReadWrite.All`                                |
| SharePoint sites     | `Sites.Read.All`          | `Sites.ReadWrite.All`                               |
| SharePoint lists     | `Sites.Read.All`          | `Sites.ReadWrite.All`                               |
| List items           | `Sites.Read.All`          | `Sites.ReadWrite.All`                               |
| Teams channels       | `Group.Read.All`          | `Channel.Create`                                    |
| Channel messages     | `ChannelMessage.Read.All` | `Teamwork.Migrate.All`, `ChannelMessage.Read.All`   |
| Channel tabs         | `Group.Read.All`          | `Group.ReadWrite.All`                               |
| Team apps            | `Group.Read.All`          | `Group.ReadWrite.All`                               |
| Team memberships     | `TeamMember.Read.All`     | `Group.ReadWrite.All`                               |
| User chats           | `Chat.Read.All`           | Not supported                                       |
| Chat messages        | `Chat.Read.All`           | Not supported                                       |
| Group conversations  | `Group.Read.All`          | `Group.ReadWrite.All`                               |
| Group membership     | `Group.Read.All`          | `Group.ReadWrite.All`                               |
| Group settings       | `Group.Read.All`          | `Group.ReadWrite.All`                               |
| Group calendar       | Not supported             | Not supported                                       |
| Planner plans        | `Tasks.Read.All`          | `Tasks.ReadWrite.All`                               |
| Planner buckets      | `Tasks.Read.All`          | `Tasks.ReadWrite.All`                               |
| Planner tasks        | `Tasks.Read.All`          | `Tasks.ReadWrite.All`                               |
| OneNote notebooks    | `Notes.Read.All`          | `Notes.ReadWrite.All`                               |
| OneNote sections     | `Notes.Read.All`          | `Notes.ReadWrite.All`                               |
| OneNote pages        | `Notes.Read.All`          | `Notes.ReadWrite.All`                               |
| To-Do task lists     | `Tasks.Read.All`          | `Tasks.ReadWrite.All`                               |
| To-Do tasks          | `Tasks.Read.All`          | `Tasks.ReadWrite.All`                               |

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
