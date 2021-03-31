---
title: Eloquent relationships explained (with examples)
slug: eloquent-relationships
publish_date: 2021-03-28
tags: Laravel, Eloquent
---

In my opinion, [Eloquent](https://laravel.com/docs/8.x/eloquent) is one of the most powerful features of Laravel. It is an API for interacting with your database, and it has a very nice and easy-to-remember syntax. For example:

```php
$post->author->name;
```

Will give you the name of the post's author.

This is an example of an Eloquent relationship. Relationships define how your models (tables) are connected. Although most are easy to understand, there are a few more complicated ones. 

In this post, I'm going to show how every relationship works.

## One to one (has one)

For this example, we have two models: a `User` and an `Address`. The `User` model holds information such as name, email address, and password, and the `Address` model holds information like country, state, city, etc.

- A `User` has one `Address`
- An `Address` belongs to a `User`

We may have this table structure:

```
users
    id - integer
    name - string
    email - string
    password - string

address
    id - integer
    country - string
    city - string
    user_id - integer
```

You can define these relationships like this:

```php
// app/Models/User.php

public function address()
{
    return $this->hasOne(Address::class);
}
```

Now you can access the user's address using `$user->address->city`.

**Note**: for this to work, the `Address` model should have a `user_id` column.

### Inverse (belongs to)

If you have an `Address` and want to find the corresponding `User`, then define this relationship:

```php
// app/Models/Address.php

public function user()
{
    return $this->belongsTo(User::class);
}
```

## One to many (has many)


In this example, we have two models: a `Post` and a `Category`. 

-  A `Post` belongs to a `Category`
- A `Category` has many `Post`s

And we have this table structure:

```
categories
    id - integer
    name - string

posts
    id - integer
    title - string
    category_id - integer
```

We can define this relationship like this:

```php
// app/Models/Category.php

public function posts()
{
    return $this->hasMany(Post::class);
}
```

And you can access all the posts like this: 

```php
foreach($category->posts as $post) {
    //
}
```

**Note**: for this to work, the `Post` model should have a `category_id` column.

## Many to one (belongs to)

In this example, we have two models: a `Post` and a `Category`. 

-  A `Post` belongs to a `Category`
- A `Category` has many `Post`s

And we have this table structure:

```
categories
    id - integer
    name - string

posts
    id - integer
    title - string
    category_id - integer
```

We can define this relationship like this:

```php
// app/Models/Post.php

public function category()
{
    return $this->belongsTo(Category::class);
}
```

And you can access the `Post`'s category like this:

```php
$post->category->name;
```

## Has many through

This relationship is a bit more difficult. In this example, we have three models: an `Author`, a `Post`, and a `Language`.

- A `Post` belongs to an `Author`
- An `Author` has many `Post`s
- An `Author` "belongs" to a `Language` (speaks a language)
- A `Language` has many `Author`s

For example, this is our table structure:

```
languages
    id - integer
    name - string

authors
    id - integer
    name - string
    language_id - integer

posts
    id - integer
    title - string
    author_id - integer
```

If we want to get all posts in a specific language, we can define this relationship:

```php
// app/Models/Language.php

public function posts()
{
    return $this->hasManyThrough(Post::class, User::class);
}
```

Now, we can get all posts using:

```php
foreach($language->posts as $post) {
    //
}
```

### Inverse

If you now want to get the `Language` of a `Post`, you can just simply do this:

```php
$post->user->language->name;
```

## Many to many (belongs to many)

In this example, we have two models: a `Product` and a `Tag`.

- A `Product` has many `Tag`s
- A `Tag` has many `Product`s

And we may have this table structure:

```
products
    id - integer
    name - string
    price - integer

tags
    id - integer
    name - string

product_tag
    product_id - integer
    tag_id - integer
```

**Note**: in the table structure, we have a third table, `product_tag`. This table connects products to tags.

Now we can define the relationships like this:

```php
// app/Models/Product.php

public function tags()
{
    return $this->belongsToMany(Tag::class);
}
```

```php
// app/Models/Tag.php

public function products()
{
    return $this->belongsToMany(Product::class);
}
```

Now we can get all tags/products using:

```php
foreach($product->tags as $tag) {
    //
}
```

```php
foreach($tag->products as $product) {
    //
}
```

In the next post, I'm going to show what polymorphic relationships are and how to use them. Thanks for reading!
