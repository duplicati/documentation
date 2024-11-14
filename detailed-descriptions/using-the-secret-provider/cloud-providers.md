---
description: This page lists the cloud providers supported as secret providers
---

# Cloud providers

For cloud-based providers there is generally a need to pass some kind of credentials to access the storage as well as the possibility of a provider being unavailable for a shorter period. To address these two issues, see [the section on how to avoid passing credentials on the commandline](advanced-configurations.md#how-to-avoid-passing-credentials-on-the-commandline) and [how to protect against outages](advanced-configurations.md#how-to-protect-against-secret-provider-outages).

Setting up and using either of the vaults described here is outside the scope of this document.

## HashiCorp Vault

The implementation for [HashiCorp Vault](https://www.vaultproject.io) supports both the cloud-based offering as well as the self-hosted version as sources.

To connect to the vault, provide the url as part of the configuration:

```
--secret-provider=
  hcv://localhost:8200?token=<token>&secrets=app1,app2
```

The url is converted to the url used to connect to the vault (e.g., `https://localhost:8200` in this example). The token is used to authenticate, and the secrets are the vaults that secrets are read from.

In the cloud-based offering the "secrets" values shown here are referred to as Apps and in the CLI as "mount points". When more than one value is supplied, the vaults are tried in order and stops once all secrets are resolved. This means that if the same secret key is found in two vaults, the value will be used from the first vault examined.

### Other options for `hcv://`

For development purposes, the url can use a `http` connection by setting `&connection-type=http` , but this should not be used in production.

To connect using a credential pair instead of the token, the credentials can be provided with the values `client-id` and `client-secret` , but should be passed via the environment variables:

```
export HCP_CLIENT_ID=<client-id>
export HCP_CLIENT_SECRET=<secret>

--secret-provider=hcv://localhost:8200?secrets=app1
```

By default, the key lookup is done case-insensitive but can be toggled case-sensitive with the option `&case-sensitive=true`.

## Amazon Secret Manager

The provider for [AWS Secret Manager](https://aws.amazon.com/secrets-manager/) supports the AWS hosted vault. The credentials for the vault are the regular Access Key Id and Access Key Secret. While these can be provided via the secret provider url as `access-id` and `access-key`, they should be passed via the environment variables:

```
export AWS_ACCESS_KEY_ID=<id>
export AWS_SECRET_ACCESS_KEY=<key>

--secret-provider=awssm://?region=us-east-1&secrets=vault1,vault2
```

The secrets values name the vaults to use (called "Secret Name" in the AWS Console). When more than one value is supplied, the vaults are tried in order and stops once all secrets are resolved. This means that if the same secret key is found in two vaults, the value will be used from the first vault examined.

Instead of suppling the region the entire service point url can also be provided via `&service-url=`.

By default, the key lookup is done case-insensitive but can be toggled case-sensitive with the option `&case-sensitive=true`.

## Google Cloud Secret Manager

The secret provider for [Google Cloud Secret Manager](https://cloud.google.com/security/products/secret-manager?hl=en) relies on the [Google Cloud SDK](https://cloud.google.com/sdk?hl=en) to handle the authentication. Follow [the steps to get the environment authenticated](https://cloud.google.com/sdk/gcloud/reference/auth/login) with Google. After the athentication is complete, the configuration is:

```
--secret-provider=gcsm://?project-id=<projectid>
```

If you need to integrate with a different flow you can also [supply an access token](https://cloud.google.com/sdk/gcloud/reference/auth/print-access-token), but notice that the token may be short-lived and you cannot change the token after configuring the secret provider:

```
--secret-provider=gcsm://?project-id=<projectid>&token=<token>
```

### Additional options for `gcsm://`

By default, the screts are accessed with the version set to `latest` but this can be changed with `&version=`. Finally, the communication protocol can be changed from gRPC to https with by adding `&api-type=Rest`.

## Azure Key Vault

With [Azure Key Vault](https://azure.microsoft.com/en-us/products/key-vault) as the provider there are several options for authenticating, where the most secure method is to use the [Azure CLI login](https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli) that handles all the details. Since this method is the default, the secret provider can be configured as:

```
--secret-provider=azkv://?keyvault-name=keyvault
```

Instead of supplying the name of the keyvault, the full vault url can be supplied with `&vault-uri=`.

### Manually authenticating

Instead of relying on the autmated login handling, it is possible to authenticate with either a client credential, or a username/password pair.

For authenticating with client credentials, use:

```
--secret-provider=azkv://?keyvault-name=keyvault
  &auth-type=ClientSecret
  &tenant-id=<tenantid>
  &client-id=<clientid>
  &client-secret=<secret>
```

And for username/password, use:

```
--secret-provider=azkv://?keyvault-name=keyvault
  &auth-type=UsernamePassword
  &tenant-id=<tenantid>
  &client-id=<clientid>
  &username=<username>
  &password=<password>
```
