---
description: >-
  This page describes how to create the Google Cloud service account and
  domain-wide delegation that Duplicati uses to back up and restore Google
  Workspace
---

# Google Workspace credentials setup

Duplicati accesses a Google Workspace domain with a **service account** that uses **domain-wide delegation** to act on behalf of the users in the domain. Before you can set up a backup, the service account needs to be created in a Google Cloud project, and a Google Workspace super administrator needs to authorize it in the Google Admin console.

When you are done, you have the two values that Duplicati asks for:

* **Admin email**: The email address of a Google Workspace administrator in the domain. The service account acts as this user to list the users, groups, organizational units and other resources in the domain.
* **Service account JSON**: The JSON key file for the service account.

The same steps apply whether you use the [Google Workspace backup source in Duplicati](google-workspace-backup-and-restore.md) or a [managed Google Workspace backup](../../duplicati-console/managed-google-workspace-backup.md) in the Duplicati Console.

{% hint style="info" %}
Creating the service account requires permission to create projects and service accounts in Google Cloud. Setting up domain-wide delegation requires a **super administrator** in the Google Workspace domain.
{% endhint %}

### Decide on backup and restore credentials

A backup only needs the read-only scopes, while a restore into the domain needs scopes with write access. You can choose between:

* **One service account for both backup and restore**: Simple to set up, but the key stored with the backup can also modify data in the domain.
* **Separate service accounts**: The service account used for the backup is only authorized for the read-only scopes. A second service account with the restore scopes is created, or temporarily authorized, when you need to restore. This is the most secure option.

If you restore into a **different domain**, the service account with the restore scopes must be authorized in the target domain.

### Step 1: Create or choose a Google Cloud project

1. Sign in to the [Google Cloud console](https://console.cloud.google.com) with an account in your Google Workspace organization.
2. Click the project selector at the top of the page, and either choose an existing project or click **New project**.
3. If you create a new project, give it a name, such as "Duplicati backup", choose your organization as the location, and click **Create**.

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-10-22 at 11.50.42.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 11.50.30.png" alt="The New Project form with the project name Duplicati backup and the organization selected" width="563"></picture><figcaption></figcaption></figure>

Make sure the new project is selected in the project selector before continuing.

### Step 2: Enable the APIs

Duplicati calls a number of Google APIs, and each one must be enabled in the project.

1. Go to **APIs & Services → Library**.
2. Search for each API in the list below, open it, and click **Enable**.

| API                  | Used for                                       |
| -------------------- | ---------------------------------------------- |
| Admin SDK API        | Users, groups and organizational units         |
| Gmail API            | Gmail                                          |
| Google Drive API     | Drive, shared drives and Sites                 |
| Google Calendar API  | Calendar                                       |
| People API           | Contacts                                       |
| Google Tasks API     | Tasks                                          |
| Google Keep API      | Keep                                           |
| Google Chat API      | Chat                                           |
| Groups Settings API  | Group settings                                 |

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-10-22 at 11.51.52.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 11.52.04.png" alt="The Gmail API product page with the Enable button" width="563"></picture><figcaption></figcaption></figure>

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-10-22 at 11.53.21.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 11.53.33.png" alt="The list of enabled APIs showing the nine APIs used by Duplicati"></picture><figcaption></figcaption></figure>

If you do not want to back up some types of data, you can leave out the APIs for those types, and exclude the types in the backup configuration.

### Step 3: Configure the Google Chat API

The Google Chat API refuses requests from a project that does not have a Chat app configured, even when the requests are made on behalf of users. If you back up Google Chat, configure it like this:

1. Go to **APIs & Services → Enabled APIs & services** and open **Google Chat API**.
2. Go to the **Configuration** tab.
3. Under **Application info**, enter an **App name**, such as "Duplicati backup", and a **Description**. The **Avatar URL** is optional, and must point to a square PNG image if you set it.
4. Leave the remaining settings at their defaults. Duplicati does not need the app to receive messages, so nobody needs to interact with it.
5. Click **Save** at the bottom of the page.

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-10-22 at 11.55.27.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 11.55.13.png" alt="The Configuration tab of the Google Chat API with the app name, avatar URL and description filled in" width="563"></picture><figcaption></figcaption></figure>

### Step 4: Create the service account

1. Go to **IAM & Admin → Service Accounts**.
2. Click **Create service account**.
3. Enter a **Service account name**, such as "duplicati-backup". The **Service account ID**, and with it the email address of the service account, is filled in from the name. Optionally, enter a description.
4. The service account does not need any roles in the project, so the optional **Permissions** and **Principals with access** steps can be skipped. Click **Create and close**.

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-10-22 at 11.56.08.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 11.56.20.png" alt="The Create service account form with the name duplicati-backup" width="563"></picture><figcaption></figcaption></figure>

### Step 5: Create a JSON key

1. In the list of service accounts, click the new service account.
2. Go to the **Keys** tab.
3. Click **Add key → Create new key**.
4. Choose **JSON** and click **Create**.

The JSON key file is downloaded to your computer. This file is the **Service account JSON** used in Duplicati.

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-10-22 at 11.57.00.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 11.56.48.png" alt="The Add key menu with Create new key" width="164"></picture><figcaption></figcaption></figure>

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-10-22 at 11.57.30.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 11.57.44.png" alt="The Create private key dialog with JSON selected" width="563"></picture><figcaption></figcaption></figure>

{% hint style="warning" %}
The JSON key gives access to all data in the domain that the service account is authorized for. Store it securely, do not commit it to source control, and delete the downloaded file once it has been added to Duplicati. A lost key cannot be downloaded again, but you can create a new key and delete the old one.
{% endhint %}

{% hint style="info" %}
Google Cloud organizations created after May 2024 enforce the organization policy **Disable service account key creation** (`iam.disableServiceAccountKeyCreation`) by default. If creating the key fails with a message that key creation is disabled, an organization policy administrator needs to make an exception for the project under **IAM & Admin → Organization Policies**.
{% endhint %}

### Step 6: Note the client ID of the service account

1. Go to the **Details** tab of the service account.
2. Copy the **Unique ID**. This is the OAuth 2 client ID of the service account, a number with about 21 digits.

The client ID is also in the JSON key file as `client_id`.

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-10-22 at 11.58.38.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 11.58.25.png" alt="The Details tab of the service account showing the Unique ID" width="563"></picture><figcaption></figcaption></figure>

### Step 7: Authorize domain-wide delegation

1. Sign in to the [Google Admin console](https://admin.google.com) as a super administrator.
2. Go to **Security → Access and data control → API controls**.
3. Under **Domain wide delegation**, click **Manage domain wide delegation**.

<figure><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 12.00.57.png" alt="The API controls page in the Google Admin console"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 12.01.38.png" alt="The list of API clients with the Add new link"><figcaption></figcaption></figure>

4. In the list of API clients, click **Add new**.
5. In **Client ID**, enter the unique ID of the service account from step 6.
6. In **OAuth scopes (comma-delimited)**, paste the list of scopes that matches how the service account is used (see below).
7. Click **Authorize**.

<figure><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 12.01.30.png" alt="The Add a new client ID dialog with the client ID entered" width="476"><figcaption></figcaption></figure>

The service account is now listed among the API clients with the client ID and the authorized scopes. Click **View details** to see the full list of scopes. In the example below, the domain uses separate service accounts for backup and restore: the first is authorized for the 12 restore scopes, and the second for the 14 backup scopes.

<figure><img src="../../.gitbook/assets/Screenshot 2026-10-22 at 12.02.08.png" alt="The list of API clients with domain-wide delegation"><figcaption></figcaption></figure>

Duplicati requests the read-only scopes when making a backup and the write scopes when restoring, so a service account used for both needs both sets of scopes:

{% tabs %}
{% tab title="Backup only" %}
```
https://www.googleapis.com/auth/gmail.readonly,https://www.googleapis.com/auth/admin.directory.user.readonly,https://www.googleapis.com/auth/admin.directory.group.readonly,https://www.googleapis.com/auth/admin.directory.orgunit.readonly,https://www.googleapis.com/auth/calendar.readonly,https://www.googleapis.com/auth/calendar,https://www.googleapis.com/auth/drive.readonly,https://www.googleapis.com/auth/contacts.readonly,https://www.googleapis.com/auth/tasks.readonly,https://www.googleapis.com/auth/keep.readonly,https://www.googleapis.com/auth/apps.groups.settings,https://www.googleapis.com/auth/chat.spaces.readonly,https://www.googleapis.com/auth/chat.messages.readonly,https://www.googleapis.com/auth/chat.memberships.readonly
```
{% endtab %}

{% tab title="Restore only" %}
```
https://www.googleapis.com/auth/gmail.modify,https://www.googleapis.com/auth/admin.directory.user,https://www.googleapis.com/auth/admin.directory.group,https://www.googleapis.com/auth/admin.directory.orgunit,https://www.googleapis.com/auth/calendar,https://www.googleapis.com/auth/drive,https://www.googleapis.com/auth/contacts,https://www.googleapis.com/auth/tasks,https://www.googleapis.com/auth/keep,https://www.googleapis.com/auth/apps.groups.settings,https://www.googleapis.com/auth/chat.messages,https://www.googleapis.com/auth/chat.spaces
```
{% endtab %}

{% tab title="Backup and restore" %}
```
https://www.googleapis.com/auth/gmail.readonly,https://www.googleapis.com/auth/admin.directory.user.readonly,https://www.googleapis.com/auth/admin.directory.group.readonly,https://www.googleapis.com/auth/admin.directory.orgunit.readonly,https://www.googleapis.com/auth/calendar.readonly,https://www.googleapis.com/auth/drive.readonly,https://www.googleapis.com/auth/contacts.readonly,https://www.googleapis.com/auth/tasks.readonly,https://www.googleapis.com/auth/keep.readonly,https://www.googleapis.com/auth/chat.spaces.readonly,https://www.googleapis.com/auth/chat.messages.readonly,https://www.googleapis.com/auth/chat.memberships.readonly,https://www.googleapis.com/auth/gmail.modify,https://www.googleapis.com/auth/admin.directory.user,https://www.googleapis.com/auth/admin.directory.group,https://www.googleapis.com/auth/admin.directory.orgunit,https://www.googleapis.com/auth/calendar,https://www.googleapis.com/auth/drive,https://www.googleapis.com/auth/contacts,https://www.googleapis.com/auth/tasks,https://www.googleapis.com/auth/keep,https://www.googleapis.com/auth/apps.groups.settings,https://www.googleapis.com/auth/chat.messages,https://www.googleapis.com/auth/chat.spaces
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
The backup list includes the full `calendar` scope, which is needed to read the access control lists (sharing settings) of calendars. If you prefer to grant only read-only scopes for the backup, remove `https://www.googleapis.com/auth/calendar` from the list and add the advanced option `--google-ignore-calendar-acl` to the backup.
{% endhint %}

See the [API permissions reference](google-workspace-backup-and-restore.md#api-permissions-reference) for what each scope is used for, and which scopes are needed for each data type. If you leave out a type of data, you can also leave out its scopes.

It usually takes a few minutes before the authorization is active, but in some cases it can take up to 24 hours.

### Step 8: Choose the admin email

Duplicati needs the email address of a user in the domain that can read the directory. The service account acts as this user when it lists the users, groups, organizational units and shared drives in the domain.

* Use the email address of a **super administrator**, or of an administrator with an admin role that can read users, groups and organizational units.
* Use the email address of a real user in the domain, not the `client_email` of the service account.
* For restores into the directory, such as restoring users, groups or organizational units, the administrator must also be allowed to create and update those objects.

### Step 9: Use the credentials in Duplicati

Enter the admin email and the contents of the JSON key file in Duplicati:

* For the Duplicati backup source, follow [Configure Google Workspace backup](google-workspace-backup-and-restore.md#configure-google-workspace-backup).
* For a managed backup, follow [Create a managed backup](../../duplicati-console/managed-google-workspace-backup.md#create-a-managed-backup).

Use the test function to check that the credentials work. In the Duplicati Console, the **Test permissions** function shows which of the required scopes are authorized for the service account, and which are missing.

### Troubleshooting

| Problem                                                          | Solution                                                                                                                                                                                    |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `unauthorized_client: Client is unauthorized to retrieve access tokens using this method, or client not authorized for any of the scopes requested` | Domain-wide delegation is missing, or a requested scope is not in the authorized list. Check that the client ID matches the service account, and that every scope is listed exactly as above. Wait a few minutes after changes. |
| `invalid_grant: Invalid email or User ID`                       | The admin email is not a user in the domain, or the user is suspended.                                                                                                                      |
| `invalid_grant: account not found` or invalid JWT signature     | The JSON key has been deleted or belongs to a different service account. Create a new key and update the backup configuration.                                                              |
| `Not Authorized to access this resource/api` for the directory   | The admin email belongs to a user without admin rights to read the directory. Use a super administrator.                                                                                   |
| `... API has not been used in project ... or it is disabled`     | Enable the API mentioned in the error message in the Google Cloud project that the service account belongs to.                                                                            |
| `Google Chat app not found`                                      | Configure the Google Chat API as described in [step 3](#step-3-configure-the-google-chat-api).                                                                                              |
| Key creation is disabled by an organization policy               | Make an exception for the `iam.disableServiceAccountKeyCreation` policy for the project, as described in [step 5](#step-5-create-a-json-key).                                               |
