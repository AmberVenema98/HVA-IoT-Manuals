# Smart Breeze

## Introduction
In this project we create a simulation of a smart ventilator with a NodeMCU and the OpenWeatherMap API. The goal is to collect real-time weather data, such as temperature, and send notifications. 

We connect the NodeMCU to WiFi to request the current temperature of a specific location via the internet. If the temperature rises above a certain degree, the system switches "on", and if the temperature drops, it switches "off". These messages become visible in the Serial Monitor of the Arduino IDE. 

In this manual I will tell you how to build this. 

### What do you need?
* NodeMCU (ESP8266 of ESP32)
* WiFi-verbinding
* API-key from [OpenWeatherMap](https://openweathermap.org/)
* Key from [IFTTT](https://ifttt.com/) (Sending automatic notifications)

## Step 1: Get API from OpenWeatherMap
### Create an account
First we make an account on [OpenweatherMap](https://openweathermap.org/). Go to "Sign Up", click on "Create an Account" and follow the steps.

![Sign in](image/SignIn.jpg)

![Create account](image/CreateAccount.jpg)

After you've created an account make sure to verify your email in your mailbox. Otherwise your API Key won't work.

![Verify email](image/VerifyEmail.jpg)

Once you come back to the OpenWeatherMap website you should see that your account has been verified. 

![Succes mail](image/SuccesMail.jpg)

### API Key
To find your API Key go to your account name and click on "My API Keys"

![To API Key](image/ToAPI.jpg)

![API Key](image/APIKey.jpg)

## Step 2: Install Arduino IDE and libraries

## Step 3: Connecting to OpenWeatherMap

## Step 4: IFTTT Applet

## Step 5: Getting notifications

## Sources
* https://openweathermap.org/current#geo
* https://randomnerdtutorials.com/esp32-http-get-open-weather-map-thingspeak-arduino/
* https://ifttt.com/explore/weather-automations
* https://www.learnrobotics.org/blog/connect-arduino-to-ifttt-for-iot-projects/

