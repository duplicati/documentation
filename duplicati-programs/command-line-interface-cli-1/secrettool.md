---
description: This page describes the Duplicati SecretTool
---

# SecretTool

The SecretTool is a small utility tool that can be used to test the [secret provider](../../detailed-descriptions/security-and-secrets/using-the-secret-provider/) configuration.

The SecretTool is called `Duplicati.CommandLine.SecretTool.exe` on Windows and `duplicati-secret-tool` on Linux and MacOS.

To use the tool, invoke it with a configuration and some secrets to locate:

```
duplicati-secret-tool test <provider url> <secret>
```

Multiple secrets can be provided and the tool will attempt to resolve each of them. See the [secret provider section](../../detailed-descriptions/security-and-secrets/using-the-secret-provider/) for details on how to use and configure the secret providers. Commandline help is also available with:

```
duplicati-secret-tool info <provider url>
```

Note that to protect the secrets, the tool will not report the actual values, but just report if it was able to obtain a value from the secret provider.

For providers that support storing secrets, the tool can also write a secret:

```
duplicati-secret-tool set <provider url> <key> [value]
```

If the value is left out, the tool asks for it twice on the console, so it does not end up in the shell history. Add `--overwrite` to replace a secret that already exists. After storing the value, the tool reads it back to check that it was stored correctly.
