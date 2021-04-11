---
title: 'How to unit test Eloquent relationships'
slug: unit-testing-eloquent-relationships
publish_date: 2021-04-11
tags: Laravel, Eloquent
---

In my opinion, [Eloquent](https://laravel.com/docs/8.x/eloquent) is one of the most powerful features of Laravel. It is an API for interacting with your database, and it has a very nice and easy-to-remember syntax. For example:

```php
$post->author->name;
```

Will give you the name of the post's author.

This is an example of an Eloquent relationship. Relationships define how your models (tables) are connected. Although most are easy to understand, there are a few more complicated ones. 

If you want to learn more about relationships themself, [click here](https://www.jeroenvanrensen.nl/blog/eloquent-relationships) and [click here](https://www.jeroenvanrensen.nl/blog/eloquent-polymorphic-relationships) for polymorphic relationships.

## One to one (has one)

In this example, we have two models: a `User` and an `Address`.

- A `User` has one `Address`
- An `Address` belongs to a `User`

You can test this relationship from the `User` model like this:

```php
// tests/Unit/Models/UserTest.php

use App\Models\Address;
use App\Models\User;

/** @test */
public function a_user_has_one_address()
{
    $user = User::factory()->create();
    $address = Address::factory()->create(['user_id' => $user->id]);

    $this->assertInstanceOf(Address::class, $user->address);
    $this->assertEquals($address->id, $user->address_id);
}
```

## One to one inverse (belongs to)

In this example, we have two models: a `User` and an `Address`.

- A `User` has one `Address`
- An `Address` belongs to a `User`

You can test this relationship from the `Address` model like this:

```php
// tests/Unit/Models/AddressTest.php

use App\Models\Address;
use App\Models\User;

/** @test */
public function an_address_belongs_to_a_user()
{
    $user = User::factory()->create();
    $address = Address::factory()->create(['user_id' => $user->id]);

    $this->assertEquals($user->id, $address->user_id);
    $this->assertInstanceOf(User::class, $address->user);
    $this->assertEquals($user->id, $address->user->id);
}
```

## One to many (has many)

In this example, we have two models: a `Post` and a `Category`.

- A `Post` belongs to a `Category`
- A `Category` has many `Posts`

You can test this relationship from the `Category` model like this:

```php
// tests/Unit/Models/CategoryTest.php

use App\Models\Category;
use App\Models\Post;
use Illuminate\Database\Eloquent\Collection;

/** @test */
public function a_category_has_many_posts()
{
    $category = Category::factory()->create();
    $post = Post::factory()->create(['category_id' => $category->id]);

    $this->assertInstanceOf(Collection::class, $category->posts);
    $this->assertInstanceOf(Post::class, $category->posts->first);
    $this->assertEquals($post->id, $category->posts->first()->id);
}
```

## Has many through

In this example, we have three models: an Author, a Post, and a Language.

- A `Post` belongs to an `Author`
- An `Author` has many `Post`s
- An `Author` "belongs" to a `Language` (speaks a language)
- A `Language` has many `Author`s

You can test this relationship from the `Language` model like this:

```php
// tests/Unit/Models/LanguageTest.php

use App\Models\Language;
use App\Models\User;
use App\Models\Post;
use Illuminate\Database\Eloquent\Collection;

/** @test */
public function a_language_has_many_posts()
{
    $language = Language::factory()->create();
    $user = User::factory()->create(['language_id' => $language->id]);
    $post = Post::factory()->create(['user_id' => $user->id]);

    $this->assertInstanceOf(Collection::class, $language->posts);
    $this->assertInstanceOf(Post::class, $language->posts->first);
    $this->assertEquals($post->id, $language->posts->first()->id);
}
```

## Many to many (belongs to many)

In this example, we have two models: a `Product` and a `Tag`.

- A `Product` has many `Tag`s
- A `Tag` has many `Product`s

You can test this relationship from the `Product` model like this:

```php
// tests/Unit/Models/ProductTest.php

use App\Models\Product;
use App\Models\Tag;
use Illuminate\Database\Eloquent\Collection;

/** @test */
public function a_product_has_many_tags()
{
    $product = Product::factory()->create();
    $tag = Tag::factory()->create();
    $product->tags->attach($tag);

    $this->assertInstanceOf(Collection::class, $product->tags);
    $this->assertInstanceOf(Tag::class, $product->tags->first);
    $this->assertEquals($tag->id, $product->tags->first()->id);
}
```

## Polymorphic: one to one

In this example, we have three models: a `Post`, a `Video`, and an `Image`.

- A `Post` has one `Image`
- A `Video` has one `Image`
- An `Image` belongs to a `Post` or a `Video`

You can test this relationship from the `Post` (or the `Video`) model like this:

```php
// tests/Unit/Models/PostTest.php

use App\Models\Post;
use App\Models\Image;

/** @test */
public function a_post_has_one_image()
{
    $post = Post::factory()->create();
    $image = Image::factory()->create([
        'imageable_type' => 'App\Models\Post',
        'imageable_id' => $post->id
    ]);

    $this->assertInstanceOf(Image::class, $post->image);
    $this->assertEquals($image->id, $post->image->id);
}
```

And you can test this relationship from the `Image` model like this:

```php
// tests/Unit/Models/ImageTest.php

use App\Models\Image;
use App\Models\Post;
use App\Models\Video;

/** @test */
public function an_image_belongs_to_a_post_or_a_video()
{
    $post = Post::factory()->create();
    $image = Image::factory()->create([
        'imageable_type' => 'App\Models\Post',
        'imageable_id' => $post->id
    ]);

    $this->assertInstanceOf(Post::class, $image->imageable);
    $this->assertEquals($post->id, $image->imageable);

    $video = Video::factory()->create();
    $image = Image::factory()->create([
        'imageable_type' => 'App\Models\Video',
        'imageable_id' => $video->id
    ]);

    $this->assertInstanceOf(Video::class, $image->imageable);
    $this->assertEquals($video->id, $image->imageable);
}
```

## Polymorphic: one to many

In this example, we have three models: a `Post`, a `Video`, and a `Comment`.

- A `Post` has many `Comment`s
- A `Video` has many `Comment`s
- A `Comment` belongs to a `Post` or a `Video`

You can test this relationship from the `Post` (or the `Video`) model like this:

```php
// tests/Unit/Models/PostTest.php

use App\Models\Comment;
use App\Models\Post;
use Illuminate\Database\Eloquent\Collection;

/** @test */
public function a_post_has_many_comments()
{
    $post = Post::factory()->create();
    $comment = Comment::factory()->create([
        'commentable_type' => 'App\Models\Post',
        'commentable_id' => $post->id
    ]);

    $this->assertInstanceOf(Collection::class, $post->comments);
    $this->assertInstanceOf(Comment::class, $post->comments->first());
    $this->assertEquals($comment->id, $post->comments->first()->id);
}
```

And you can test this relationship from the `Comment` model like this:

```php
// tests/Unit/Models/CommentTest.php

use App\Models\Comment;
use App\Models\Post;
use App\Models\Video;
use Illuminate\Database\Eloquent\Collection;

/** @test */
public function a_comment_belongs_to_a_post_or_video()
{
    $post = Post::factory()->create();
    $comment = Comment::factory()->create([
        'commentable_type' => 'App\Models\Post',
        'commentable_id' => $post->id
    ]);

    $this->assertInstanceOf(Post::class, $comment->post);
    $this->assertEquals($post->id, $comment->commentable->id);

    $video = Video::factory()->create();
    $comment = Comment::factory()->create([
        'commentable_type' => 'App\Models\Video',
        'commentable_id' => $video->id
    ]);

    $this->assertInstanceOf(Video::class, $comment->video);
    $this->assertEquals($video->id, $comment->commentable->id);
}
```

## Polymorphic: many to many

In this example, we have three models: a `Post`, a `Video`, and a `Tag`.

- A `Post` has many `Tag`s
- A `Video` has many `Tag`s
- A `Tag` belongs to many `Post`s or `Video`s

You can test this relationship from the `Post` (or the `Video`) model like this:

```php
// tests/Unit/Models/CommentTest.php

use App\Models\Tag;
use App\Models\Post;
use Illuminate\Database\Eloquent\Collection;

/** @test */
public function a_post_has_many_tags()
{
    $post = Post::factory()->create();
    $tag = Tag::factory()->create();
    $post->tags->attach($tag);

    $this->assertInstanceOf(Collection::class, $post->tags);
    $this->assertInstanceOf(Tag::class, $post->tags->first());
    $this->assertEquals($tag->id, $post->tags->first()->id);
}
```

And you can test this relationship from the `Tag` model like this:

```php
// tests/Unit/Models/CommentTest.php

use App\Models\Tag;
use App\Models\Post;
use Illuminate\Database\Eloquent\Collection;

/** @test */
public function a_tag_has_many_posts_and_videos()
{
    $tag = Tag::factory()->create();
    
    $post = Post::factory()->create();
    $post->tags->attach($tag);

    $this->assertInstanceOf(Collection::class, $tag->posts);
    $this->assertInstanceOf(Post::class, $tag->posts->first());
    $this->assertEquals($post->id, $tag->posts->first()->id);

    $video = Video::factory()->create();
    $video->tags->attach($tag);

    $this->assertInstanceOf(Collection::class, $tag->videos);
    $this->assertInstanceOf(Video::class, $tag->videos->first());
    $this->assertEquals($video->id, $tag->videos->first()->id);
}
```

## Conclusion

Unit testing your Eloquent relationships can be very useful. I hope you learned something from this tutorial. Thanks for reading!
