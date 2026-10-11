---
description: This page describes how to add error-correction data to the remote volumes
---

# Parity error correction

{% hint style="info" %}
Parity error correction is available from Duplicati 2.4.0.0.
{% endhint %}

Files on a storage destination can be damaged, for instance by bit rot on a disk or by an error in the storage service. Duplicati detects damaged volumes, but cannot repair them on its own. With a parity module, Duplicati stores extra error-correction data next to the backup volumes, so a damaged volume can be repaired when it is downloaded.

Parity is not enabled by default. The only parity module in Duplicati 2.4 is `par2`, which uses the [PAR2](https://github.com/Parchive/par2cmdline) format and requires the `par2` (par2cmdline) program to be installed. On Linux and MacOS it can usually be installed with the package manager.

## Enabling parity

Set the parity module as an advanced option on the backup:

```
--parity-module=par2
```

When a data volume (`dblock`) or a filelist (`dlist`) is uploaded, Duplicati also creates and uploads a companion file with the same name and the extension `.par2`. Index files (`dindex`) do not get parity data. The companion file is deleted together with its volume.

When a downloaded volume fails the integrity check, for instance during a restore or compact, Duplicati uses the companion file to try to repair the downloaded copy before using it. The verification after a backup and the `test` command do not repair, so that damaged volumes are still reported.

## Options

| Option | Default | Description |
| --- | --- | --- |
| `--parity-module` | (empty) | The parity module to use. Set to `par2` to enable parity. |
| `--parity-redundancy-level` | `5` | The amount of error-correction data, as a percentage of the volume size. Higher values can repair more damage, but use more storage and upload time. |
| `--par2-program-path` | | The path to the `par2` program, if it is not on the system path. |
| `--par2-extra-options` | | Extra commandline options for `par2` when creating parity data. |

Only volumes uploaded after parity was enabled get a companion file.
