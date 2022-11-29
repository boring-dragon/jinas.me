---
title: "How to Make Console App With Symfony Console"
date: 2019-11-30T23:04:58+05:00
draft: false
---
![image](https://boring-dragon.sgp1.digitaloceanspaces.com/images/2cCwy.png)

In this tutorial, I am going to build out a simple console application using Symfony console component. The Console component allows you to create command-line commands. Your console commands can be used for any recurring task, such as cronjobs, imports, or other batch jobs.



#### Things required

 - PHP
 - Composer


To start you need to run:

```bash
composer init
```
If you don’t already have composer installed on your pc follow the link below to download the composer

Get Composer

Once you have initialized composer. run the following command below to include Symfony console in your application

```bash
composer require Symfony/console
```
after this step, I will add a psr4 autoloading in the composer.json file. And map it to the source folder. In the source folder, I will store all of my commands so that it will be easy to manage codes once I start to add new commands and functionality to the application.
```json
"autoload": {
        "psr-4": {
            "Acme\\": "src"
        }
```
To use the Symfony console component you need to first create a PHP script to define the console application.. an example is shown below:

```php
#!/usr/bin/env php
<?php
// app.php

require __DIR__.'/vendor/autoload.php';

use Symfony\Component\Console\Application;
use Acme\sayHelloCommand;

$application = new Application();

// The place you register the command for the application

$application->run();
```

Next step is to register a command. An example is shown below:

```php
$application->add(new sayHelloCommand());
```

Now that I have registered a `new command` I will now create a file called sayHelloCommand.php in my src directory. remember in the previous code I have imported the sayHelloCommand into the application according to the namespace I have set in my composer.json file.

In our sayHelloCommand.php the code looks like this:
```php
<?php
namespace Acme;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Output\OutputInterface;

class sayHelloCommand extends Command{

    public function configure(){
        $this->setName('sayhello')
             ->setDescription('Out puts Hello world to the screen')
    }

    public function execute(OutputInterface $output){

        $output->writeln('<info> Hello World </info>');
    }

```

In the above code, I have first written the namespace of the application and imported all the Symfony console components that I need for this console app to use.

I am extending the Command class of symfony console which gives me access to some of helper methods. Example: setName, setDescription.

For the next step, I am declaring two methods. The first method configures the command and the second method is responsible for handling any executions. In this case, it would be to output hello world to the screen.

In the configure() method I am setting the name for the command and a description to describe what the command does.

In execute() method I am importing OutputInterface that provided by symfony console component as $output.

So that I can use the methods provided by that interface.

For this example, I will be using the method called writeln

```php
$output->writeln('<info> Hello World </info>');
```

This method outputs a text to the console. Notice that i have used an info . This is some useful html like syntax symfony console offers to color your output text or data.

Hope this helps in any way. I will continue writing this tutorial into a bit advanced one when I get time. In the meantime check out more about Symfony console by clicking the link below
