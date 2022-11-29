---
title: "Making a Bridge Between Cloudflare and Nodemcu"
date: 2020-12-01T03:00:59+05:00
draft: true
---
![image](https://boring-dragon.sgp1.digitaloceanspaces.com/images/cloudflarenodemcu.avif)

## Story
This is a project I did over weekends to try out something new and learn. The idea was to get the Cloudflare analytics data from the Cloudflare API and display the total amount of requests made to my site in an LCD using a Nodemcu.

My approach was to make a custom API that communicates with the Cloudflare API and cache the response so the Nodemcu can request without limiting any request and it was easy to parse the JSON data with PHP and give out the data only I wanted to the Nodemcu.

To write the API I used a PHP micro-framework called [Flight](http://flightphp.com/). Flight is a fast, simple, extensible framework for PHP. Flight enables you to quickly and easily build RESTful web applications.

Here is a visualized concept:

![image](https://boring-dragon.sgp1.digitaloceanspaces.com/images/cloudflarediagram1.avif)


NOTE: My drawings and stuff are lame :P

## Things used

### WEB:
 - [Flight](http://flightphp.com/) - PHP micro-framework
 - [Jsonify](https://github.com/jinas123/jsonify) - Composer package I made to easily make JSON responses.
 - [jamesryanbell/cloudflare](https://github.com/jamesryanbell/cloudflare) - Cloudflare PHP Client.
 - [phpfastcache/phpfastcache](https://github.com/PHPSocialNetwork/phpfastcache) - Fast Caching library for PHP applications.


### NODEMCU:
 - ESP8266WiFi - Library to use the built-in wifi module.
 - ESP8266HTTPClient - Library to send HTTP request.
 - ArduinoJson - Library that lets you parse JSON.

#### Custom API
This API will contain only one route that returns all the analytics data as JSON.

`route.php`
```php
/*
============================================
        Routes
============================================
*/

use App\Controllers\AnalyticsController;

$analytics = new AnalyticsController();

Flight::route('/analytics', array($analytics, 'analytics'));
```

analytics routes use the analytics controller. analytics controller contains one method called analytics() that is responsible for getting the Cloudflare analytics report and cache it for 2mins inside the API and parse the JSON and give back HTTP response. Cloudflare Email and the Token is set in the environment file of the application.

AnalyticsController.php

```php
<?php
namespace App\Controllers;

use Jinas\Jsonify\Util;
use Phpfastcache\Helper\Psr16Adapter;

class AnalyticsController
{
    public function analytics(){
        //Response Utility
        $response = new Util();

        $defaultDriver = 'Files';
        $Psr16Adapter = new Psr16Adapter($defaultDriver);

        if (!$Psr16Adapter->has('cloudflaredata')) {
            // Setter action
            $client = new \Cloudflare\Api(getenv('CLOUDFLARE_EMAIL'), getenv('CLOUDFLARE_APIKEY'));

            $analytic = new \Cloudflare\Zone\Analytics($client);
            //First argument is the zone id in the cloudflare.
            $array = json_decode(json_encode($analytic->dashboard('50515cfef4495e8bb32f79f6b28b1b54')), true);

            $index = $array["result"]["timeseries"][6];
            $Psr16Adapter->set('cloudflaredata', $index, 120); // 2 minutes before cache expire
        } else {
            // Getter action
            $index = $Psr16Adapter->get('cloudflaredata');
        }

        $request = $index["requests"]["all"];
        $bandwidth = $index["bandwidth"]["all"];
        $pageviews = $index["pageviews"]["all"];
        $uniques = $index["uniques"]["all"];


        if (!array_key_exists("MV", $index["bandwidth"]["country"])) {
            $country_bandwidth = null;
        } else {
            $country_bandwidth = $index["bandwidth"]["country"]["MV"];
        }


        $data = [
            'total_request' => $request,
            'total_bandwidth' => $bandwidth,
            'total_pageviews' => $pageviews,
            'total_unique_users' => $uniques,
            'total_bandwidth_local' => $country_bandwidth
        ];

        echo $response->sendResponse($data, 'data retrieved successfully');
    }
}
```
Whoohoo!. So basically the API part is pretty much done. Now all I need to do is connect the Nodemcu to the API and display it in the LCD. The API is hosted on my local network using a raspberry pi in the same network the Nodemcu

Connecting everything together
At this point, I am all set to send HTTP request to my API from the Nodemcu. Not so fast!.

Here is the Arduino codes I wrote for the Nodemcu to interface with my API.

getdata.ino

```c
// Importing the libraries
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <ArduinoJson.h>

#define LEDRED D8
#define LEDYELLOW D7
#define LEDGREEN D6

const char* ssid = "";
const char* password = "";

void setup() {

pinMode(LEDRED, OUTPUT);
pinMode(LEDYELLOW, OUTPUT);
pinMode(LEDGREEN, OUTPUT);

Serial.begin(9600);
WiFi.begin(ssid, password);

while (WiFi.status() != WL_CONNECTED) {

delay(1000);
Serial.println("Connecting..");

}


}

void loop() {

if (WiFi.status() == WL_CONNECTED) { //Check WiFi connection status

HTTPClient http;  //Declare an object of class HTTPClient

http.begin("http://192.168.1.8:8000/analytics");  //Specify request destination
int httpCode = http.GET();                                                                  //Send the request

if (httpCode > 0) { //Check the returning code

String payload = http.getString();   //Get the request response payload
StaticJsonDocument<500> doc;

DeserializationError error = deserializeJson(doc,payload);   //Parse messageif (error) {      //Check for errors in parsing
    Serial.print("deserializeJson() failed with code ");
    Serial.println(error.c_str());
    delay(5000);
    return;

  }

int totalrequest = doc["data"]["total_request"];

  if(totalrequest >= 300)
  {
  digitalWrite(LEDRED, HIGH);
  delay(400);
  digitalWrite(LEDRED, LOW);
  delay(400);
  }

  if(totalrequest < 300 && totalrequest >= 250)
  {
    digitalWrite(LEDYELLOW, HIGH);
    delay(400);
    digitalWrite(LEDYELLOW, LOW);
    delay(400);
  }

  if(totalrequest < 250)
  {
    digitalWrite(LEDGREEN, HIGH);
    delay(400);
    digitalWrite(LEDGREEN, LOW);
    delay(400);
  }
Serial.println(totalrequest);

}

http.end();   //Close connection

}

delay(10000);
}
```
If you check the code you might have noticed there is leds in them. This is the point in the project I was stuck with an issue. Nodemcu gives out a voltage 3.3V but the LCD I am using needs 5V to operate. I forgot to check it before. So I tried to connect a 7 segment numeric LED but it also requires more digital pins in the Nodemcu 🤦‍♂️. But I found a way to solve this issue.

The solution is to connect an external power source to the components that need more power and let the Nodemcu do all the logical work. So for the time being connected 3 LED indicates the request limits. Green LED for Safe,Yellow LED for warning and a RED LED for danger if the request limit reaches a certain limit. Not much but it workss!!. Sometimes I don’t achieve the exact thing I want to do but failing, learning and trying it again is the point right??. Hope this is an interesting read. I just wanted to share my story of trying to implement this idea. 

- [API Source Code](https://github.com/jinas123/cloudflare-flight)