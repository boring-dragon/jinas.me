---
title: "Building a Chainable Collection"
date: 2020-11-30T03:17:24+05:00
draft: true
---

Collections can be a really useful tool when you are doing any sort of calculations, sortings. I have been using Laravel Collection for a long time in my projects. So let's build a collection with a fluent chainable API to interact it with. This would just a chainable wrapper around PHP array functions. Let's start by building out the class structure.

```php
class Collection
{
  protected $items = array(); 
}
```

Here I have a simple class name called `Collection` and a protected property called `items`. This is the property we will be using to add the items into. You can think of this as a box that we can store things and interact with. The first function I am gonna be implementing will be the `collect` function where you can collect or put items into the collection.

```php
class Collection
{
  protected $items = array(); 

  public function collect($array)
  {
     $this\->items = $array;
     return $this;
   }
  }
```
  

In this method what I am doing is if an array gets passed into the method setting that array into the `items` property and returning the current instance of the collection so we can chain the methods like a fluent API.

**Example Would be:**

```php
$collection = new Collection;
$collection->collect(\[20,20\]);
```

This will return the current instance of the collection and items property will be set to an array given as the arguments to the function. In this case it will be set to `[20,20]`.

Now we need a way to return those items as an array. Because at the moment the visibility of that property is set to protected and we cannot get that directly. To cast that I will be implementing a `toArray()` method which just returns the existing items.

**Now class looks like this**

### **toArray()**

```php
class Collection
{
  protected $items = array(); 

  public function collect($array)
{
     $this\->items = $array;
     return $this;
   }
public function toArray()
  {
  	return $this\->items;
  }
  }
```

**Now we can get the items like so**

```php
$collection = new Collection;
$collection->collect(\[20,20\])->toArray();
\*
OUTPUT:
[
[0] => 20,
[1\ => 20]
*/
```

Notice the `toArray()` method is stacked up at the end of the collect method like a chain. That is possible because when we collect the items we eventually return the current instance of the Collection class.

Now at this point, our collection class is useless and there is not much going on. So let's add some fun methods to play around with.

### Count()

Count the number of items inside the collection.

```php
class Collection
{
  protected $items = array(); 

  public function collect($array)
{
     $this\->items = $array;
     return $this;
   }
public function toArray()
{
  	return $this\->items;
  }

public function count()
  {
  	return count($this\->items);
  }
  }
```
  

Here what I have added is a count method that just counts the number of items inside the array. Let's test it!

```php
$collection = new Collection;
$collection->collect(\[20,20,20,80,40,70,90\])->count();
``
OUTPUT:
```
7
```

You can see being able to chain these methods pretty awesome. Let's add a few more.

### sum()

Sum all values inside the collection.
```php
class Collection
{
  protected $items = array(); 

  public function collect($array)
{
     $this\->items = $array;
     return $this;
   }
public function toArray()
{
  	return $this\->items;
  }

public function count(){
  	return count($this\->items);
  }
public function sum()
  {
  	return array\_sum($this\->items);
  }
  }
```
### pop()

Remove the last item from the collection.

```php
class Collection
{
  protected $items = array(); 

  public function collect($array)
{
     $this\->items = $array;
     return $this;
   }
public function toArray()
{
  	return $this\->items;
  }

public function count(){
  	return count($this\->items);
  }
public function sum(){
  	return array\_sum($this\->items);
  }

  public function pop()
  {
  	array\_pop($this\->items);
    return $this;
  }
  }
```
You can add a few as many chainable methods as you want. I am gonna add few more methods and let's check how we could chain them and do pretty cool stuff.

### Collection Class Snippet

```php
class Collection
{
  protected $items = array();
  
  public function collect($array)
  {
  	$this\->items = $array;
    return $this;
  }
  
  public function reverse()
  {
  	$this\->items = array\_reverse($this\->items);
    return $this;
  }
  
  public function push($array)
  {
  	return array\_push($this\->items,$array);
    return $this;
  }
  
  public function count()
  {
  	return count($this\->items);
  }
  
  public function sum()
  {
  	return array\_sum($this\->items);
  }
  
  public function map(Callable $callback)
  {
  	$this\->items = array\_map($callback,$this\->items);
    return $this;
  }
  
  public function pop()
  {
  	array\_pop($this\->items);
    return $this;
  }
  
  public function toArray()
  {
  	return $this\->items;
  }
}
```

Usage
-----

```php
$collection = new Collection;

$result = $collection->collect(\[20,20\])->map(function($item){
return $item \* 5;
})->toArray();

var\_dump($result);
/\*
OUTPUT:
﻿﻿array(2) {
  \[0\]=>
  int(100)
  \[1\]=>
  int(100)
}
\*/
```

In this Example what I am doing is collecting two values into the collection and mapping through the collection items and for each item inside the collection multiplying them by 5 and returning the array and dumping the result. The result will be 100 for each item because we have two items as a 20 inside the collection. You can also take this map method even further.

### More Mapping!

```php
$results = $collection->collect(\[20,20\])->map(function($item){
return $item \* 5;
})->map(function($item){
	return $item \* 10;
})->toArray();
```
In this example I am taking the functionality even further and doing two mappings of the collection. The first time it maps over the items all the items will be equal to 100 because we are multiplying by 5. And the second time it maps over the items each item will be equal to 1000 because we are multiplying them by 10.

### Lets Sum!

After we map the second time lets sum all the arrays. The output for this will be `2000`.
```php
$results = $collection->collect(\[20,20\])->map(function($item){
return $item \* 5;
})->map(function($item){
	return $item \* 10;
})->sum();

\*
OUTPUT:
﻿﻿int(2000)
*/

```

There you have it!. Its really easy to implement a collection with PHP and its really powerful when ever you need to filter a result sets. Having a fluent API like this helps to better understand how the code(result) flows between the methods.