---
title: "Sentiment Analysis Implementation"
date: 2020-12-08T03:31:36+05:00
draft: false
---

A few months ago I started collecting some dhivehi data for trying out sentiment analysis and so far collected about 800 data points. Which is not much but it was enough to try it out. I was just curious how this is going to turn out. This is mainly done for experimental purposes and I am very new to machine learning.

[Sentiment Analyis 101](https://jinas.me/posts/sentiment-analysis-101) : Article I wrote about collecting data for this project.

So tonight I tried to train a model with some of the data in the dataset. Most of the data were considered positive so I had to strip out evenly between the labels to make the predictions a bit accurate. It was not perfect.

To train the model I used A high-level machine learning and deep learning library for the [PHP](https://php.net/) called [Rubix ML](https://rubixml.com/).

Data
----

Data structured and labeled.

To structure the data this way I made a script based on PHP League CSV Reader

```php
<?php

require './vendor/autoload.php';

use League\\Csv\\Reader;

//load the CSV document from a file path
$csv = Reader::createFromPath('comments.csv', 'r');
$csv->setHeaderOffset(0);

$header = $csv->getHeader();
$records = $csv->getRecords();
$num = 0;

foreach ($records as $record) {

    $handle = fopen("data/nuetral/".$num."\_nuet.txt", 'w');
    fwrite($handle, $record\["comment"\]);
    fclose($handle);
    $num++;
}
```
Training
--------

To Setup the dataset I am making the folder names as labels and the content inside the .txt file as the sample it corresponds to.

```php
use Rubix\\ML\\Other\\Loggers\\Screen;
use Rubix\\ML\\Datasets\\Labeled;
use Rubix\\ML\\PersistentModel;
use Rubix\\ML\\Pipeline;
use Rubix\\ML\\Transformers\\TextNormalizer;
use Rubix\\ML\\Transformers\\WordCountVectorizer;
use Rubix\\ML\\Other\\Tokenizers\\NGram;
use Rubix\\ML\\Transformers\\TfIdfTransformer;
use Rubix\\ML\\Transformers\\ZScaleStandardizer;
use Rubix\\ML\\Classifiers\\MultilayerPerceptron;
use Rubix\\ML\\NeuralNet\\Layers\\Dense;
use Rubix\\ML\\NeuralNet\\Layers\\Activation;
use Rubix\\ML\\NeuralNet\\Layers\\PReLU;
use Rubix\\ML\\NeuralNet\\Layers\\BatchNorm;
use Rubix\\ML\\NeuralNet\\ActivationFunctions\\LeakyReLU;
use Rubix\\ML\\NeuralNet\\Optimizers\\AdaMax;
use Rubix\\ML\\Persisters\\Filesystem;
use Rubix\\ML\\Datasets\\Unlabeled;

use function Rubix\\ML\\array\_transpose;

ini\_set('memory\_limit', '-1');

$logger = new Screen();

$logger->info('Loading data into memory');

$samples = $labels = \[\];

foreach (\['positive', 'negative', 'neutral'\] as $label) {
    foreach (glob("data/$label/\*.txt") as $file) {
        $samples\[\] = \[file\_get\_contents($file)\];
        $labels\[\] = $label;
    }
}

$dataset = new Labeled($samples, $labels);

$estimator = new PersistentModel(
    new Pipeline(\[
        new TextNormalizer(),
        new WordCountVectorizer(10000, 3, 10000, new NGram(1, 2)),
        new TfIdfTransformer(),
        new ZScaleStandardizer(),
    \], new MultilayerPerceptron(\[
        new Dense(100),
        new Activation(new LeakyReLU()),
        new Dense(100),
        new Activation(new LeakyReLU()),
        new Dense(100, 0.0, false),
        new BatchNorm(),
        new Activation(new LeakyReLU()),
        new Dense(50),
        new PReLU(),
        new Dense(50),
        new PReLU(),
    \], 256, new AdaMax(0.0001))),
    new Filesystem('sentiment.model', true)
);

$estimator->setLogger($logger);

$estimator->train($dataset);

$scores = $estimator->scores();
$losses = $estimator->steps();

Unlabeled::build(array\_transpose(\[$scores, $losses\]))
    ->toCSV(\['scores', 'losses'\])
    ->write('progress.csv');

$logger->info('Progress saved to progress.csv');

if (strtolower(trim(readline('Save this model? (y|\[n\]): '))) === 'y') {
    $estimator->save();
}

```
Prediction
----------

```php
use Rubix\\ML\\PersistentModel;
use Rubix\\ML\\Persisters\\Filesystem;

ini\_set('memory\_limit', '-1');

$estimator = PersistentModel::load(new Filesystem('sentiment.model'));

while (empty($text)) $text = readline("Enter some text to analyze:\\n");

$prediction = $estimator->predictSample(\[$text\]);

echo "The sentiment is: $prediction" . PHP\_EOL;
```

Here is some prediction is made. It is still not that accurate. I will work on improving it and the dataset. I am still getting into machine learning myself and this project is mainly for me to learn these things and so far I am really happy with the results and how it turned out.


### Github: [sentiment-analysis-implementaion](https://github.com/jinas123/sentiment-analysis-implementaion)