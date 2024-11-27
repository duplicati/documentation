---
description: This page provides an overview of the authentication in Duplicati
---

# Server authentication model

To authenticate with Duplicati, the authenticating system needs a password. There are several ways to define that password as described in the section on [Duplicati Access Password](../detailed-descriptions/duplicati-access-password.md). There is no username, as Duplicati does not support the concept of different users, so all access is the same.

The password is not stored in plain text, it is repeatedly hashes with a [PBKDF](https://en.wikipedia.org/wiki/PBKDF2) method to ensure it is not readable from the database. This ensures that even an attacker with access to the database will not be able to obtain a password that can be used to log in.

When the password is provided, it is compared to the hashed value that is stored, and only if the password was the same, is the access granted. The access in Duplicati is granted in the form of a long-lived Refresh Token, usually stored as a protected cookie. Since cookie based authentication has a number of weaknesses, the cookie is only used to obtain a short-lived Access Token.

The Access Token is newer persisted, it only lives in the browsers memory to protect it as much as possible from various attacks. If any tool needs access to call Duplicati, it can also request an access token directly with the password, and then call the API methods with the Access Token.

Once the Access Token expires, a new token must be issued. In the browser, this happens transparently when the expiration is detected. For external tools, this re-issue handling must be implemented.

For some external access, such as the one used by the [TrayIcon](../duplicati-programs/trayicon.md) and [ServerUtil](../duplicati-programs/command-line-interface-cli-1/serverutil.md), a third token type is issued: Signin Token. The Signin Token is a placeholder for the password and can be used to obtain either a Refresh Token or an Access Token, but it is very short-lived.

As noted in the section on [Duplicati Access Password](../detailed-descriptions/duplicati-access-password.md), the issuing of tokens is done with keys stored in the local database. As a precaution, Duplicati keeps track of all refresh tokens it has created, meaning that even with the issuing keys, it is not possible to create a new Refresh Token that will be accepted by the server.

However, it is possible to issue Signin Tokens and Access Tokens, for anyone with access to the signing keys stored in the database. To limit this exposure, it is possible to [disable the issuance of Signin Tokens](../detailed-descriptions/duplicati-access-password.md), but the Access Tokens cannot currently be secured in case the database is compromised.

To avoid leaking the signing keys, it is recommended that the local database is protected by [setting the field encryption password](../detailed-descriptions/protecting-the-server-database.md).
