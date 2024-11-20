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

To obtain the API key, follow the [Telegram guide to creating an application](https://core.telegram.org/api/obtaining\_api\_id).

With all required values obtained, you can set up the Telegram notifications in the general settings:

<div>

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-20 at 15.21.57.png" alt=""><figcaption><p>Set up Telegram notifications with the default options editor</p></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-20 at 15.22.08.png" alt=""><figcaption><p>Set up Telegram option with a text field</p></figcaption></figure>

</div>

You can toggle between the two views using the "Edit as list" and "Edit as text" links.

Besides the mandatory options, it is also possible to configure:

* The notification message and format
* Conditions on when to send emails
* Conditions on what log elements to include

For details on how to customize the notification message, see the [section on customizing message content](custom-message-content.md).
