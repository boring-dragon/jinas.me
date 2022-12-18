---
title: "Bot to Automatically Predict World Cup Score Purplelane App"
date: 2022-12-15T06:48:35+05:00
draft: false
---
The end of the world-cup is finally getting near & everyone rushing to predict the final score of the game. I thought I would share an interesting thing I made just two weeks into the world-cup. Since the start of it everyone and almost all the companies were posting predict and win score guessing competitions and among them was also the purple-lane app.

Purple-lane is this amazing reward program app based in Maldives that helps companies to enrol & manage their customers in their own reward programs such as coupons, discounts & more.

I signed up for the world-cup Foari that they were doing and started making predictions of the match of my own. Almost everything I predicted wrong. And I was also missing matches because I wasn’t able to watch or keep up with the world-cup schedule. So I looked under the hood of how purple-lane API worked & found the end-point to predict the score for the match. There was a prediction close time for each fixture of the match so I knew from the pattern of the matches so far If neither of teams scores before the prediction closes (which was approx 70mins into the game) I would get points. And for extra monitoring I also made a telegram bot to send me a notification every-time the prediction bot made a prediction.

![telgram bot](/telegramss.png)

So I made the bot in like few hours & I initially made it to predict only the last 2 mins before prediction closes but that had a major flaw when sometimes when the cron job failed. I instead then changed the bot to predict the current score every 2 mins until the prediction close time. I got lot of points and even some how it was able to reach the leaderboard.

![Leader board](/leaderboard_22.png)

I think if the bot was active from the beginning of world-cup I would have had a change to win some goodies. But it was just unpredictable in the knock-out matches & most matches after that. But it was a really fun experiment regardless.

Below is the link to the GitHub repo of the project. Don't mind the messy code I was in a rush.

Cheers

[Github](https://github.com/boring-dragon/purple-lane-predict-bot)