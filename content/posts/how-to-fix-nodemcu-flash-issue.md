---
title: "How to Fix Nodemcu Flash Issue"
date: 2019-11-29T11:37:12+05:00
draft: true
---

![image](https://boring-dragon.sgp1.digitaloceanspaces.com/images/617T2JKnxiL._SX522_.jpg)

Here is a quick fix for the flashing issue in node mcu

Install python 2.7 or 3 download here 
Install pyserial download here 
Download to the esptool.py from here save it in the python installation folder
Download nodemcu_latest.bin from here and save it in the python installation folder


Edit `esptool.py` so that:

```C
ESP_RAM_BLOCK = 0x180
ESP_FLASH_BLOCK = 0x40
```


Open terminal and go to the python folder and type in this (change the COM7 to whatever your FTDI is)

`python esptool.py -p COM7 write_flash 0x000000 nodemcu_latest.bin`