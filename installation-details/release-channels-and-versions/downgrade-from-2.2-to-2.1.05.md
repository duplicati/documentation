---
description: This page describes how to downgrade from Duplicati 2.1.0.5 to 2.0.8.1
---

# Downgrade from 2.2 to 2.1.05

Before you downgrade, you need to downgrade the databases to the format that is expected by Duplicati 2.1.0.5. If you do not do this, Duplicati will not start saying that the database version is unsupported.

{% hint style="info" %}
Note: make sure you run the DatabaseTool **before** you downgrade Duplicati.
{% endhint %}

First, make sure Duplicati is not running. You can then use the [DatabaseTool](../../duplicati-programs/command-line-interface-cli-1/databasetool.md) to downgrade from version 2.2 to version 2.1.0.5:

```
duplicati-database-tool downgrade --server-version=8 --local-version=14
```

After the downgrade is complete, uninstall Duplicati 2.2 and install 2.1.0.5.

## Obtaining older releases

The [installer packages for 2.1.0.5](https://github.com/duplicati/duplicati/releases/tag/v2.0.8.1-2.0.8.1_beta_2024-05-07) are available on Github. You can [browse the list of releases ](https://github.com/duplicati/duplicati/releases)for other versions you may want.
