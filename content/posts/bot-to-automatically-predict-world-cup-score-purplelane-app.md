---
title: "Bot to Automatically Predict World Cup Score In Purplelane App"
date: 2022-12-15T06:48:35+05:00
draft: false
tags:
    - bots
    - world-cup
---

### Intro
The end of the world cup is finally getting near & everyone rushing to predict the final score of the game. I thought I would share an interesting thing I made just two weeks into the world cup. Since the start of it everyone & almost all the companies were posting predict and win score guessing competitions and among them was also the purple-lane app.

Purple-lane is this amazing reward program app based in Maldives that helps companies to enroll & manage their customers in their reward programs such as coupons, discounts & more.

I got signed up for the world-cup Foari that they were doing and started making predictions of the match of my own. Almost everything I predicted was wrong. And I was also missing matches because I wasn’t able to watch or keep up with the world-cup schedule. So thought of making the process automated.

### Initial plan & Steps

- [x] Get all the Live fixtures from worldcup API
- [x] Get the fixtures to predict from PurpleLane API
- [x] Convert Local time ( Qatar Time) to Maldives Time
- [x] Mins Before `prediction_closes_at` equals to current time. Get the live score of the world cup fixture
- [x] Predict with for the given email with away team score & home team score
- [x] Send notification over telegram when the bot makes a prediction
- [x] Host & run the app periodically on server
  
So I looked under the hood of how purple-lane API worked & found the end-point to predict the score for the match & the live fixtures.

##### Sample fixture response from their API
```json
{
			"id": 2414,
			"uuid": "97d6a6c5-9620-4a01-928d-76f26f22970d",
			"participant_id": "279",
			"fixture_id": 31,
			"home_team_score": "0",
			"away_team_score": "0",
			"status": 1,
			"prize_won": 0,
			"won": 0,
			"created_at": "2022-11-26T16:56:07.000000Z",
			"updated_at": "2022-11-26T17:56:59.000000Z",
			"fixture": {
				"id": 31,
				"uuid": "97c7a238-d1c0-469a-a150-16fd2d185e74",
				"stage": "Group D",
				"tournament_id": 1,
				"home_team_id": 13,
				"home_team_score": "2",
				"away_team_id": 15,
				"away_team_score": "1",
				"prize": 2,
				"prediction_closes_at": "2022-11-26T17:22:00.000000Z",
				"kick_off_time": "2022-11-26T16:00:00.000000Z",
				"match_status": 3,
				"created_at": "2022-11-19T05:47:08.000000Z",
				"updated_at": "2022-11-26T17:56:42.000000Z",
				"date": "26-11-2022",
				"time": "21:00:00",
				"home_team": {
					"id": 13,
					"tournament_id": 1,
					"name": "France",
					"code": "FRA",
					"logo": null,
					"flag": "GH2ZVtbsLaTpS1WBCw22VY1jecILdFWmMsctsowb.png",
					"created_at": "2022-11-16T02:56:09.000000Z",
					"updated_at": "2022-11-16T02:56:09.000000Z"
				},
				"away_team": {
					"id": 15,
					"tournament_id": 1,
					"name": "Denmark",
					"code": "DEN",
					"logo": null,
					"flag": "dPwWtRNjPgOGlqQp3HV4grvOnyBC4td74JsAZtkI.png",
					"created_at": "2022-11-16T02:56:50.000000Z",
					"updated_at": "2022-11-16T02:56:50.000000Z"
				}
			}
		}
```

 There was a `prediction_closes_at` time for each fixture of the match so I knew from the pattern of the matches so far that If neither of the team's scored before the prediction closes (which was approx 70mins into the game) I would get points. And for extra monitoring, I also made a telegram bot to send me a notification every time the prediction bot made a prediction.

![telgram bot](/telegramss.png)

So I tested and made the bot in like few hours & I initially made it to predict only the last 2 mins before the prediction closes but that had a major flaw when sometimes when the cron job failed. I instead then changed the bot to predict the current score every 2 mins until the prediction close time. I got a lot of points and even somehow I was able to reach the leaderboard!

![Leader board](/leaderboard_22.png)

I think if the bot was active from the beginning of world-cup I would have had a chance to win some goodies. But it was just unpredictable in the knock-out matches & most matches after that. But it was a really fun experiment regardless.

Below is the link to the GitHub repo of the project. Don't mind the messy code I was in a rush.

Cheers

[Github](https://github.com/boring-dragon/purple-lane-predict-bot)