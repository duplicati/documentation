---
description: This page describes the template system used to format text messages sent
---

# Custom message content

The template system used in Duplicati is quite simple, as it will essentially expand Windows-style environment placeholders, `%EXAMPLE%`, into values.  The same replace logic works for both the subject line (if applicable) and the message body.

**Note**: The description here only covers the text-based output (such as emails, etc). The t[emplate system for JSON](custom-message-content.md#json-output) is a bit different.

Duplicati has defaults for the body and subject line, but you can specify a custom string here. For convenience, the string can also be a path to a file on the machine, which contains the template.

An example custom template could look like:

```
Duplicati %OPERATIONNAME% for %backup-name% on %machine-name%

The %OPERATIONNAME% operation has completed with the result: %PARSEDRESULT%

Source folders: 
%LOCALPATH%

Encryption module: %ENCRYPTION-MODULE%
Compression module: %COMPRESSION-MODULE%

%RESULT%
```

The template engine supports reporting any setting by using the setting name as the template value. Besides the options, there are also a few variables that can be used to extract information more easily:&#x20;

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
  The assigned id for the backup. Can be overridden with --backup-id
%backup-name%
  The name of the backup. Can be overridden with --backup-name
%machine-name%
  The name of the machine. Can be overridden with --machine-name
```

## JSON output

If the output is JSON it needs to be handled different than regular text, to ensure the result is valid. The logic for this is to re-use the templating concept, but only as a lookup, to figure out what keys to include in the results.

An example template could be:

```
%OPERATIONNAME% 
%backup-name% 
%machine-name% 
%PARSEDRESULT% 
%LOCALPATH%
%ENCRYPTION-MODULE%
%COMPRESSION-MODULE%
```

This will ensure that each of those values will be included in the `extra` element in the JSON output. The default template for JSON output includes all fields listed above, but no options are included by default.
