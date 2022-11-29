---
title: "Aggregate Cheapest Products From Online Shops in Maldives"
date: 2020-11-30T03:24:39+05:00
draft: true
---

### Story

For a long time I have wondered how these product aggregation websites work?. These are the websites that list the shops where you can buy the product you specified cheapest. So that you can easily buy the product at the cheapest store and save up a lot of money. I wanted to make an application that could do this.

I did a little researched and found out it wasn't that hard to do and would n't be hard to implement.

I found this paper to be really helpful to get an Idea of how it is going to work. It is a paper about "web scraping for food price research". So I started coding the application in PHP.

### Web Scraping
Scraping is the key ingredient of this project to make things work. So the first thing I did was list out all the online shops I wanted to scrap for products. Naming conventions are different in every site so it won't be that accurate to implement it in Maldivian online shops. Then again this a project to see if I could make this work. I wrote the whole scraping process by treating a shop as a driver that follows the same convention so that it would be easier to scrap them all asynchronously. To make asynchronous scraping work with PHP I am using a library called Buzz React. Buzz React is a simple, async PSR-7 HTTP client for concurrently processing any number of HTTP requests, built on top of ReactPHP.

While I was writing the scraping drivers for the shopping sites I noticed that most of the site underlining classes are the same which indicates that it was built with the same tool. An e-commerce solution called [Shopify](https://www.shopify.com/).

### Processing Multiple Requests
The next piece of the puzzle for me is to process multiple requests concurrently. To make this work [Buzz react](https://github.com/clue/reactphp-buzz) has this awesome function that awaits an array of Response objects.

```
Block\awaitAll($promises, $loop);
```
To Easily have all the promises returned by the driver in once place was a bit challenging to figure out but I figured it out!. So what I did was have an array that registers those promises that later can be resolved out of the array for processing. Almost like a container to put all the drivers (Each shop I am scraping) promises into a box.

`Container.php`

```php
<?php

namespace Jinas\Aggregator;

class Container
{
    public static $items;
    public static function register($value)
    {
        static::$items[] = $value;
    }
}
```
After that, I could easily just register any drivers in the application that would be later resolved out as an array for processing. Below You can see how I registered those drivers.

```php
Container::register((new DhimartDriver($browser))
            ->scrape(dot($this->configs)
                ->get('shops.dhimart.search') . "milk chocolate"));

Container::register((new EatMvDriver($browser))
            ->scrape(dot($this->configs)
                ->get('shops.eatmv.search') . "milk chocolate"));
```
So now you might be thinking "How I am gonna resolve these ?". Well, it's easy at this point. Here is how I did it.
```php
$products = Block\awaitAll(Container::$items, $loop);
```
This will Resolve all the promises inside the container and return the result as an array.

Whoohoo! Now we got the data that we needed. Keep in mind that this might not be accurate since the naming conventions are a bit harder to get when I am through every shopping site's search query. In this case, I passed "milk chocolate" as a query param. Close Enough 🤔.

### Saving all the Data Inside a Database
Now I have arrived at one of the final stages of this project!. Now we get the data we wanted from our shops. Its time to store these data inside a database so that we could later filter using these data. You might be wondering "how I plan on figuring out what products to scrap. You can't just manually type something always right?". Well, for that my idea is to use a lookup table of predefined product name and index all the sites accordingly and insert the results into the database.

```php
//Lookup Table Products
    "products" => [
        "milk chocolate",
        "peanut butter",
        "breads"
    ]
```
Storing the products inside a database straight forward. I have a DB helper class that takes care of this.

// Loop through all the products and create records inside the database
(new DB)->create($products);
This code just creates a DB class instance and pass the array of products into a create method that just loops through the products and insert them into the database. To always have up to date product prices I planned to run a cron job that takes care of data scraping and refreshing the database. You could of course log these data inside a CSV file but I am using a SQLite database in my case.

### Filtering the Products to Find the Cheapest Shop

Quick recap. I scraped the sites for the products I specified and stored them inside the database and now its time to filter through them given a product name as a query. Now I have to query the data I collected whenever I have to find the cheapest shop to buy that product.

SQL query to filter the Products by title.
```mysql
select count(*) as aggregate from `products` where `title` like '%bread%'
```

Could go even further and get the exact cheapest product but since the results of the name are not accurate from the site's search queries the ending result might not be accurate. Anyways you could just easily get all the products named with "bread" and that product model will contain the store where the product is from.

### Closing Thoughts

This was just a fun project I did to experiment to see if this idea could work. There are multiple ways of doing things. Still, I am trying to figure out more. If you want to check out the code base of the project. I made the whole project open source on Github. Codebase might not be that clean since I am still trying to figure things out here and there. I will try to write more drivers when I get time. Cheers 😊

Github: [Product Aggregator](https://github.com/jinas123/product-aggregators)