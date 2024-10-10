# Smart Breeze

## Introduction
The goal is to simulate a functional smart ventilator using a combination of a weather API, a Telegram bot and and a control button to turn the ventilator on and off. The system will allow users to manually adjust the ventilator settings through the bot's interface, while also offering an automated mode that adjusts the ventilator based on the weather API. 

### What do you need?
* NodeMCU Arduino Board, ESP8266
* Arduino IDE
* LED strip
* Micro USB cable
* WIFI connection
* Key Switch Sensor
* Telegram

## Step 1: Making a bot
Go to Google Play or App Store, download and install Telegram. 

![Telegram](image/telegram.jpg)

Open Telegram and follow the next steps to create a Telegram Bot. First, search for “botfather” and click the BotFather as shown below. Or open this link t.me/botfather in your smartphone. 

![Botfather](image/botfather.jpg)

The following window should open and you’ll be prompted to click the start button.

![Botfather start](image/bot.jpg)

Type /newbot and follow the instructions to create your bot. Give it a name and username. 

If your bot is successfully created, you’ll receive a message with a link to access the bot and the bot token. Save the bot token because you’ll need it so that the ESP32/ESP8266 can interact with the bot.

![Botfather bot token](image/bot2.jpg)

### Get your Telegram User ID
Anyone that knows your bot username can interact with it. To make sure that we ignore messages that are not from our Telegram account (or any authorized users), you can get your Telegram User ID. Then, when your telegram bot receives a message, the ESP can check whether the sender ID corresponds to your User ID and handle the message or ignore it.

In your Telegram account, search for “IDBot” or open this link  [MyIDBot](t.me/myidbot) in your smartphone.

![IDBot](image/idbot.jpg)

Start a conversation with that bot and type /getid. You will get a reply back with your user ID. Save that user ID, because you’ll need it later in this tutorial.

![IDBot ID](image/idbot2.jpg)

## Step 2: Add Libraries 
To interact with the Telegram bot, we’ll use the Universal Telegram Bot Library created by Brian Lough that provides an easy interface for the Telegram Bot API. And we also need AruinoJson.

### ArduinoJson
Go to Sketch > Include Library > Manage Libraries and add “ArduinoJson” Benoit Blanchon. We are using version 5.13.5.

![Library](image/library.jpg)
![ArduinoJson](image/ArduinoJson.jpg)

### Universal Telegram Bot
And add the 'universal telegram bot' via the 'Library Manager' of Arduino.
Choose for the 1.1.0 version

![Library](image/library-universal2.jpg)