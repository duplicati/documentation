---
description: Describes the features and how to configure sending emails with backup details
---

# Sending reports via Email

The basic options for sending email are documented via the CLI:

```
--send-mail-to=<email-address>
  Send an email to <email-address> after a backup. 
  Valid formats are "Name <test@example.com>, Other <test2@example.com>, test3@example.com". 
  Multiple addresses must be separated with a comma. 
--send-mail-from=<email-address>
  This is the sender address of the email that is sent.
--send-mail-subject=<subject>
  This is the subject line of the email that is sent. 
  E.g. this can be "Duplicati %OPERATIONNAME% Report"
--send-mail-body=<body>
  The content of the email message. This should contain "%RESULT%".
--send-mail-url=<smtp-url>
  A URL to connect to an SMTP server to send out an email. 
  Example: "tls://smtp.example.com:587", "smtps://smtp.example.com:465" or "smtp://smtp.example.com:25"
--send-mail-username=<username>
  Required username to authenticate with SMTP server.
--send-mail-password=<password>
  Required password to authenticate with SMTP server.
--send-mail-level=<level>
  When email messages are sent: "Success", "Warning", "Error", "Fatal", "All" are possible.
--send-mail-any-operation=true
  Also send emails after other operations like restore etc.
```

To configure the contents of the email, you can use these placeholder values in the subject and body options:

```
 %PARSEDRESULT%
      The parsed result op the operation: Success, Warning, Error
    %RESULT%
      When used in the body, this is the result/log of the backup, 
      When used in the subject line, this is the same as %PARSEDRESULT%
    %OPERATIONNAME%
      The name of the operation, usually "backup", but could also be "restore" etc.
    %REMOTEURL%
      The backend url
    %LOCALPATH%
      The path to the local folders involved (i.e. the folders being backed up)
    %machine-id%
      The assigned unique random identifier for the current machine.
      Can be overridden with --machine-id
    %backup-id%
      The assigned id for the backup.
      Can be overridden with --backup-id
    %backup-name%
      The name of the backup.
      Can be overridden with --backup-name
    %machine-name%
      The name of the machine.
      Can be overridden with --machine-name
```
