---
description: This page describes the Dropbox storage destination
---

# GoogleDrive Destination

Duplicati supports using Google Drive as a storage destination. Note that Duplicati stores compressed and encrypted volumes in Google Drive and does not store files so they are individually accessible from Google Drive.

To use Google Drive, use the following URL format:

```
googledrive://<folder>/<subfolder>?authid=<authid>
```

To use Google Drive you must first obtain an `AuthID` by using a Duplicati service to log in to Google and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

## Access levels

Duplicati can work with limited access to Google Drive, where it only has access to its own files. This access is recommended, because it prevents accidents where files not relevant for Duplicati can be read or written. On the community server, this option is called "Google Drive (limited)".

Unfortunately, the security model in Google Drive sometimes resets the access, cutting off Duplicati from accessing the files it has created. If this happens, it is not currently possible to re-assign access to Duplicati, and in this case you must grant full access to the Google Drive for Duplicati to work. On the community server, this option is called "Google Drive (full access)".

## Team folder

If you need to use a Team Drive, set the option `--googledrive-teamdrive-id` to the ID for the Team Drive to use. If this is not set, it will use the personal Google Drive. For example:

```
googledrive://folder/subfolder?authid=<authid>&googledrive-teamdrive-id=<team id>
```
