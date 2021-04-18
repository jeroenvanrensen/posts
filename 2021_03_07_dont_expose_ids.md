---
title: "Why you shouldn't expose your incrementing IDs"
slug: dont-expose-incrementing-ids
publish_date: 2021-03-07
tags: Security, UUID
---

By default, every Laravel model has an incrementing ID. Although this makes things very easy, there are some security reasons why you shouldn't be exposing this ID to your users. In this article, I'm going to explain why.

## Security risk

If you use default incrementing IDs in your URLs, anyone can guess which one is the previous, and which one is the next. For example, you've got orders and you use this URL scheme:

```url
http://www.example.org/orders/9
```

Anyone can know that the previous was `8` and the next will be `10`. Of course, you don't want your users to know what others bought.

Although you should return a 403 error if an unauthorized user tries to visit `http://www.example.org/orders/8`, if you forget to do so you have a big security risk.

## Resource harvesting

If you have images and corresponding records in your DB, you can have image names like those:

```url
http://example.org/images/avatar34.jpg
http://example.org/images/avatar35.jpg
```

And anyone can visit `http://example.org/images/avatar33.jpg` and so on to get all your images. This practice is called resource harvesting and applies to models too but is a bigger problem with static files.

## Others know how big your website is

If someone registers and visits their account page, they may end up with a URL like this one:

```url
http://example.org/users/223
```

Now they know they are number `223`. This may not seem like an issue at first, but your competition can create an account too and see how many users you have.

Of course, if you create a forum thread or something else with the same URL scheme, you have this problem too. For example:

```url
http://example.org/threads/43
http://example.org/books/some-book/reviews/34
```

## Solutions

Fortunately, there are plenty of solutions available for this problem. I've described two.

### Slugs

You may use slugs to have a unique identifier that exposes nothing about your DB size.

```url
A post about a specific subject
a-post-about-a-specific-subject
```

And it is what I do with my blog too. But for some models, a slug doesn't make much sense, such as for users or reviews.

And besides, it's a lot of work to get slugs working, especially if everyone can create a new model. For example:

- What if two slugs are the same?
- What if a title changes?

### UUIDs

A UUID (Universally Unique Identifier) is kind of a normal ID, except no-one knows what the previous or next one is.

This is an example of a UUID:

```string
e4a3e607-24b0-40e0-b56d-882304123506
```

And you'll get URLs like this one:

```url
http://www.example.org/orders/065e9fb3-6bec-494c-9917-4ab8e71750d4
http://www.example.org/users/d3835dda-e08f-4ed5-baff-756d62a749b2
http://www.example.org/images/avatar-d54a923b-c5d4-4294-8033-a221a57ef361.jpg
```

If you would like to learn more about UUIDs, I've written a tutorial about [how to use UUIDs in your URLs with Laravel](https://www.jeroenvanrensen.nl/blog/uuids-in-laravel).
