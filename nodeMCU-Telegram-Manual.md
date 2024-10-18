# Telegram Adafruit ESP8266 

## Introduction
This guide shows how to control the ESP32 or ESP8266 NodeMCU GPIOs from anywhere in the world using Telegram. As an example, we’ll control an LED, but you can control any other output. You just need to send a message to your Telegram Bot to set your outputs HIGH or LOW. The ESP boards will be programmed using Arduino IDE.

![Telegram and ESP8266](image/intro.jpg)

### What do you need?
* NodeMCU Arduino Board (ESP8266)
* Arduino IDE
* WIFI connection
* Micro USB cable

## :robot: Step 1: Making a bot
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

## :books: Step 2: Add Libraries 
To interact with the Telegram bot, we’ll use the Universal Telegram Bot Library created by Brian Lough that provides an easy interface for the Telegram Bot API. And we also need AruinoJson.

### ArduinoJson
Go to Sketch > Include Library > Manage Libraries and add “ArduinoJson” Benoit Blanchon. We are using version 5.13.5.

![Library](image/Library.jpg)
![ArduinoJson](image/ArduinoJson.jpg)

### Universal Telegram Bot
And add the 'universal telegram bot' via the 'Library Manager' of Arduino.
Choose for the 1.1.0 version

![Library](image/library-universal2.jpg)

## :speaking_head: Step 3: Echobot
Open 'echobot' via File > Examples > UniversalTelegramBot > ESP8266 > EchoBot

![Library echobot](image/Echo-bot.jpg)

You may have code like below, the code is correct, but some data and the code about messages are not included.

```cpp
/*******************************************************************
*  An example of bot that echos back any messages received         *
*                                                                  *
*  written by Giacarlo Bacchio (Gianbacchio on Github)             *
*  adapted by Brian Lough                                          *
*******************************************************************/
#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>

// Initialize Wifi connection to the router
char ssid[] = "XXXXXXX";     // your network SSID (name)
char password[] = "XXXXXXXXX"; // your network key

// Initialize Telegram BOT
#define BOTtoken "XXXXXXXXXX"  // your Bot Token (Get from Botfather)

WiFiClientSecure client;
UniversalTelegramBot bot(BOTtoken, client);

int Bot_mtbs = 1000; //mean time between scan messages
long Bot_lasttime;   //last time messages' scan has been done

void setup() {
  Serial.begin(115200);

  // Set WiFi to station mode and disconnect from an AP if it was Previously
  // connected
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  delay(100);

  // Attempt to connect to Wifi network:
  Serial.print("Connecting Wifi: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());
}

void loop() {
  if (millis() > Bot_lasttime + Bot_mtbs)  {
    int numNewMessages = bot.getUpdates(bot.last_message_received + 1);

    while(numNewMessages) {
      Serial.println("got response");
      for (int i=0; i<numNewMessages; i++) {
        bot.sendMessage(bot.messages[i].chat_id, bot.messages[i].text, "");
      }
      numNewMessages = bot.getUpdates(bot.last_message_received + 1);
    }

    Bot_lasttime = millis();
  }
}

```

The following code allows you to control your ESP32 or ESP8266 NodeMCU GPIOs by sending messages to a Telegram Bot. To make it work for you, you need to enter your network details (SSID and password), the Telegram Bot Token and your Telegram user ID. Copy this:

```cpp
/*
  Rui Santos
  Complete project details at https://RandomNerdTutorials.com/telegram-control-esp32-esp8266-nodemcu-outputs/
  
  Project created using Brian Lough's Universal Telegram Bot Library: https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot
  Example based on the Universal Arduino Telegram Bot Library: https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot/blob/master/examples/ESP8266/FlashLED/FlashLED.ino
*/

#ifdef ESP32
  #include <WiFi.h>
#else
  #include <ESP8266WiFi.h>
#endif
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>   // Universal Telegram Bot Library written by Brian Lough: https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot
#include <ArduinoJson.h>

// Replace with your network credentials
const char* ssid = "REPLACE_WITH_YOUR_SSID";
const char* password = "REPLACE_WITH_YOUR_PASSWORD";

// Initialize Telegram BOT
#define BOTtoken "XXXXXXXXXX:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"  // your Bot Token (Get from Botfather)

// Use @myidbot to find out the chat ID of an individual or a group
// Also note that you need to click "start" on a bot before it can
// message you
#define CHAT_ID "XXXXXXXXXX"

#ifdef ESP8266
  X509List cert(TELEGRAM_CERTIFICATE_ROOT);
#endif

WiFiClientSecure client;
UniversalTelegramBot bot(BOTtoken, client);

// Checks for new messages every 1 second.
int botRequestDelay = 1000;
unsigned long lastTimeBotRan;

const int ledPin = 2;
bool ledState = LOW;

// Handle what happens when you receive new messages
void handleNewMessages(int numNewMessages) {
  Serial.println("handleNewMessages");
  Serial.println(String(numNewMessages));

  for (int i=0; i<numNewMessages; i++) {
    // Chat id of the requester
    String chat_id = String(bot.messages[i].chat_id);
    if (chat_id != CHAT_ID){
      bot.sendMessage(chat_id, "Unauthorized user", "");
      continue;
    }
    
    // Print the received message
    String text = bot.messages[i].text;
    Serial.println(text);

    String from_name = bot.messages[i].from_name;

    if (text == "/start") {
      String welcome = "Welcome, " + from_name + ".\n";
      welcome += "Use the following commands to control your outputs.\n\n";
      welcome += "/led_on to turn GPIO ON \n";
      welcome += "/led_off to turn GPIO OFF \n";
      welcome += "/state to request current GPIO state \n";
      bot.sendMessage(chat_id, welcome, "");
    }

    if (text == "/led_on") {
      bot.sendMessage(chat_id, "LED state set to ON", "");
      ledState = HIGH;
      digitalWrite(ledPin, ledState);
    }
    
    if (text == "/led_off") {
      bot.sendMessage(chat_id, "LED state set to OFF", "");
      ledState = LOW;
      digitalWrite(ledPin, ledState);
    }
    
    if (text == "/state") {
      if (digitalRead(ledPin)){
        bot.sendMessage(chat_id, "LED is ON", "");
      }
      else{
        bot.sendMessage(chat_id, "LED is OFF", "");
      }
    }
  }
}

void setup() {
  Serial.begin(115200);

  #ifdef ESP8266
    configTime(0, 0, "pool.ntp.org");      // get UTC time via NTP
    client.setTrustAnchors(&cert); // Add root certificate for api.telegram.org
  #endif

  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, ledState);
  
  // Connect to Wi-Fi
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  #ifdef ESP32
    client.setCACert(TELEGRAM_CERTIFICATE_ROOT); // Add root certificate for api.telegram.org
  #endif
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi..");
  }
  // Print ESP32 Local IP Address
  Serial.println(WiFi.localIP());
}

void loop() {
  if (millis() > lastTimeBotRan + botRequestDelay)  {
    int numNewMessages = bot.getUpdates(bot.last_message_received + 1);

    while(numNewMessages) {
      Serial.println("got response");
      handleNewMessages(numNewMessages);
      numNewMessages = bot.getUpdates(bot.last_message_received + 1);
    }
    lastTimeBotRan = millis();
  }
}
```

The code is compatible with ESP32 and ESP8266 NodeMCU boards (it’s based on the Universal Arduino Telegram Bot library example). The code will load the right libraries accordingly to the selected board.

### WIFI credentials
Insert your network credentials in the following variables.

```cpp 
// Initialize Wifi connection to the router
char ssid[] = "XXXXXX";     // your network SSID (name)
char password[] = "YYYYYY"; // your network key
```

### Telegram Bot Token
Insert your Telegram Bot token you’ve got from Botfather on the BOTtoken variable.

```cpp 
#define BOTtoken "XXXXXXXXXX:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"  // your Bot Token (Get from Botfather)
```

### Telegram User ID
Insert your chat ID. The one you’ve got from the IDBot.

```cpp 
#define CHAT_ID "XXXXXXXXXX"
```

## :speech_balloon: Step 4: Chat with Bot
Now display the text in your serial monitor.

You can edit within the loop of numNewMessages to add intelligence and customize the text it returns. Try sending an additional message using a second call to sendMessage. 

```cpp
bot.sendMessage(bot.messages[i].chat_id, "Are you alright comrade?", "");
```

## :sparkles: Step 5: LED Light
We are now going to apply rudimentary intelligence. Within the same loop we create an if / else if that listens to .text value.

If this value is equal to your command, for example “lights on”, turn on the built-in LED 
```cpp
(LED_BUILTIN = LOW)
``` 
Also think of what text you want in the else if to turn it off (HIGH).

``` cpp
if (text == "/start") {
      String welcome = "Welcome, " + from_name + ".\n";
      welcome += "Use the following commands to control your outputs.\n\n";
      welcome += "/led_on to turn GPIO ON \n";
      welcome += "/led_off to turn GPIO OFF \n";
      welcome += "/state to request current GPIO state \n";
      bot.sendMessage(chat_id, welcome, "");
    }

    if (text == "/led_on") {
      bot.sendMessage(chat_id, "LED state set to ON", "");
      ledState = HIGH;
      digitalWrite(ledPin, ledState);
    }
    
    if (text == "/led_off") {
      bot.sendMessage(chat_id, "LED state set to OFF", "");
      ledState = LOW;
      digitalWrite(ledPin, ledState);
    }
    
    if (text == "/state") {
      if (digitalRead(ledPin)){
        bot.sendMessage(chat_id, "LED is ON", "");
      }
      else{
        bot.sendMessage(chat_id, "LED is OFF", "");
      }
    }
```
## :computer: Step 6: Port and Board
Choose for the NodeMCU 1.0 (ESP-12E Module) and your connected port. To find this go to Tools > Board > esp8266 > NodeMCU 1.0 (ESP-12E Module).

![Board](image/Board.jpg)

For the port you also go to Tools > Port and select the right Port. It could be that you don't have any connection and your Port is disabled like this:

![Port Disabled](image/PortDisabled.jpg)
![No Port](image/NoPort.jpg)

There are some things you can do to fix that:
* Check the USB cable: Ensure you're using a data cable, not just a charging cable. Try a different USB cable if possible.
* Install drivers: NodeMCU usually needs CH340G or CP210x drivers.
Download and install the appropriate driver for your board.
* Restart your computer:
* Select the correct board
Try different USB ports
* Update Arduino IDE
* Check Windows security (Windows): Sometimes Windows security can block new USB devices.

## :arrow_up: Step 7: Upload code
Upload your code and open the Serial Monitor to verify if your WiFi is functioning. To upload the code, click the blue arrow in the top left corner. 

![Upload](image/Upload.jpg)

Nothing happening? Check the baud on your right, this should be 115200.

![No connection](image/NoConnect.jpg)

Having trouble locating the Serial Monitor? Click on the icon in the top right corner to open it. Then, set the baud rate to 115200 at the bottom. Upload the code again, and you should see the connection being established.

![No Serial Monitor](image/ConnectionBot.jpg)


## :high_brightness: Step 8: Turn The Lights On!
Now open your Telegram application on your smartphone. Go to BotFather and find the link to your newly created bot. The link should look like: t.me/(your named bot).

![Link bot](image/bot3.jpg)

Type /start and press enter to send it to your bot. As a result, this will show you a welcome message from the bot. All the different commands will be displayed which you can enter one by one.

* Send /led_on to turn GPIO2 ON
* Send /led_off to turn GPIO2 OFF
* Send /state to request the current GPIO state

![Led on and off chat](image/Led-aan-uit.jpg)

Now you should see the light turning on:

![Led on](image/Light.jpg)

## :bulb: Problem solving
### Serial Monitor
Can't see the Serial Monitor working? It could be that the wrong baud is sellected. Check the baud on your right, this should be 115200.

![No connection](image/NoConnect.jpg)

Having trouble locating the Serial Monitor? Click on the icon in the top right corner to open it. Then, set the baud rate to 115200 at the bottom. Upload the code again, and you should see the connection being established.

![No Serial Monitor](image/ConnectionBot.jpg)

### Port
Do you get an error message about your port not being connected but you got the board right? 

![Port Disabled](image/PortDisabled.jpg)
![No Port](image/NoPort.jpg)

There are some things you can do to fix that:
* Check the USB cable: Ensure you're using a data cable, not just a charging cable. Try a different USB cable if possible.
* Install drivers: NodeMCU usually needs CH340G or CP210x drivers.
Download and install the appropriate driver for your board.
* Restart your computer
* Select the correct board
Try different USB ports
* Update Arduino IDE
* Check Windows security (Windows): Sometimes Windows security can block new USB devices

## :information_source: Sources
* [DfETsr IOT Telegram](https://icthva.sharepoint.com/:w:/r/sites/FDMCI_ORG__CMD-Amsterdam/_layouts/15/Doc.aspx?sourcedoc=%7B6e77c9be-5af2-4c98-b951-5b30757ff56a%7D&action=view&wdAccPdf=0&wdparaid=1A6631C3)
* [Random Nerd Tutorials Telegram](https://randomnerdtutorials.com/telegram-control-esp32-esp8266-nodemcu-outputs/)
* [Microcontrollerslab Telegram](https://microcontrollerslab.com/telegram-esp32-esp8266-nodemcu-control-gpios-leds/)

## :question: What went wrong?
What i saw when downloading Universal Telegram Bot was:

![Universal error](image/library-universal.jpg)

Even though I had already downloaded ArduinoJson, the 1.1.0 version of the Universal Telegram Bot worked when I tried it. However, I’m not using the latest version of ArduinoJson because it wasn’t compatible with the 1.3.0 version of the bot for some reason. The 1.1.0 version of the Universal Telegram Bot is only compatible with ArduinoJson 5.13.5, so I used that version instead. 









