# Advanced configurations

## Sharing the secret provider

If the secret provider is configured for the entry application (e.g., the [TrayIcon](../../duplicati-programs/trayicon.md), [Server](../../duplicati-programs/server.md) or [Agent](../../duplicati-programs/agent.md)) it will naturally work for that application, but will also be shared within that process.

For the [Agent](../../duplicati-programs/agent.md), this means that setting the secret provider for the agent, will also let the server that it hosts use the same secret provider. When a backup or other operation is then executed by the server it will also have access to the same secret provider.

This sharing simplifies the setup by only having a single secret provider configuration and then letting each of the other parts access secrets without further configuration. If needed, the secret providers can be specified for the individual backups, such that it is possible to opt-out of using the shared secret provider.

## How to avoid passing credentials on the commandline

To make passing arguments to the application a bit harder to obtain, the value for `--secret-provider` is treated as an environment variable if:

* It starts with `$` optionally with curly brackets `{}`:&#x20;
  * `$secretprovider`
  * `${secretprovider}`
* If it starts and ends with `%` :
  * `%secretprovider%`

No expansion is done on environment variables, so the entire provider string is required to be set as an environment variable.

## How to protect against secret provider outages

If you run an operation and the secret provider is unavailable when the secrets are requested, the operation will fail. For most uses the occurence of an outage is so rare that this situation is acceptable.

However, for some uses it is important that the backups keep running, even in the face out outages. To handle this need, Duplicati supports an optional cache strategy:

```
--secret-provider-cache=None
--secret-provider-cache=InMemory
--secret-provider-cache=Persistent
```

Storing the secrets somewhere makes it more likely that it is eventually leaked. For that reason, the default is to use the cache setting `None` which turns off the caching fully and only relies on the provider.

The `InMemory` setting is the least intrusive version as it only stores the secrets in the process memory. This option is most useful when using a [shared provider](advanced-configurations.md#sharing-the-secret-provider) such that it stays in memory between runs.

Finally, the `Persistent` option will write secrets to disk, so it can handle situations where the provider is unavailable during startup, or where a shared provider does not work.&#x20;

As the purpose of the secret provider is to prevent the secrets from being written to disk, the secrets are written to disk using a passhprase derived from the secret provider url. If the secret provider url does not contain a strong secret already, it is possible to add any parameter to the url to increase the strength of the key.

If the secret provider url changes, it is no longer possible to retrieve the cached values, and the next run will fail if the provider is unavailable, but will otherwise write a new encrypted cache file to disk.
