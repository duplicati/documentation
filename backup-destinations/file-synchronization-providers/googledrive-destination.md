---
description: This page describes the Dropbox storage destination
---

# Google Drive Destination

Duplicati supports using [Google Drive](https://workspace.google.com/products/drive/) as a storage destination. Note that Duplicati stores compressed and encrypted volumes in Google Drive and does not store files so they are individually accessible from Google Drive.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.15.23.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.15.30.png" alt="View of configuration of the Google Drive destination"></picture><figcaption></figcaption></figure>

To configure the Google Drive destination you need to pick a unique folder name for the backups, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

## URL format from Commandline

To use Google Drive, use the following URL format:

```
googledrive://<folder>/<subfolder>?authid=<authid>
```

To use Google Drive you must first obtain an `AuthID` by using a Duplicati service to log in to Google and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

## Access levels

Duplicati can work with limited access to Google Drive, where it only has access to its own files. This access is recommended, because it prevents accidents where files not relevant for Duplicati can be read or written. On the community server, this option is called "Google Drive (limited)".

Unfortunately, the security model in Google Drive sometimes resets the access, cutting off Duplicati from accessing the files it has created. If this happens, it is not currently possible to re-assign access to Duplicati.

To recover from this situation, you must download the files from Google Drive, then delete them in Google Drive, and finally re-upload the files using the [Duplicati BackendTool](../../duplicati-programs/command-line-interface-cli-1/backendtool.md).

You can also choose to [run your own OAuth server](../../detailed-descriptions/running-a-self-hosted-oauth-server.md), and you can configure this to grant Duplicati full access to all files in Google Drive.

## Team folder

If you need to use a Team Drive, set the option `--googledrive-teamdrive-id` to the ID for the Team Drive to use. If this is not set, it will use the personal Google Drive. For example:

```
googledrive://folder/subfolder?authid=<authid>&googledrive-teamdrive-id=<team id>
```
