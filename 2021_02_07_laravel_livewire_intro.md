---
title: 'Laravel Livewire: an introduction'
slug: laravel-livewire-intro
publish_date: 2021-02-07
tags: laravel, livewire
---

[Laravel Livewire](https://laravel-livewire.com/) is a great framework for creating interactive web apps without writing any JavaScript. In this tutorial we're going to make a few components using Laravel Livewire so we can see the power of this framework.

**Important: Livewire requires you to use [Laravel](https://laravel.com/).**

You can install Livewire using Composer:

```
composer require livewire/livewire
```

Next, you have to include Livewire's assets on your page:

```html
<!-- resources/views/layouts/app.blade.php -->

<livewire:styles /> <!-- Styles -->
<livewire:scripts /> <!-- Scripts -->
```

And that was it! Now you can use Livewire.

## The counter

A typical example of Livewire is the counter. Although it may not be really useful to use Livewire in this situation, it does however really show the power of Livewire.

First, we are gonna make a new Livewire component:

```
php artisan livewire:make counter
```

Now Livewire has made two files: a `Counter.php` class and a `counter.blade.php` view.

Next we'll go to the `Counter.php` class and add this code:

```php
<?php

// app/Http/Livewire/Counter.php

namespace App\Http\Livewire;

use Livewire\Component;

class Counter extends Component
{
    public int $count = 0;

    public function render()
    {
        return view('counter');
    }

    public function increment()
    {
        $this->count++;
    }

    public function decrement()
    {
        $this->count--;
    }
}
```

And now we'll add this in our `counter.blade.php` view:

```html
<!-- resources/views/livewire/counter.blade.php -->

<div>
    <h1>{{ $count }}</h1>
    
    <button wire:click="increment">+</button>
    <button wire:click="decrement">-</button>
</div>
```

Finally, we have to include the component in our blade view:

```html
<!-- resources/views/layouts/app.blade.php -->

<livewire:counter />
```

And that's it! This is all we have to do to create a working counter in Laravel Livewire.

**Note: If you want to create a counter in a real-project, you probably don't want to do it using Livewire, because there are far simpler alternatives using only JS.**

## How does this work?

Now that we have had a look at the awesomeness of Livewire, let's explain how this counter example works.

1. Livewire renders the component at the first-page load, so it's SEO-friendly
2. When a user does something (such as clicking a button), Livewire's JS makes a request to the server
3. The server handles the request and then Livewire rerenders the component.

## Forms with Livewire

A better use-case for Livewire are forms. So let's create a form where a user can create a new post.

We'll start by creating a new component.

```
php artisan livewire:make create-post
```

And for the sake of simplicity, a post has a title and a body. So we have to add those fields to our class.

```php
<?php

// app/Http/Livewire/CreatePost.php

namespace App\Http\Livewire;

use App\Models\Post;
use Livewire\Component;

class CreatePost extends Component
{
    public string $title = '';

    public string $body = '';

    public function render()
    {
        return view('create-post');
    }

    public function createPost()
    {
        $post = Post::create([
            'title' => $this->title,
            'body' => $this->body
        ]);

        return redirect()->route('posts.show', $post);
    }
}
```

And in our form we'll use the `wire:model` attribute to connect the `input` and `textarea` to our Livewire property.

```html
<!-- resources/views/livewire/create-post.blade.php -->

<div>
    <h1>New Post</h1>

    <!-- Title -->
    <label for="title">Title</label>
    <input type="text" name="title" id="title" wire:model="title" />

    <!-- Body -->
    <label for="body">Body</label>
    <textarea name="body" id="body" wire:model="body"></textarea>
</div>
```

Now our form works! You can click on the submit button and it'll create a new post!

### Livewire's validation

It's time for some validation. We can do that really simple using Livewire. First we'll add a `$rules` property to our component.

```php
// app/Http/Livewire/CreatePost.php

protected $rules = [
    'title' => ['required', 'string', 'max:255'],
    'body' => ['required', 'string']
];
```

And next, we'll tell Livewire to validate before creating the post.

```php
// app/Http/Livewire/CreatePost.php

public function createPost()
{
    $this->validate(); // <-- Add the validate method before creating the post

    $post = Post::create([
        'title' => $this->title,
        'body' => $this->body
    ]);

    return redirect()->route('posts.show', $post);
}
```

Finally we have to display the validation error messages in the component view.

```html
<!-- resources/views/livewire/create-post.blade.php -->

<div>
    <h1>New Post</h1>

    <!-- Title -->
    <label for="title">Title</label>
    <input type="text" name="title" id="title" wire:model="title" />
    @error('title')<span>{{ $message }}</span>@enderror

    <!-- Body -->
    <label for="body">Body</label>
    <textarea name="body" id="body" wire:model="body"></textarea>
    @error('body')<span>{{ $message }}</span>@enderror

    <!-- Submit button -->
    <button wire:click="createPost">Create Post</button>
</div>
```

### Real-time validation

Now that we have added validation, it is really easy to add real-time validation with Livewire. This is the only method you have to add:

```php
// app/Http/Livewire/CreatePost.php

public function updated($property)
{
    $this->validateOnly($property);
}
```

How does this work? When a field (property) is updated, Livewire automatically calls the `updated` method, from where the `validateOnly` method is called which validates one field instead of all.

So in theory you could also just say `validate`, but then the whole form turns full of error messages before the user has typed anything. And that's not very user-friendly.

## Turbolinks and SPA's

With Livewire you can easily create SPA's. All you have to do is add those two lines:

```html
<!-- resources/views/layouts/app.blade.php -->

<script src="https://cdnjs.cloudflare.com/ajax/libs/turbolinks/5.2.0/turbolinks.js"></script>
<script src="https://cdn.jsdelivr.net/gh/livewire/turbolinks@v0.1.x/dist/livewire-turbolinks.js" data-turbolinks-eval="false" data-turbo-eval="false"></script>
```

And now your application will use [Turbolinks](https://github.com/turbolinks/turbolinks) and the [Livewire Turbolinks adapter](https://github.com/livewire/turbolinks).

## Testing Livewire

Livewire is easy to test using [PHPUnit](https://github.com/sebastianbergmann/phpunit). You can learn more at the [Livewire docs](https://laravel-livewire.com/docs/2.x/testing).

## Conclusion

To conclude, Livewire is a great option for writing interactive apps when you don't want to write JavaScript or when you want it to be SEO-friendly. It is a framework full of features and I've just covered the basics.

These are some great features of Livewire:

- [Pagination](https://laravel-livewire.com/docs/2.x/pagination)
- [File uploads](https://laravel-livewire.com/docs/2.x/file-uploads)
- [Flash messages](https://laravel-livewire.com/docs/2.x/flash-messages)

And much more.

I hope you liked this tutorial. If you did so, please leave a comment.
