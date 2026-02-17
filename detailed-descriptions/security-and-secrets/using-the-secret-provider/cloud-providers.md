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

The provider for [AWS Secret Manager](https://aws.amazon.com/secrets-manager/) supports the AWS hosted vault. The credentials for the vault are the regular Access Key Id and Access Key Secret. While these can be provided via the secret provider url as `access-id` and `secret-key`, they should be passed via the environment variables:

```
export AWS_ACCESS_KEY_ID=<id>
export AWS_SECRET_ACCESS_KEY=<key>

--secret-provider=awssm://?region=us-east-1&secrets=vault1,vault2
```

The `secrets` values name the vaults to use (called "Secret Name" in the AWS Console). When more than one value is supplied, the vaults are tried in order and stops once all secrets are resolved. This means that if the same secret key is found in two vaults, the value will be used from the first vault examined.

Instead of suppling the region the entire service point url can also be provided via `&service-url=`.

By default, the key lookup is done case-insensitive but can be toggled case-sensitive with the option `&case-sensitive=true`.

If you use IAM to create an account for this, you can use the policy `SecretsManagerReadWrite`.

{% hint style="warning" %}
You need to create the "Secret Name" in AWS console before you can use it.

The secrets in AWS should be key/value and can contain multiple values. Setting a value will always append it to the first secret provided.
{% endhint %}

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

By default, the secrets are accessed with the version set to `latest` but this can be changed with `&version=`. The communication protocol can be changed from gRPC to https with by adding `&api-type=Rest`.

It is also possible to use either `service-account-file` or `service-account-json` to authenticate with a service account.

### Configure a service account for Google Cloud Secret Manager

To configure a service account for Google Cloud Secret Manager, visit  [IAM ](https://console.cloud.google.com/iam-admin/serviceaccounts)→[ Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts) → Create.

Use the role `Secret Accessor` for read-only access, and add `Secret Version Adder` if the account should be able to update the secrets.&#x20;

Then create the key with: Service account → Keys → Add key → Create new key → JSON.

This will generate a key that will be downloaded. You can either use the key-file with `service-account-file=<path-to-file>`, or you can url-encode the contents and supply it to `service-account-json=<url-encoded-json>`.

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

### How to configure manual access to an Azure Key Vault

There are several ways as shown above, and the recommended is to use the CI method, but here is a description of a manual way: creating a client secret.

First, visit Portal → Microsoft Entra ID → App registrations → New registration.

Enter a name for the application and create it. On the application screen, record the Application Id and Directory Id:

<figure><img src="../../../.gitbook/assets/Screenshot 2025-12-01 at 21.04.58.png" alt=""><figcaption></figcaption></figure>

Then visit: App → Certificates & secrets → Client secrets → New client secret.

Create a new client secret, and make sure you copy the secret as it will not be shown again:

<figure><img src="../../../.gitbook/assets/Screenshot 2025-12-01 at 21.09.19.png" alt=""><figcaption></figcaption></figure>

Event with all the information ,you still need to grant permissions for the app/client to access the key vault: Key Vault → Access control (IAM) → Add role assignment.

If you need read-only access, use the role `Key Vault Secrets User` and if you need read/write acces, use the role `Key Vault Secrets Officer`. On the member page, add the app/client.

Now you have all the details to connect to the vault, add them as shown above, and make sure to url-encode the values, particularly the secret as it may contain characters that are not url-safe. You need to encode: `keyvault-name`, `tenant-id`, `client-id`, and `client-secret`.

