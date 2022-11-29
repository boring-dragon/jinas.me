---
title: "Sentiment Analysis 101"
date: 2020-12-30T03:28:48+05:00
draft: false
---

For the past few days, I have been trying to make a sentiment analysis tool for Dhivehi. So to do this I had to get a lot of Dhivehi text people have written. What I did was index the comments on a couple of different news site and as of now, the dataset includes more than 11k comments in it. To do this I used a tool that I built a few months ago called [Grab.](https://github.com/jinas123/grab) Grab is an indexing CLI tool for WordPress API. Built with laravel Zero.

Here comes the hard part of this project!. I have to Label 11k comment according to how positive, negative, or neutral the comment is. Big problem! It's just too hard to do and time-consuming to do on database end. So I made a tool that could automatically display random comments inside the database and given the option to label it will label the selected comment inside the database. Also, the biggest benefit would do be to have others contribute to the project which will speed up the process of actually getting the dataset done and starting with the training phase. As of now, 404 comments have been labeled. The codebase for the tool is experimental since I was in a bit of a hurry to build it. I will refactor it a bit later on. The tool itself is built with Laravel and Livewire. Just a little bit of an update about my life 😁. How was your week going?😊

Data collected from:

*   [Vaahaka.com](https://vaahaka.com)
*   [Dhuvas.mv](https://dhuvas.mv/)
*   [Addulive.com](https://www.addulive.com/)

[Sentiment](https://github.com/jinas123/sentiment)

Dhivehi Text labeling tool to easily create datasets for sentiment analysis. Built with laravel and livewire. (Experimental Project)

![](/storage/wink/images/8QMGphs72B9FxlVgJj6MEAnVkNP4HBqfAu0WxahP.png)