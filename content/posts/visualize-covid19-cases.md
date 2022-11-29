---
title: "Visualize Covid19 Cases"
date: 2020-11-30T03:12:03+05:00
draft: false
---

Its been a while since I wrote an article or posted any blog posts. Been carried away from things lately. Anyways I thought it might be interesting to do some visualizations. Let’s visualize some time-series!

In this code snippet, you will see a time-series visualization of covid19 cases in the Maldives. The code is written and tested in a laravel environment so it will be easy for me to implement and test it out. You can use whatever framework or even vanilla PHP to do this.

### Timeseries

Controller Code Snippet
```php
$confirmed = Http::get('https://coronavirus-tracker-api.herokuapp.com/confirmed')->json();
$confirmedData = collect($confirmed['locations'])->where('country_code', 'MV')->first();

$chartData = [
    ['Date', 'Confirmed']
];

collect($confirmedData['history'])->sortBy(function ($numConfirmed, $date) {
    return Carbon\Carbon::createFromFormat('n/j/y', $date)->startOfDay();
})->map(function ($numConfirmed, $date) use (&$chartData) {
  $chartData[] = [$date, $numConfirmed];
});

return view('welcome', [
    'chartData' => $chartData,
    'country' => $confirmedData['country']
]);
```
The first part of the code is sending a get request to coronavirus-tracker-API using laravel built-in HTTP client and storing the response in a variable called confirmed. And I am filtering through the collection where the country_code of the result array returned from the API matches the country code given. In this case, I am filtering by the Maldives. I am using carbon to format the history date and passing the Data back to the view to render in a chart.

View Code Snippet
```html
<body>
    <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
    <script type="text/javascript">
        google.charts.load('current', {'packages': ['corechart']});
        google.charts.setOnLoadCallback(drawChart);
        function drawChart() {
            var data = google.visualization.arrayToDataTable(@json($chartData));
            var chart = new google.visualization.AreaChart(document.getElementById('chart'));
            chart.draw(data, {
                title: 'Covid19 cases in {{ $country }}',
                legend: {position: 'bottom'},
                width: '100%',
                height: '900'
            });
        }
    </script>
    <div style="height:100%;width:100%;" id="chart"></div>
</body>
```
To visualize the chart data I am using the chart library from google and injecting the data array as JSON from the controller into the chart using laravel blade. That’s about it!

### Covid19 cases in Maldives by gender of the cases

Controller Code Snippet
```php
$cases = Http::get('https://covid19.health.gov.mv/api/fetch_cases')->json();
$male = collect($cases["cases"])
    ->where('gender', "Male")
    ->count();
$female = collect($cases["cases"])
    ->where('gender', "Female")
    ->count();

$chartData = [['Gender', 'Value'], ["Male", $male], ["Female", $female]];

return view('welcome', [
    'chartData' => $chartData,
]);
```
In this visualization first thing, I am doing is sending a get request to HPA API and passing the result into a collection and filtering the cases where the gender matches the Male or Female and counting the number of results. Finally, the results get passed to the view.

View Code Snippet

```html
<body><script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script><script type="text/javascript">
        google.charts.load('current', {'packages': ['corechart']});
        google.charts.setOnLoadCallback(drawChart);
        function drawChart() {
            var data = google.visualization.arrayToDataTable(@json($chartData));
            var chart = new google.visualization.PieChart(document.getElementById('chart'));
            chart.draw(data, {
                title: 'Covid19 cases by gender.',
                legend: {position: 'bottom'},
                width: '100%',
                height: '900'
            });
        }
    </script><div style="height:100%;width:100%;" id="chart"></div>
</body>
```
Inside the view, I am doing the same thing as before. Only the changing the chart type to PieChart. Since I am using arrayToDataTable function there is no need to structure the data inside the front-end.

### Covid19 cases in Maldives by nationality of the cases

Controller Code Snippet
```php
$cases = Http::get('https://covid19.health.gov.mv/api/fetch_cases')->json();

$chartData = [['Nationality', 'Value']];

$counted = collect($cases["cases"])
    ->groupBy('nationality')
    ->map->count()
    ->map(function ($item, $key) use (&$chartData) {
        $chartData[] = [$key, $item];
    });

return view('welcome', [
      'chartData' => $chartData
]);
```
In this code snippet what I am doing is sending an HTTP request to HPA API and adding the result into a collection grouped by the nationality of the case and counting the total separately for each nationality and mapping the result and sending the data back to view to render.

View Code Snippet
```html
<body><script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script><script type="text/javascript">
        google.charts.load('current', {'packages': ['corechart']});
        google.charts.setOnLoadCallback(drawChart);
        function drawChart() {
            var data = google.visualization.arrayToDataTable(@json($chartData));
            var chart = new google.visualization.PieChart(document.getElementById('chart'));
            chart.draw(data, {
                title: 'Covid19 cases by nationality.',
                  pieSliceText: 'label',
                legend: {position: 'bottom'},
                width: '100%',
                height: '900'
            });
        }
    </script><div style="height:100%;width:100%;" id="chart"></div>
</body>
```

Covid19 Statistics in Maldives

Source: HPA

Controller Code Snippet
```php
use Illuminate\Support\Facades\Http;

$response = Http::get('http://covid19.health.gov.mv/api/fetch_stats')->json();

$statistics = collect($response["local"]["surveillance"]);

return view('welcome', [
    'statistics' => $statistics,
]);
```
View Code Snippet
```html
<html class="h-full"><body class="h-full" style="background-color: #2C3748;"><link href="https://unpkg.com/tailwindcss@^1.0/dist/tailwind.min.css" rel="stylesheet"><div class="h-full w-full overflow-scroll"><div class="flex justify-center"><h1 class="text-3xl font-bold text-gray-400">HPA Statistics</h1></div><div class="flex flex-wrap justify-center">

      @foreach($statistics as $stats)
            <div class="max-w-sm w-1/4 m-4 rounded shadow-lg flex-col flex overflow-hidden  items-center bg-gray-900"><div><div class="text-xl mb-2 mt-4 flex-1  text-gray-600">{{$stats["english_label"]}}                        </div></div><h2 class="text-3xl font-bold text-gray-400">{{$stats["statistic"]}}</h2></div>        
  @endforeach

        </div></div></body>
</html>
```