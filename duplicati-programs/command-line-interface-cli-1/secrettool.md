---
description: This page describes the Duplicati SecretTool
---

# SecretTool

The SecretTool is a small utility tool that can be used to test the [secret provider](../../detailed-descriptions/using-the-secret-provider/) configuration.

The SecretTool is called `Duplicati.CommandLine.SecretTool.exe` on Windows and `duplicati-secret-tool` on Linux and MacOS.

To use the tool, invoke it with a configuration and some secrets to locate:

```
duplicati-secret-tool test <provider url> <secret>
```

Multiple secrets can be provided and the tool will attempt to resolve each of them. See the [secret provider section](../../detailed-descriptions/using-the-secret-provider/) for details on how to use and configure the secret providers. Commandline help is also available with:

```
duplicati-secret-tool info <provider url>
```

Note that to protect the secrets, the tool will not report the actual values, but just report if it was able to obtain a value from the secret provider.
