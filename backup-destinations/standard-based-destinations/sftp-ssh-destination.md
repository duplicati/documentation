---
description: This page describes the SFTP (SSH) storage destination
---

# SFTP (SSH) Destination

The SFTP destination is using the ubiquitous SSH system to implement a secure file transfer service. Using SSH allows secure logins with keys and is generally a secure way to connect to another system. The SSH connection is implemented with [Renci SSH.Net](https://github.com/sshnet/SSH.NET).

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.44.44.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.44.52.png" alt="View of the SSH connection configuration screen"></picture><figcaption></figcaption></figure>

To use the SFTP destination you must enter at least the shown information: server, port, folder path, and username. You must most likely also provide either a password or an SSH private key through the advanced options.

## URL format for Commandline

To use the SFTP destination you can use a URL such as:

```
ssh://<hostname>/<path>
  ?auth-username=<username>
  &auth-password=<password>
  &ssh-fingerprint=<fingerprint>
```

You can supply a non-standard port through the hostname, such as `ssh://hostname:2222/folder`.

## Using key-based authentication

It is very common, and more secure, to use key-based authentication, and Duplicati supports this as well. You can either provide the entire key as part of the URL or give a path to the key file. If the key is encrypted, you can supply the encryption key with `--auth-password`.

Starting with Duplicati 2.2 it is now possible to provide an SSH private key file with the option `--ssh-keyfile=/path/to/file` or an inline key with the option `--ssh-key=url-encoded-key` . In the user interface you can drop the file with the private key, or paste in the contents.

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.45.32.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.45.25.png" alt="Advanced options shown for the SFTP backend"></picture><figcaption></figcaption></figure>

## Using key-based authentication from the Commandline

To use a private key inline, you need to url encode it first and then pass it to `--ssh-key`. An example with an inline private key:

```
ssh://server/home/backup
  ?ssh-key=sshkey%3A%2F%2F----%20BEGIN%20SSH2%20PRIVATE%20KEY%20----...
  &auth-username=user
  &ssh-fingerprint=<fingerprint>
```

Note that you need both the prefix `sshkey://` and you need to URL encode the contents.

If you have the SSH keyfile installed in your home folder, you can use the file directly with `--ssh-keyfile`:

```
ssh://server/home/backup
  ?ssh-keyfile=/home/user/.ssh/keyfile
  &auth-username=user
  &auth-password=<keyfile password>
  &ssh-fingerprint=<fingerprint>
```

Note that Duplicati does not currently support key agents so you must pass the password here.

For best security it is recommended to use a separate identity and key files for the user, so a compromise of the keys does not grant more permissions than what is required.

## Validating the host key

Since SSH does not have a global key registry, like for HTTPS, it is possible to launch a man-in-the-middle attack on an SSH connection. To prevent this, Duplicati and other SSH clients will use certificate pinning where the previously recorded host certificate hash is saved and changes to the host certificate must be manually handled by the user.

On the first connection to the SSH server, Duplicati will throw an exception that explains how to trust the server host key, including the host key fingerprint. Once you obtain the host key fingerprint, you can supply it with the `--ssh-fingerprint` option.

If the host key changes, you will get a different message, but also reporting the new host key, so you can update it. The option `--ssh-accept-any-fingerprints=true` is only recommended for testing and not for production setups as it will disable the man-in-the-middle protection.

If you are using the UI, you can click the "Test connection" button and it will guide you to set the host key parameters based on what the server reports.

## Timeout and keep-alive

By default, Duplicati will assume that the connection works once it has been established. If the SSH server is malfunctioning it may cause operations to hang. To guard against this case, you can set the `--ssh-operation-timeout` option to enforce a maximum time the operation may take.&#x20;

A different kind of timeout is when firewalls and other network equipment monitors the connections and closes them if there is no activity. Because Duplicati may open a connection and then perform a long operation locally, it may cause the connection to be closed due to inactivity. The option `--ssh-keepalive` can be used to define a keep-alive interval where messages are sent if there is no other activity.

Both options are default disabled and should only be enabled if there are special conditions in a setup where the options are needed.

