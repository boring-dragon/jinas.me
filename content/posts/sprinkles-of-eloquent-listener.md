---
title: "Sprinkles of Eloquent Listener"
date: 2021-11-30T03:36:54+05:00
draft: false
---

I have been using Eloquent Events Listeners to handle a lot of tasks in my projects. And I love it!!. It's one of those things that makes your code a little bit shorter and organized. Here are some of the ways I been using eloquent default model events to split the code out of the controller to the domain (Model) layer. If code logic is a bit too large I would recommend you to use a dedicated [Observer](https://laravel.com/docs/8.x/eloquent#observers) Class.

### Setting some default values while creating the model.

`Post` Model `boot` method

```php
static::creating(function(self $post) {
  $post->access\_token = Str::random();
});
```

### Removing the Many to Many relationships while the Model is being deleted.

`Post` Model `boot` method

In this case, I have a post model that has many tags. And as soon as the post model is deleted. The tags attached to that post will be deleted as well. You can also do this in the database layer using a [Cascade on Delete](https://www.mysqltutorial.org/mysql-on-delete-cascade/).

```php
static::deleting(function (self $post) {
 $post->tags()->detach(); 
});
```

### Removing A file attachment when a model is deleted.

`Post` Model `boot` method

In this code snippet. I am listening for a Post Model deleted `event` and automatically removing the featured image associated with the post model from the disk.

static::deleting(function (self $post) {
 Storage::delete($post->featured\_image);
});

### Locking a cart when the cart operations are done.

`Cart` Model `boot` method

In this code snippet. I have a cart model that is being used on an e-commerce site. And the eloquent event listener is responsible for locking the cart when the user check-out so no further changes can be added to the cart.

```php
static::created(function (self $cart) {
  $cart->lockCart(); //Setting the locked attribute to true
});
```

### Comparing changes before saving

In this code snippet. I have an Order Model and I am checking whether the order is locked. If the order is locked the only write action allowed is unlocking the order. Only then the changes can be made.
```php
static::saving(function (self $order) { //Allow unlocking order
  if($order->getOriginal('locked') && $order->locked) {
   throw new OrderIsLockedException('Whoops.. Your order is locked.'):
  }
});
```
### Generating Open Graph Image after Post is Created.

Sometimes you might need to have a way to generate dynamic open graph images for your model. In this case a post. An Eloquent `created` event can be used to dispatch a job that is responsible for creating an open graph image.

Extra
-----

As you can see this is a really powerful feature that is built into eloquent models in laravel. You can also create custom [events](https://laravel.com/docs/8.x/events) in laravel to split code into Event classes to better organize the code. I often use laravel built-in `Registered` and `Login` event to handle any logic that is required to perform as soon as the user logs into the application. The use case I had for this is to Listen for a Login event and setting the user's team id when the user logs into the account. So the [global scope](https://laravel.com/docs/8.x/eloquent#global-scopes) will only query the results matching the logged-in user's team.

Hope you have a great day 😃