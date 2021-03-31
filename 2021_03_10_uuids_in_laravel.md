---
title: How to use UUIDs in URLs in Laravel
slug: uuids-in-laravel
publish_date: 2021-03-10
tags: laravel, security, uuid
---

In my [previous post](https://www.jeroenvanrensen.nl/blog/dont-expose-incrementing-ids), I told why you shouldn't expose your default incrementing IDs, because of security and other issues. Luckily there are some solutions for this problem: slugs and UUIDs.

If you want to read more about slugs, you can [read more](https://www.jeroenvanrensen.nl/blog/dont-expose-incrementing-ids) about it here.

In this post, I'm going to show you how you can configure UUIDs with Laravel. Then you'll get URLs like these:

```url
http://www.example.org/orders/065e9fb3-6bec-494c-9917-4ab8e71750d4
http://www.example.org/users/d3835dda-e08f-4ed5-baff-756d62a749b2
http://www.example.org/images/avatar-d54a923b-c5d4-4294-8033-a221a57ef361.jpg
```

## Migrations

First, we'll have to add a `uuid` column in the migration. Laravel has a [UUID column](https://laravel.com/docs/8.x/migrations#column-method-uuid) available that we can use.

```php
// database/migrations/create_posts_table.php

Schema::create('posts',  function  (Blueprint  $table)  {
    $table->id();
    $table->uuid('uuid')->unique(); // <-- Add the UUID column in your migration
    $table->string('title');
    $table->text('body');
    $table->timestamps();
});
```

## Models

Next, every time a model (for example a post) is created, we want to automatically assign a UUID to it. We can do that in the `boot` model of our model.

```php
// app/Models/Post.php

use Illuminate\Support\Str;

protected  static  function  boot()
{
    parent::boot();
    
    static::creating(function  ($model)  {
        $model->uuid = (string) Str::uuid();
    });
}
```

## Factories

If you are using model factories to quickly create test models, you don't have to add anything to them because the `boot` method will run when using factories too.

## Routing

The last step is to update the routes file. Every time you use [Route Model Binding](https://laravel.com/docs/8.x/routing#route-model-binding) in your `routes/web.php` file add `:uuid` at the end:

```php
// routes/web.php

Route::get('/posts/{post:uuid}', [PostController::class, 'show'])
    ->name('posts.show');
```

Now Laravel will automatically use the UUID column for routing.

### URLs

If you use URLs, you'll have to update your routes everywhere. For example:

```php
// resources/views/posts/index.blade.php

<ul>
    @foreach($posts as $post)
        <li>
            <a href="{{ url('/posts/' . $post->uuid) }}">
                {{ $post->title }}
            </a>
        </li>
    @endforeach
</ul>
```

### `route` helper

If you're using the `route` helper method, you don't have to change anything! For example:
```php
// resources/views/posts/index.blade.php

<ul>
    @foreach($posts as $post)
        <li>
            <a href="{{ route('posts.show', $post) }}">
                {{ $post->title }}
            </a>
        </li>
    @endforeach
</ul>
```

## Conclusion

As you can see, using Laravel it's very easy to use UUIDs and hide your incrementing IDs. I hope you liked this post, and if you did, you can subscribe to my newsletter below.
