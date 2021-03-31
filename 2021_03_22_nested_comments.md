---
title: Nested comments in Laravel
slug: nested-comments
publish_date: 2021-03-22
tags: laravel
---

Last week I added a nested comments feature to my blog. You can see it at the bottom of [this post](https://www.jeroenvanrensen.nl/blog/uuids-in-laravel), where I received a few comments. In this post, I would like to tell you how I added this feature.

**Note**: This is not a step-by-step tutorial. Rather, I'll explain some things that I think are interesting.

## Migrations

First of all, I added a `parent_id` column in my comments migration.

```php
$table->integer('parent_id')->nullable();
```

If it is null, it is a root comment and else it is nested.

## Leaving comments

Next, I updated my [Livewire](https://www.jeroenvanrensen.nl/blog/laravel-livewire-intro) component to allow for replies, but only on root comments. When someone clicks "reply", a `parent` property at the component will be set to that comment and it shows a little message like "In reply to: Jeroen".

When a user hits "save", a little check is done to see if the comment really belongs to the current post and if it is a root comment, because I only want to allow comments to be nested one level deep.

## Showing comments

When showing comments, all root comments (with an empty `parent_id`) will be listed, and for each comment, all children will be listed as well. These are exactly the same, except the children are a little bit indented at the left and don't have a "reply" button.

## Notifications

When someone replies to a comment, everyone who also replied to that comment will get notified, but participants on other comments won't.

Before this, everyone would get notified. But that's sometimes weird if there are two discussions below one post.

## Conclusion

Thanks for reading! I hope you learned something. At least it was a joy to write.
