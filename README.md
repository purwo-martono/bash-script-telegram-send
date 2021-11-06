# telegram-send
Taken from Konstantin Bogomolov site (https://bogomolov.tech/Telegram-notification-on-SSH-login/) 

Create telegram bot

To send a message to Telegram group or channel, you should first create your own bot. Just open Telegram, find @BotFather and type /start. Then follow instructions to create bot and get token to access the HTTP API.
Create Channel

Create a new Channel in Telegram and add your bot as a member. So your bot could send messages to the Channel.

In order to get Channel Id, first, post any message to the Channel. Then use this link template to get Channel Id:
https://api.telegram.org/bot<YourBOTToken>/getUpdates

Here is a response example:
```
{
  "ok":true,
  "result": [
    {
      "update_id":123,
      "channel_post": {
        "message_id":48,
        "chat": {
          "id":-123123123, // this is your channel id
          "title":"Notifications",
          "type":"channel"
        },
        "date":1574485277,
        "text":"test"
      }
    }
  ]
}
```

Script to send message

In order to send a message we could use simple command:
```
curl 'https://api.telegram.org/bot<YourBOTToken>/sendMessage?chat_id=<channel_id>&text=<text>'
```

But in programming, it is good practice to hide the low-level implementation. So we will create a linux terminal command telegram-send and could send messages with this simple command.

Lets create file telegram-send.sh
```
touch telegram-send.sh
```

Then add script to this file. Set your group id and token in script.
```
#!/bin/bash
    
GROUP_ID=<group_id>
BOT_TOKEN=<bot_token>

# this 3 checks (if) are not necessary but should be convenient
if [ "$1" == "-h" ]; then
  echo "Usage: `basename $0` \"text message\""
  exit 0
fi

if [ -z "$1" ]
  then
    echo "Add message text as second arguments"
    exit 0
fi

if [ "$#" -ne 1 ]; then
    echo "You can pass only one argument. For string with spaces put it on quotes"
    exit 0
fi

curl -s --data "text=$1" --data "chat_id=$GROUP_ID" 'https://api.telegram.org/bot'$BOT_TOKEN'/sendMessage' > /dev/null
```

It is not a good practice to store your token in that place, but for now, it is ok. Also, you could limit actions your bot could do in the Channel only to send messages.

To run this script we should add permission
```
chmod +x telegram-send.sh
```

Now you can test it
```
./telegram-send.sh "Test message"
```

In order to use this script from everywhere and type telegram-send instead ./telegram-send.sh add it to /usr/bin/ folder
```
sudo mv telegram-send.sh /usr/bin/telegram-send
```

Owner of all files in /usr/bin is root user. So let’s do the same with our script:
```
sudo chown root:root /usr/bin/telegram-send
```

Now you can test it
```
telegram-send "Test message"
```














