---
description: >-
  This page describes the providers that operate locally on the machine they are
  running
---

# Local providers

## The Environment Variable provider

The simplest provider is the `env://` provider, which simply extracts environment variables and replaces those. There is no configuration needed for this provider, and the syntax for adding it is simply:

```
--secret-provider=env://
```

## The File Secret provider

The `file-secret://` provider supports reading secrets from a file containing a JSON encoded dictionary of key/value pairs. As an example, a file could look like:

```json
{
  "key1": "value1",
  "passphrase": "my password"
}
```

The file provider also supports files encrypted with [AESCrypt](../../../using-tools/encrypting-and-decrypting-files.md) and you supply the decryption key with the option `passphrase`.  Suppose the file is encrypted with the key `mypassword` you can then configure the provider:

```
--secret-provider=file-secret:///home/user/secrets.json.aes?passphrase=my-password
```

To avoid passing the encryption key via a commandline, see [the section on how to inject the secret provider configuration via an environment variable](advanced-configurations.md#how-to-avoid-passing-credentials-on-the-commandline).&#x20;

To encrypt the file, you can use the [AESCrypt tool](../../../duplicati-programs/command-line-interface-cli-1/sharpaescrypt.md) provided with Duplicati:

```
Duplicati.CommandLine.SharpAESCrypt.exe e my-password source.json destination.json.aes
```

## Credential Manager (Windows)

On Windows XP and later, the [Credential Manager](https://support.microsoft.com/en-us/windows/accessing-credential-manager-1b5c916a-6a16-889f-8581-fc16e8165ac0) can be used to securely store secrets. As the credentials are protected by the account login, there is no configuration needed, so the setup is simply:

```
--secret-provider=wincred://
```

## Using `libsecret` (Linux)

The[ `libsecret` implementation](https://gnome.pages.gitlab.gnome.org/libsecret/) stores various credentials on Linux and integrates with various UI applications to let the user approve or reject attempts to read secrets.  The `libsecret` provider supports a single optional setting, `collection`, which indicates what collection to read from. If not supplied the `default` collection is used. To use the `libsecret` provider, use this argument:

```
--secret-provider=libsecret://
```

If you are using a system with a Gnome-based desktop, such as Ubuntu, you can use the [Seahorse](https://wiki.gnome.org/Apps/Seahorse) application to manage your passwords.

## Using the `pass` secret provider (Linux)

The [`pass` command](https://www.passwordstore.org) is a project that implements a secure password storage solution on Linux system, backed by GPG. Duplicati can use `pass` as the secret provider:

```
--secret-provider=pass://
```

If you want to use `pass`, make sure it is installed on the system. You also need a GPG key, and you can create one with:

```
gpg --full-generate-key
```

As part of the key generation process, you are asked to enter an email address that will later be used to identify the key. Once you have the GPG key you can initialize pass with:

```
pass init <your-email-address>
```

## Using the KeyChain (MacOS)

For MacOS users the standard password storage is the [KeyChain Access](https://support.apple.com/lt-lt/guide/keychain-access/kyca1083/mac) program. The secrets stored here as application passwords can be used by Duplicati. The KeyChain can be enabled as a secret provider with:

```
--secret-provider=keychain://
```

For more advanced uses the options `account` and `service` can be used to narrow down what secrets can be extracted.
