---
title: Eloquent polymorphic relationships explained (with examples)
slug: eloquent-polymorphic-relationships
publish_date: 2021-04-05
tags: Laravel, Eloquent
---

In my opinion, [Eloquent](https://laravel.com/docs/8.x/eloquent) is one of the most powerful features of Laravel. It is an API for interacting with your database, and it has a very nice and easy-to-remember syntax. For example:

```php
$post->author->name;
```

Will give you the name of the post's author.

This is an example of an Eloquent relationship. Relationships define how your models (tables) are connected. Although most are easy to understand, there are a few more complicated ones. 

In this post, I'm going to show how every relationship works.

## One to one

In this example, we have three models: a `Post`, a `Video`, and an `Image`.

- A `Post` has one `Image`
- A `Video` has one `Image`
- An `Image` belongs to a `Post` or `Video`

And we have this table structure:

```
posts
    id - integer
    title - string

videos
    id - integer
    name - string

images
    id - integer
    path - string
    imageable_id - integer
    imageable_type - string
```

We can define these relationships like this:

```php
// app/Models/Post.php

public function image()
{
    return $this->morphOne(Image::class, 'imageable');
}
```

```php
// app/Models/Video.php

public function image()
{
    return $this->morphOne(Image::class, 'imageable');
}
```

```php
// app/Models/Image.php

public function imageable()
{
    return $this->morphTo();
}
```

Now we can access the image like this:

```php
$post->image->path;
$video->image->path;
```

And if we have the `$image`, we can get the object where it belongs to (a `Post` or a `Video`) like this:

```php
$image->imageable;
```

## One to many

In this example, we have three models: a `Post`, a `Video`, and a `Comment`.

- A `Post` has many `Comment`s
- A `Video` has many `Comment`s
- A `Comment` belongs to a `Post` or a `Video`

And we have this table structure:

```
posts
    id - integer
    title - string

videos
    id - integer
    name - string

comments
    id - integer
    body - string
    commentable_id - integer
    commentable_type - string
```

We can define the relationships like this:

```php
// app/Models/Post.php

public function comments()
{
    return $this->morphMany(Comment::class, 'commentable');
}
```

```php
// app/Models/Video.php

public function comments()
{
    return $this->morphMany(Comment::class, 'commentable');
}
```

```php
// app/Models/Comment.php

public function commentable()
{
    return $this->morphTo();
}
```

Now we can access the comments like this:

```php
foreach($post->comments as $comment) {
    //
}

foreach($video->comments as $comment) {
    //
}
```

And if we have a `$comment`, we can get the corresponding model (a `Post` or a `Video`) like this:

```php
$comment->commentable;
```

## Many to many

In this example, we have three models: a `Post`, a `Video`, and a `Tag`.

- A `Post` has many `Tag`s
- A `Video` has many `Tag`s
- A `Tag` belongs to many `Post`s or `Video`s

For example, a `Tag` called "personal" can belong to a ``Post` **and** a `Video`.

We may have this table structure:

```
posts
    id - integer
    title - string

videos
    id - integer
    name - string

tags
    id - integer
    name - string

taggables
    tag_id - integer
    taggable_id - integer
    taggable_type - string
```

We can define the relationships like this:

```php
// app/Models/Post.php

public function tags()
{
    return $this->morphToMany(Tag::class, 'taggable');
}
```

```php
// app/Models/Video.php

public function tags()
{
    return $this->morphToMany(Tag::class, 'taggable');
}
```

```php
// app/Models/Tag.php

public function posts()
{
    return $this->morphedByMany(Post::class, 'taggable');
}

public function videos()
{
    return $this->morphedByMany(Video::class, 'taggable');
}
```

Now we can access the tags like this:

```php
foreach($post->tags as $tag) {
    //
}

foreach($video->tags as $tag) {
    //
}
```

And if we have a `$tag`, we can access the posts and videos like this:

```php
foreach($tag->posts as $post) {
    //
}

foreach($tag->videos as $video) {
    //
}
```
