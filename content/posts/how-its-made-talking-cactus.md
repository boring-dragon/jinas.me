---
title: "How Its Made: Talking Cactus"
date: 2020-02-08T23:30:24+05:00
draft: false
---
![image](https://boring-dragon.sgp1.digitaloceanspaces.com/images/photo_2019-10-23_02-00-40.avif)

## Story
This is a mini project I made for fun. It was done at the beginning of this year and I thought I should share the story behind the making of this. It’s a cactus that tweet when it needs water :P. The idea started as an inspired project. I was watching a ted talk called How Arduino is open-sourcing imagination. 

The video is shown below.

{{< youtube UoBUXOOdLXY >}}


The guy who gave the speech showed a plant which is connected to an Arduino. Arduino measures the well being of the plant and sends tweets about it using the twitter API. Its a really interesting talk.

I thought about the concept for a while. And I wanted to make my cactus to tweet when the plant needs water. I had a soil moisture sensor laying around and the code side of things are really easy to make. The first thing I did is make a twitter account named buttercup cactus and register as a developer and I got the API key from the twitter that I need to integrate into my application. The only thing I had to do was to read the soil moisture level of the soil and send the data through Serial. The Arduino is connected to my laptop and I used python to read the serial data sent by the Arduino and run a condition to see if the water level is below a certain value. If its below that value the python script calls the Twitter API with the Message saying:

`HEY!! I NEED WATER. My thirsty level is 200`

Here is a screenshot:
![image](https://boring-dragon.sgp1.digitaloceanspaces.com/images/photo_2019-03-18_08-49-11.avif)

The thirsty level is the water level in the soil which is sent by the Arduino after reading it through the soil moisture sensor. That’s really is there to it. Nothing complicated. This was just a fun project I wanted to try out. Let’s make your plants come to life :)

I love to challenge myself because it helps me to keep improving myself and also to learn new things. and also to make cool things. I am no expert in these things. I am still learning. Almost all the stuff I learned from the internet and tutorials. Anyone could learn to make creative and cools things. You just need determination, Curiosity, and an open mind. If anyone of you is interested to make your plant come to life I left the codes I used down below. Feel free to edit the codes or to use them. Unfortunately, the cactus died. I forgot to water it :(



## Things I used:

 - Soil moisture sensor
 - Arduino nano
 - Python

Arduino codes:

```c
//Analog pin thats connected to the sensor
int sensorPin = A0;
//Variable to store the sensor reading
int sensorValue;

void setup() {
    Serial.begin(9600);
    pinMode(13, OUTPUT);
}

void loop() {

    //Read and store the value of sensor data
    sensorValue = analogRead(sensorPin);
    //Output the value to the serial
    Serial.println(sensorValue);

    //Wait for 1 second before reading again
    delay(1000);
}
```

I have left some comments to state what the code actually does.

Python codes:
```python
# importing the module 
import tweepy
import time
import serial
  
# personal details 
consumer_key =""
consumer_secret =""
access_token =""
access_token_secret =""

serial_port = 'COM4' #Define serial port
baud_rate = 9600 #In arduino, Serial.begin(baud_rate)
  
# authentication of consumer key and secret 
auth = tweepy.OAuthHandler(consumer_key, consumer_secret) 
  
# authentication of access token and secret 
auth.set_access_token(access_token, access_token_secret) 
api = tweepy.API(auth) 
  
# update the status 


ser = serial.Serial(serial_port, baud_rate)
while True:
    line = ser.readline()
    line = line.decode("utf-8")

    value = float(line)
    
    if (value < 300):

        api.update_status(status ="HEY!! I NEED WATER.. My thirsty level is {}".format(value))
    else:
        print("i am alright.. My thirsty level is {}".format(value))
    time.sleep(600)     

```  

Personal details are set according to your API credentials.