---
description: This page describes the OAuth login used by some providers
---

# OAuth Server

[OAuth is an industry standard](https://oauth.net/2/) that allows applications to securely admit third-party access from legitimate users without exposing details, such as real name, passwords, etc.

Many large providers only allow access with OAuth, requiring the user to authenticate that Duplicati may access their resources on their behalf. This generally works by initiating a login request, then redirecting the web browser to the login page, and then delivering a secure access token to Duplicati.

For web-based applications, this is a very smooth process, but for a tool such as Duplicati that needs to run, even when there is no browser or UI available, it is not an ideal solution. The workaround developed for Duplicati is to pre-authenticate with a long-lived token from a place where there is a browser available. Once the token is created, it is returned to the user in the form of an `AuthID` string.

This AuthID can then be used by Duplicati to access resources on the users behalf, acting as a kind of API key. Further details on how the OAuth server works is described in the [forum post on OAuth](https://forum.duplicati.com/t/how-we-get-along-with-oauth/17680).

## Cloud service

Duplicati has a hosted service that can be used to get access to a variety of different storage providers. It is hosted on [Google App Engine](https://cloud.google.com/appengine?hl=en) and the [source code is available](https://github.com/duplicati/oauth-handler).

This service is the default as it is the most convenient for most users. To generate a token, simply visit:

[https://duplicati-oauth-handler.appspot.com](https://duplicati-oauth-handler.appspot.com)

Click the button for your prefered provider, complete the login, and obtain the AuthID, that you can then use on another machine as needed.

If you are using the UI, you can click the `AuthID` label/link to start the process. Once you complete it, the UI will automatically fill in the ID, no interaction required.

If you want to remove access, you can either revoke a specific AuthID at the same place where you created it, using the [revoke AuthID feature](https://duplicati-oauth-handler.appspot.com/revoke). You can also go to the provider, say Dropbox or OneDrive, and remove the authorization for Duplicati, which will immediately revoke all tokens issued for your account.

## Self-hosted server

If you prefer to manage the full cycle and not send tokens into a provider not under your control, you can use the [self-hosted open-source OAuth Server](https://github.com/duplicati/oauth-server). The server is Docker enabled and also available as a [pre-built Docker image](https://hub.docker.com/r/duplicati/oauth-server).

Refer to the [Github documentation](https://github.com/duplicati/oauth-server) for how to configure it. Before you can use the server, you need to obtain a Client ID and Client Secret for the provider you want to support. Refer to the default providers file to see the links to each service, or consult your service provider for details on how to obtain these values.

After you have set up the server, use the option `--oauth-url=<local server url>` to configure Duplicati to use another server to authenticate with.
