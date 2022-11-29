---
title: "Writing Thaana on Lcd Display"
date: 2020-01-29T23:25:37+05:00
draft: true
---

So for the past few weeks, I have been doing bit of research and digging on how to write Dhivehi thaana to a 16*2 LCD. What I found out is that it’s not hard to do it. So the thing I had to do was to assign custom byte codes that match the letters in the Dhivehi thaana. So the easiest way to think about this was making a 5 * 8 squares like a table Shown below.


So what I ended up doing was drawing custom characters that looked like Thaana letters and convert them to bytes and assign as a custom character in Arduino. I was searching for an alternative way of converting the custom characters to byte code and came across this website. LCD-Character-Creator. which lets you draw custom characters in squares and it automatically converts it to byte codes. A really handy tool to make any custom characters or objects for LCD. The display i used was 1602A. Keep in mind that custom character creation for every display will be different and some might not offer the libraries out of the box to do so.

So i took this idea and started making custom characters and as of now made a few Thaana letters and displayed them in the LCD.

Here are a few examples:


byte code for noonu is shown below.
```c
byte noonu[] = {
  B10101,
  B10101,
  B10101,
  B11111,
  B10000,
  B10000,
  B10000,
  B10000
};
```

byte code for shaviyani is shown below.
```c
byte shaviyani[] = {
  B00001,
  B11001,
  B11001,
  B11111,
  B10000,
  B10000,
  B10000,
  B10000
};
```

You can clearly see it is possible and easy to do. I have been experimenting with this one and made a few more characters. I am still a bit new to these but it was a fun learning process. Next step would be to write parser to convert things automatically to thaana.

So can do things like:
```c
thaana.write("ދިވެހިބަސް");
```

And automatically convert them to byte codes and display on the LCD screen. One of the main challenges I faced was not being able to accurately create characters. Some of them are accurate but it is hard to do because the LCD I am using has some limitations. Because the size of the display is not that huge. I will keep on trying to figure out how to improve this and to write a library. This mini project started as a question. because I wanted to see if this was possible to do so.

If any of you want to point out something, want to collaborate or have any other approach of doing this leave a comment down below. And I am not the best of writers. sorry for grammar or spelling mistakes :)

I will push the code to GitHub soon.