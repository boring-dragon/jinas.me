---
title: "Nasa Image Search With Vue"
date: 2020-11-30T03:08:37+05:00
draft: false
---

### Story

Recently I started using Vuejs for my projects and immediately fell in love with the framework. Vuejs is easy to learn and from someone who hasn’t worked with javascript. It was a really fun experience to learn VUEJS. So last night I was scrolling through NASA API documentation to check out the contents they have I discovered an end-point that you can pass queries to and the NASA API will fetch the matched contents along with some images. I always wanted to try to make a web app that uses NASA API. So what I built last night is a web app that fetches image of the thing you searched for and display the result in a webpage. Demo app

### Technologies I used
 - Vuejs
 - Tailwind CSS
 - NASA API

### UI

For styling the web app I used a popular CSS framework called tailwind. Tailwind CSS is a highly customizable, low-level CSS framework that gives you all of the building blocks you need to build bespoke designs without any annoying opinionated styles you have to fight to override. It was created by one of my favourite keynote speaker Adam wathan. The Ui components I used are directly taken from the tailwind CSS documentation.

### Vue js Codes
```js
new Vue({
  el: "#app",
  data: {
    results: [],
    query: ""
  },

  methods: {
    getPics: function() {
      fetch(
        `https://images-api.nasa.gov/search?q=${this.query}&media_type=image`
      )
        .then(response => {
          return response.json();
        })
        .then(parsedJson => {
          this.results = parsedJson["collection"]["items"];
        });
    }
  }
});
```
In the javascript code what I am doing is fetching the search query from the NASA API and the search query is dynamically added to the request URL because the text field is bind to the query variable. After the response comes back from the NASA API JSON is then parsed and added to the result array to loop in the HTML.

Connecting Everything Together
The last thing to do is to loop through the result in the HTML to display the result. It was done like so

```vue
<div v-for="result in results" class="mb-5">
<div class="rounded overflow-hidden shadow-lg"><img class="w-full" :src="result.links[0].href" />

<div class="px-6 py-4"><div class="font-bold text-xl mb-2">{{result.data[0].title}}</div>
<p class="text-gray-700 text-base">{{result.data[0].description}}</p>
</div>
<div class="px-6 py-4" v-for="keyword in result.data[0].keywords">
<span class="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2">{{keyword}}</span>
</div>
</div>
</div>
```
There you have it. All the source code is given below:

[Github](https://github.com/jinas123/nasa-image-search)
[Demo app](https://nasa-image-search.netlify.com/)