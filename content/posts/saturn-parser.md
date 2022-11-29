---
title: "Saturn Parser"
date: 2020-01-20T23:28:09+05:00
draft: true
---
#### Saturn Parser - Extract contents from a url

Saturn Parser is a composer package that extracts the bits that humans care about from any URL you give it.

#### Story

Couple of days ago I came across an interesting library written in javascript called mercury this library allows us to extracts the bits of the content from any given URL. That includes article content, titles, authors, published dates, excerpts, lead images, and more. I was surprised by how nicely this library worked and wanted to try to make one for myself in PHP. So I started building the same kind of parser called Saturn. It didn’t quite work the way I wanted at first but over time i kept improving it and started to add new features and also extracted core functionality to separate classes to make the code look elegant and clean.

After a few days, I finished the prototype of the library. It was able to fetch metadata from a URL. For scraping the website I used a PHP DomDocument class. I wanted to do the scraping part with goutte because it has more functionality but I wanted to try it out first without a library. Maybe, Later on, I will include goutte and improve the scraping capabilities at some point. Php DomDocument also had some downside to it. Using a well-maintained scraper will be easier. I will integrate goutte web scraper to a future update. The code is still bit rusty and a lot of improvements need to add to the Extractors.

#### Installation:
Command:
```bash
composer require jinas/saturn
```

Source codes:
 - [Github](https://github.com/jinas123/saturn)
 - [Packagist](https://packagist.org/packages/jinas/saturn)