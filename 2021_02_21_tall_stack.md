---
title: The TALL stack explained
slug: tall-stack
publish_date: 2021-02-21
tags: tailwind css, alpine js, laravel, livewire
---

The TALL stack is a set of frameworks to build interactive apps using Laravel. It stands for [Tailwind CSS](https://tailwindcss.com/), [Alpine.JS](https://github.com/alpinejs/alpine), [Laravel](https://laravel.com/), and [Livewire](https://laravel-livewire.com/). In this article, I'm going to explain all of them. So let's start!

## Tailwind CSS

Tailwind CSS is a utility-first CSS framework. That means that instead of writing classic CSS, you apply utility classes like `pt-4`, `bg-red-500` and `shadow-lg` to your HTML.

Although your HTML might become a little mess, it is far more enjoyable to use Tailwind CSS compared to Bootstrap or any other CSS framework.

If you want to try out Tailwind CSS, you can read the [installation instructions for Laravel](https://tailwindcss.com/docs/guides/laravel) on their website.

## Alpine.js

Alpine.js is a smal Javascript framework for quickly adding interactivity to your page. You can create a Alpine component just by adding the x-data attribute to an element.

Alpine.js handles click events, model binding, transitions and much more. For example, this is a dropdown component:

```html
<div x-data="{ open: false }">
    <button @click="open = true">Open Dropdown</button>

    <ul
        x-show="open"
        @click.away="open = false"
    >
        Dropdown Body
    </ul>
</div>
```

You can read more about Alpine.js on their [Github](https://github.com/alpinejs/alpine) page.

## Laravel

You probably know Laravel. It is a PHP framework with lots of handy features, like queues, events, Eloquent and much more.

If you're new to Laravel, I recommend watching to [this series at Laracasts](https://laracasts.com/series/laravel-6-from-scratch).

## Livewire

The last piece to the TALL stack puzzle is Livewire. Livewire is a full-stack framework to create dynamic components without writing JavaScript, only PHP and blade!

Every time the user updates something, Livewire's JavaScript makes a request to the server, and then refreshes the page. The user won't notice anything so it will feel like a SPA-app.

If you want to learn more about Livewire, I've written [a tutorial about starting with Livewire](https://www.jeroenvanrensen.nl/blog/laravel-livewire-intro) before.

## Conclusion about the TALL stack

The TALL stack enables you to write dynamic applications using the Laravel framework. Everything you'll want is included in one of the four technologies.
