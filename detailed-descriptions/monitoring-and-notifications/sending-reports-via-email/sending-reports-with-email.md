---
description: Describes the how to configure sending emails with backup details
---

# Sending reports with email

Sending emails is supported by having access to an SMTP server that will accept the inbound emails. From on your SMTP/email server provider you need to get a url, a username, and a password. If you are a [GMail or Google Workspace user, use the Google SMTP guide](https://support.google.com/a/answer/176600), otherwise consult your provider for these details.

Besides the connection details, you also need to provide the recipient email address. Note that SMTP servers may sometimes restrict what recipients they allow, but generally using the provider SMTP server will allow sending to your own account.

In the UI you can configure these mandatory values as well as the optional values.

The basic options for sending email can be entered into the general settings, which will then apply to each backup. It is also possible to apply or change the settings for the individual backups by editing the advanced options. Here is how it looks when editing it in the user interface:

<div>

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-20 at 08.54.04.png" alt=""><figcaption><p>Set up email with the default options editor</p></figcaption></figure>

  

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-20 at 08.54.14.png" alt=""><figcaption><p>Set up email option with a text field</p></figcaption></figure>

</div>

You can toggle between the two views using the "Edit as list" and "Edit as text" links.

Besides the mandatory options, it is also possible to configure:

* Email sender address
* The subject line
* The email body
* Conditions on when to send emails

For details on how to customize the subject line and message body, see the [section on customizing message content](custom-message-content.md).

If you prefer email logs, but would also like to get reports, check out the community provided [dupReport](https://forum.duplicati.com/t/announcing-dupreport-a-duplicati-email-report-summary-generator/1116) tool that can summarize the emails into overviews.
