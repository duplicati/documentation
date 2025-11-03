---
description: This page describes the Rclone storage destination
---

# Rclone Destination

Duplicati has a wide variety of storage destinations, but the [Rclone project](https://rclone.org) has even more! If you are familiar with Rclone, you can configure Duplicati to utilize Rclone to transfer files and extend to the full set of destinations supported by Rclone.

If you are using Rclone, some features, such as bandwidth limits and transfer progress do not work.

Duplicati does not bundle Rclone, so you need to download and install the appropriate binaries before you can use this backend.&#x20;

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.05.51.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.05.40.png" alt="View of the Rclone configuration page"></picture><figcaption></figcaption></figure>

For rclone, you need to specify the remote and path. You can use the advanced options to specify more options, including the path to the Rclone binary, if it is not in the default search path.

## URL format for Commandline

The URL format for the Rclone destination is:

```
rclone://<remote repo>/<remote path>
  ?rclone-executable=<path to rclone executable>
```

If the remote repo is not a valid hostname, you can instead use this format:

```
rclone://
  ?rclone-remote-repository=<remote repo>
  &rclone-remote-path=<remote path>
  &rclone-executable=<path to rclone executable>
```

## Advanced options

If you need to change the Rclone local repo you can use the option `--rclone-local-repository` which will otherwise be set to `local`, which works for most setups.

If you need to supply options to Rclone, these can be passed via `--rclone-option`. Note that the values must be url encoded, and multiple options can be passed by separating them with spaces, before encoding.

As an example adding "`--opt1=a --opt2=b`" needs to url encoded and results in:

```
rclone://<remote repo>/<remote path>
  ?rclone-option=--opt1%3Da%20--opt2%3Db
```
