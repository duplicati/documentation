---
description: Describes the how to configure sending notifications via Telegram
---

# Sending Telegram notifications

To send a notification via Telegram you need to supply a channel id, a bot token and a an api key.

To obtain the bot token (aka bot id), message the `@BotFather` bot. After creating the bot, send a message to the bot, so it can reply. For more details on Telegram bots, see the [Telegram bot documentation](https://core.telegram.org/bots).

After obtaining the bot token you can obtain the channel id with a cURL script:

```
BOT_TOKEN="YOURBOTTOKEN" curl -s "https://api.telegram.org/bot$BOT_TOKEN/getUpdates" \
  | grep -o '"id":[0-9]*' | head -1 | cut -d':' -f2
```

To obtain the API key, follow the [Telegram guide to creating an application](https://core.telegram.org/api/obtaining_api_id).

With all required values obtained, you can set up the Telegram notifications in the general settings:

<div><figure><img src="../../../.gitbook/assets/Screenshot 2024-11-20 at 15.21.57.png" alt=""><figcaption><p>Set up Telegram notifications with the default options editor</p></figcaption></figure> <figure><img src="../../../.gitbook/assets/Screenshot 2024-11-20 at 15.22.08.png" alt=""><figcaption><p>Set up Telegram option with a text field</p></figcaption></figure></div>

You can toggle between the two views using the "Edit as list" and "Edit as text" links.

Besides the mandatory options, it is also possible to configure:

* The notification message and format
* Conditions on when to send emails
* Conditions on what log elements to include

### Telegram Notification Options



**Bot Configuration**

**`--send-telegram-bot-id`**` ``(String)` - The Telegram bot ID that will send messages

**`--send-telegram-api-key`**` ``(String)` - The API key for authenticating your Telegram bot

**Message Destination**

**`--send-telegram-channel-id`**` ``(String)` - The channel ID where messages will be sent

**`--send-telegram-topid-id`**` ``(String)` - Topic ID for posting in specific topics within Telegram groups

**Notification Content**

**`--send-telegram-message`**` ``(String)` - Template for message content with support for variables like %OPERATIONNAME%, %REMOTEURL%, %LOCALPATH%, and %PARSEDRESULT%

**`--send-telegram-result-output-format`**` ``(format)` - Format for presenting operation results&#x20;

* Duplicati
* Json

**Notification Filtering**

**`--send-telegram-level`**` ``(level)` - Controls which result types trigger notifications:

* Success - Only successful operations
* Warning - Operations that completed with warnings
* Error - Operations that failed with recoverable errors
* Fatal - Operations that failed with critical errors
* All - All operation results regardless of status

**`--send-telegram-any-operation`**` ``(Boolean)` - When enabled, sends notifications for all operations, not just backups

**`--send-telegram-log-level`**` ``(Enumeration)` - Sets minimum severity level for included log entries:

* ExplicitOnly - Show only explicitly requested messages
* Profiling - Include performance measurement data
* Verbose - Include detailed diagnostic information
* Retry - Include information about retry attempts
* Information - Include general status messages
* DryRun - Include simulation mode outputs
* Warning - Include potential issues that didn't prevent completion
* Error - Include critical failures that require attention

**`--send-telegram-log-filter`**` ``(String)` - Filters log entries based on specified patterns

**`--send-telegram-max-log-lines`**` ``(Integer)` - Limits the number of log lines included in notifications



For details on how to customize the notification message, see the [section on customizing message content](custom-message-content.md).
