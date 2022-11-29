---
title: "Lets Make Weather Station"
date: 2020-12-29T23:38:29+05:00
draft: true
---

This is sort of a tutorial that I put together to explain much as I can about the process of making a simple realtime weather station with Arduino and some web technologies. In this project, I will be attempting to get sensor data from a DHT11 temperature, Humidity sensor and displaying it in a nice web user interface.

For the back-end of the system, I will be using a Javascript web framework called Express. Sensor reading from the Arduino will be sent through a serial communication and with the help of javascript, I will be reading those serial communications and manipulating the data to emit to the frontend using socket.io. For an extra thing, I am also Integrating the MoosunMv API to display some extra weather information. MoosunMv API is an open API that is built on top of Maldives meteorology stations.

NOTE: All the weather information is coming from my own server. it won’t overload meteorology servers. Our servers get the weather information every 12hrs from the Maldives meteorology stations and store it our database :)

### Web Technologies used
 - Express
 - Socket.io
 - Vuejs
 - Axios

### Requirement:
 - Arduino Uno
 - DHT11 Sensor
 - Breadboard
 - 3 Jumper cables
 - USB cable type A/B Standard USB 2.0 cable

![image](https://boring-dragon.sgp1.digitaloceanspaces.com/images/components.avif)


### Hardware wiring:

Wiring the hardware components to arduino. The wiring diagram is shown below:

![image](https://boring-dragon.sgp1.digitaloceanspaces.com/images/DHT11-with-Arduino-UNO-wiring-diagram-schematic.avif)


Note: Sensor I am using has a built in resistor so one of the pin will be missing from my sensor.

### Writing the Arduino codes with Arduino IDE:
```c
#include "DHT.h"

//Sensor digital pin
#define DHTPIN 8
//Setting the sensor type
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

void setup() {
    //Starting the serial communication
    Serial.begin(9600);
    dht.begin();
}

void loop() {
    //delay for 2 second before reading again
    delay(2000);
    //setting humidityfloat humidity = dht.readHumidity();
    //setting temperaturefloat temperature = dht.readTemperature();

    Serial.print(temperature);
    //Output for splitting the string into two array during serial read.
    Serial.print(",");
    Serial.println(humidity);
}
```

Arduino library I used to read data from the sensor is [DHT-sensor-library](https://github.com/adafruit/DHT-sensor-library) by adafruit. DHT Library.

### Reading Serial data and sending to front-end:

After sending the sensor data through serial. I am using a javascript library called Serial Port to read the serial data sent by the Arduino and splitting the data string into two variable. Data is split from where the comma starts.

emitting the date to the front-end is done like so:

```js
var today = new Date();
var date = today.getDate() + "-" + (today.getMonth() + 1) + "-" + today.getFullYear();
weekday = [
    "Sunday", "Monday", "Tuesday", "Wednesday", "Thurday", "Friday", "Saturday"
];
var day = weekday[today.getDay()];
var secondDay = weekday[today.getDay() + 1];
var thirdDay = weekday[today.getDay() + 2];
var fourthDay = weekday[today.getDay() + 3];
```

So far what I have done is to get all the information I need to send to the front end using a WebSocket connection. Now I am going to use a javascript library called socket.io to send the data from the backend to the front-end in realtime.

Code for this part is shown below:
```js
io.sockets.emit('data', {
     humidity: humidity,
     temperature: temperature,
     date: date,
     day: day,
     secondDay: secondDay,
     thirdDay: thirdDay,
     fourthDay: fourthDay
});
```
Now, pretty much that’s all the things required to do in the backend. Not the most complicated thing in the world. The next day is sent to the frontend for a demonstration only. The temperature data on it is completely static. MOVING ON!!..

Let’s recall a bit. I am sending the sensor data from the Arduino to a serial port. In my case its COM5. Javascript Serial Port library than reads the serial data from the serial port and split the data according to where the split key is set. In this case, I am using a comma to separate the string into two variables. I am also setting some date and information about the data to sent out to the front-end. At last, I am emitting the data through a WebSocket using socket.io. In the front-end, socket.io is gonna listen to the port the data is coming from and I am using VUEJS to bind the data that is being received by socket.io to display them in the HTML.

### Connecting Everything together:
Front-end javascript code is shown below:
```js
feather.replace()

var socket = io.connect('http://127.0.0.1:4000/');
var temperature = new Vue({
    el: '#app',
    data: {
        ApiUrl: 'http://moosunmv.jinas.me/v1/latest',
        temperature: '',
        humidity: '',
        date: '',
        day: '',
        wind: '',
        secondDay: '',
        thirdDay: '',
        fourthDay: ''
    },
    methods: {
        getWeather(){
            var vm = this;
            axios.get(this.ApiUrl)
            .then(function (response) {
                vm.wind = response.data.wind;
                console.log(vm.wind);
            })
        }
    },
    mounted: function () {
        socket.on('data', function (data) {
            this.temperature = data.temperature;
            console.log(this.temperature);
            this.humidity = data.humidity;
            console.log(this.humidity);
            this.date = data.date;
            this.day = data.day;
            this.secondDay = data.secondDay;
            this.thirdDay = data.thirdDay;
            this.fourthDay = data.fourthDay;
        }.bind(this));

        this.getWeather();

    }

});
```
Data set by VUEJS is then bind to inside the HTML file. I am using a javascript library called [axios](https://github.com/axios/axios) to get the weather information from MoosunMv API.

It is done like so:

```html
<div class="info-side">
    <div class="today-info-container">
        <div class="today-info">
            <div class="precipitation">
                 <span class="title">PRECIPITATION</span>
                 <span class="value">0 %</span>
                 <div class="clear"></div>
                 </div>
                 <div class="humidity"> 
                    <span class="title">HUMIDITY</span>
                    <span class="value">{{humidity}} %</span>
                    <div class="clear"></div>
                    </div>
                    <div class="wind"> 
                        <span class="title">WIND</span>
                        <span class="value">{{wind}}</span>
                        <div class="clear">

                    </div>
                </div>
            </div>
    </div>
```

### Demo

Note: Html, Css template used in this project is taken from codepen. Shout out to [Colin Espinas](https://codepen.io/Call_in/pen/pMYGbZ).

That’s pretty much it for this project. You can find all the codes of this project down below:

- [Arduino codes](https://github.com/jinas123/weather-station-arduino)
- [Front-end and backend](https://github.com/jinas123/weather-station)