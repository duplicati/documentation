---
description: This page describes the
---

# Command Line Tools

Besides the general [commandline interface](../command-line-interface-cli.md), Duplicati ships with a number of supporting commandline tools. Besides ServerUtil, each of the tools are intended to be used in special circumstances, outside the expected normal operation of Duplicati.

[BackendTester](backendtester.md), [Snapshots](snapshots.md), [AutoUpdater](autoupdater.md), and [SecretTool](secrettool.md) are intended to be used for testing functionality on the actual setup, ahead of making changes or running backups.

The [BackendTool](backendtool.md) and [SharpAESCrypt](sharpaescrypt.md) tools are intended to work directly with the remote storage files.

The [RecoveryTool](recoverytool.md) can work directly with the remote storage without using the regular Duplicati code, and can both recover files from a damaged remote destination, but also re-upload existing files.
