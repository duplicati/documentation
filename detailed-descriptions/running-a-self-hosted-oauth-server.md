---
description: This page describes how to set up and run a self-hosted OAuth Server
---

# Running a self-hosted OAuth Server

If you are using one of the backends that requires login via OAuth (Google, Dropbox, OneDrive, etc) you will need to obtain a "clientId" and a "clientSecret". These are given by the service providers when you are logged in, and are usually free.

If you prefer to avoid the hassle of setting this up, you can opt to use the Duplicati provided OAuth server, where Duplicati's team will handle the configuration. This OAuth server is the default way to authenticate. If you prefer to be more in control of the full infrastructure, you can use this guide to set up and use your own self-hosted OAuth Server.

For example, this guide will show how to set up an OAuth server for internal use in an organization, granting Duplicati instances full access to the Google Drive files.

If you need to set up another provider than Google, see [the configuration defaults that has links to the pages where the Client ID and Client secret can be found for other services](https://github.com/duplicati/oauth-server/blob/main/ConfigurationLoader.Defaults.cs).

## Getting access to Google Cloud Services

The first step is to [sign up for Google Cloud Services](https://console.cloud.google.com/) if you are not already a customer. Once you are signed up, you can create a new project as shown here:

<figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.10.52.png" alt="" width="375"><figcaption><p>Creating a new project</p></figcaption></figure>

Once you have create a project where the OAuth settings can live in, you need to enable the "**Google Drive API**". Go to the top-left menu, choose "**API & Services**" and then "**Enabled APIs & Services**". From here search for "**Google Drive API**", click it and enable:

<div><figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.11.21.png" alt=""><figcaption><p>Choosing the menu "Enabled APIs &#x26; Services"</p></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.11.44.png" alt=""><figcaption><p>Enabling API and Services</p></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.12.11.png" alt=""><figcaption></figcaption></figure></div>

Before you can get the values you need to configure the consent screen that is shown when users log in with your OAuth Service. You can choose "**Internal**" here, unless you need to provide access to people outside your organization. Choosing "External" also requires a Google review. On the consent screen, you only need to fill in the required fields, the app name and some contact information:

<div><figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.13.54.png" alt=""><figcaption><p>Choosing Audience</p></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.15.29.png" alt=""><figcaption><p>Setting up the consent screen</p></figcaption></figure></div>

The last step in the consent is choosing the scopes (meaning the permissions) it is possible to grant with this setup. In this example we choose the `auth/drive` scope, granting full access to all files in the users Drive. For regular uses, it is safest to use `auth/drive.file` which will only grant Duplicati access to files created by Duplicati. However, in some cases Google Drive will drop your permissions and refuse to let Duplicati access the files. There is no way to change the permissions on the files, so if this happens, your only choice is to use `auth/drive` and obtain full access:

<figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.16.13.png" alt="" width="375"><figcaption><p>Choosing the scopes</p></figcaption></figure>

You can now click update and save the consent screen and proceed to setting up the credentials needed. Click "**Create Credentials**" and choose "**OAuth client ID**". On the next page, choose the type "**Web application**". In the "**Authorized redirect URIs**" field you need to enter the url for the server that is being called after login. The Duplicati OAuth server uses a path of `/logged-in` so make sure it ends with that. In the screenshot, the server is hosted on a single machine, so the setup is for `https://localhost:8080/logged-in`:

<div><figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.54.13.png" alt=""><figcaption><p>Choose OAuth client ID</p></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.17.28.png" alt=""><figcaption><p>Configure the OAuth client ID</p></figcaption></figure></div>

When you are done, click "Save" and a popup will show the credentials that are generated. Use the convenient copy buttons to get "**Client ID**" and "**Client secret**" or download the JSON file containing them. If you loose them, you can get then again via the "Credentials" page. The credentials shown here are redacted:

<figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 22.17.41.png" alt="" width="375"><figcaption><p>Redacted view of the generated credentials</p></figcaption></figure>

## Setting up the configuration

With the credentials available, create a JSON text file similar to this:

```json
{
  "GD_CLIENT_ID": "<Put Client ID here>",
  "GD_CLIENT_SECRET": "<Put Client secret here>"
}
```

If you are setting up a secure server, you should use [SharpAESCrypt](../duplicati-programs/command-line-interface-cli-1/sharpaescrypt.md) to encrypt the file after you have created it. If you do, make a note of the passphrase used. Save the file either as `secrets.json` or `secrets.json.aes` if you have encrypted it.

In the following, we will only set up Full Access Google Drive, which for legacy reasons is called "`googledocs`" in the OAuth server. If you are looking to set up one of the other services, see [the configuration document](https://github.com/duplicati/oauth-server/blob/main/config.json), and pick the ids you need.

In the following, the `services` are configured to just `googledocs` but it can be a comma separated list of services if you want to enable multiple. The storage is here simply a local folder that stores encrypted tokens, but you can also use an S3 compatible storage if needed. See the [OAuth server readme](https://github.com/duplicati/oauth-server/blob/main/README.md) for more details.

## Docker based setup

If you are using Docker, you can run the [OAuth server image](https://hub.docker.com/r/duplicati/oauth-server) directly and simply add environment variables:

```yaml
- ASPNETCORE_URLS: "http://localhost:8080"
- HOSTNAME: "localhost:8080"
- SECRETS: "/path/to/secrets.json.aes"
- SECRETS_PASSPHRASE: "<encryption passphrase>"
- STORAGE: "file:///path/to/persisted/folder"
- SERVICES: "googledocs"
```

The hostname here **MUST** match the one set as the redirect URI or the authorization will fail. The URLs parameter is what the internal Docker engine thinks it is running. For this setup there is no TLS/SSL certificate, so the URL here is `http` but note that we used `https` in the redirect URI and these two must match in the end. Here I am assuming some other service is providing the SSL layer.

If you need to serve the certificate directly from the Docker container, generate a certificate `.pfx` file and use a configuration such as:

```yaml
- ASPNETCORE_URLS: "https://localhost:8080"
- HOSTNAME: "localhost:8080"
- SECRETS: "/path/to/secrets.json.aes"
- SECRETS_PASSPHRASE: "<encryption passphrase>"
- STORAGE: "file:///path/to/persisted/folder"
- SERVICES: "googledocs"
- ASPNETCORE_Kestrel__Certificates__Default__Path: "/path/to/certificate.pfx"
- ASPNETCORE_Kestrel__Certificates__Default__Password: "<certificate password>"
```

## Local machine setup

To run without Docker, first you need to [download the OAuth Server binaries for your operating system](https://github.com/duplicati/oauth-server/releases) and extract them to a suitable place. The binaries are self-contained so the will run without any additional framework installation.

To run the server, invoke it with a setup like this:

```
OAuthServer run 
  --listen-urls=http://localhost:8080 
  --hostname=localhost:8080
  --storage=file:///path/to/persisted/folder
  --secrets=/path/to/secrets.json.aes
  --secrets-passphrase=<encryption passphrase>
  --services=googledocs
```

The hostname here **MUST** match the one set as the redirect URI or the authorization will fail. The URLs parameter is what the process thinks it is running locally. For this setup there is no TLS/SSL certificate, so the URL here is `http` but note that we used `https` in the redirect URI and these two must match in the end. Here I am assuming some proxy service is providing the SSL certificate.

If you need to serve the certificate directly from the the binary, generate a certificate `.pfx` file and use a configuration such as:

```
OAuthServer run 
  --listen-urls=https://localhost:8080 
  --hostname=localhost:8080
  --storage=file:///path/to/persisted/folder
  --secrets=/path/to/secrets.json.aes
  --secrets-passphrase=<encryption passphrase>
  --services=googledocs
  --certificate-path=/path/to/certificate.pfx
  --certificate-password=<certificate password>
```

## Issuing an AuthID

Once the service is running, you can navigate to the page and generate an AuthID:

<figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 23.28.59.png" alt="" width="375"><figcaption><p>Ready to generate an AuthID</p></figcaption></figure>

## Using the self-hosted OAuth server in Duplicati

The final step is to instruct Duplicati to use the self-hosted OAuth server instead of the regular instance. This is done by visiting the "Settings" page in the Duplicati UI and adding the advanced option `--oauth-url=https://localhost:8080/refresh`:

<figure><img src="../.gitbook/assets/Screenshot 2025-02-04 at 23.32.32.png" alt=""><figcaption><p>Adding the OAuth URL to Duplicati</p></figcaption></figure>

Don't forget to click "OK" to save the settings. Once configured, the "AuthID" links in the UI will point to your self-hosted OAuth server, and all authorization is done purely through the self-hosted OAuth server.
