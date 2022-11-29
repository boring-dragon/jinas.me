---
title: "How to Make a Static Site Generator"
date: 2020-11-30T03:15:28+05:00
draft: true
---

Here is how I made a static site generator with PHP and [Laravel blade](https://github.com/PhiloNL/Laravel-Blade) templating engine. The library I used in this is called laravel-blade by Philo. You can also use Markdown as your content editing but in this one I am using JSON files because It's really easy to implement.

Code snippet of the static site generator
```php
<?php

require 'vendor/autoload.php';

use Philo\Blade\Blade;

class Gethi
{
    /**
     * init
     *
     * @return void
     */
    public function init()
    {
        //Content directory
        $contentDir = __DIR__ . '/content';
        //Scan the directory for files
        $contentFiles = array_diff(scandir($contentDir), array('.', '..'));

        foreach ($contentFiles as $file) {
            $data = json_decode(file_get_contents($contentDir . '/' . $file));

            $blade = new Blade('resources/views', 'cache');
            $content = $blade->view()
                ->make('default', get_object_vars($data))
                ->render();

            $outputDir = __DIR__ . '/dist' . $data->slug;
            if (!is_dir($outputDir))
                mkdir($outputDir, 0755, true);

            $outputpath = $outputDir . '/index.html';
            file_put_contents($outputpath, $content);

            echo 'Built Page ' . $outputpath . PHP_EOL;
        }
    }
}

$gethi = new Gethi;
$gethi->init();
```
As you can see the code for is really short and yet powerful enough to do dynamic renders. For any content that gets injected into the HTML file it will be stored inside a JSON file located in a `content` directory.

Here is the folder structure of the application
Folder structure
Folder structure

As you can see I have two pages ready to be rendered into an HTML. Let's look at one of the JSON files that gets converted to PHP variables and injected into `default.blade.php` template file which will be used.

Here is my Index.json File
```json
{
    "title" : "Landing page",
    "slug": "/",
    "subtitle": "This is the front page of the app",
    "items": [
        {
            "name": "Google",
            "url": "google.com"
        },
        {
            "name": "Twitter",
            "url": "twitter.com"
        },
        {
            "name": "Facebook",
            "url": "facebook.com"
        }
    ]
}
```
All of these JSON objects will get converted into the PHP variables using the PHP built-in function get_object_vars($data) and passing in the decoded JSON data of the file. Let's take a look at the default.blade.php file.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>{{$title ?? 'welcome'}}</title>
</head>
<body>
    <div class="wrapper">
            {{$title}}
    </div>

    @isset($items)
    @foreach ($items as $item)
    <ul>
        <li>{{$item->name}}</li>
    </ul>
    @endforeach
    
    @endisset
   
</body>
</html>
```
Here I have the basic HTML structure setup with some blade directives. Did you notice that the variable names are the same inside the JSON file ?. Yes it gets passed into this view. You can render it however you want. To render the list view of the items object what I am doing is checking if the items are being set and if the items exist then I am looping through the items and inserting item name inside a list view.

You can run the application using the PHP CLI with the command `php filename.php` it will then create a dist folder with the static templates.