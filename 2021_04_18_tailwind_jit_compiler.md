---
title: 'How to use the Tailwind JIT compiler'
slug: tailwind-jit-compiler
publish_date: 2021-04-18
tags: Tailwind CSS
---

Recently, Tailwind v2.1 was released with the JIT compiler included. The JIT (Just In Time) compiler only generates CSS that you actually use, instead of all sorts of classes that you (almost) never use, like `subpixel-antialiased`, `place-self-start`, and `backdrop-brightness-95`. And even better compiling your CSS goes extremely fast, about 100ms.

If you don't know what Tailwind CSS is or how to use it, [read my post](https://www.jeroenvanrensen.nl/blog/tall-stack) about it.

## The Problem

The main problem with Tailwind CSS was the large development file size. In that file lots of classes are included, most of which you will never use. Because of this, not all spacing variants (like `mt-35`) are included. Moreover, if you want to use special variants like `group-focus` and `disabled` are not included by default.

When going into production, you had to run `npm run prod`, to purge all unused CSS classes. That means your deployment process takes more time, so users have to wait longer before they can use your website.

## The solution

The team behind Tailwind CSS has created the JIT compiler to solve this problem. Once you make a change in one of your files, your CSS gets recompiled with only the classes you actually use.

Compiling CSS has become lots faster. Whereas it used to take a few seconds, now it only takes 100ms, according to [the official announcement](https://blog.tailwindcss.com/just-in-time-the-next-generation-of-tailwind-css) even 3ms.

## How to use it

If you want to use the JIT compiler, follow these steps:

First, install Tailwind v2.1: 

```
npm install -D tailwindcss@latest postcss@latest autoprefixer@latest
```

Next, add this to your `tailwind.config.js` file:

```js
// tailwind.config.js

module.exports = {
    mode: 'jit',
    purge: [
	    './resources/**/*.blade.php'
    ],
    //
}
```

Finally, run `npm run watch`, and keep it running.

## Benefits

Using the JIT compiler has more benefits:

- **Compiling CSS is fast**: whereas it used to take a few seconds to compile your CSS, now it gets done within a few milliseconds.
- **All variants are enabled**: you can use variants like `focus-group`, `active` and `disabled` without configuring anything to your configuration file.
- **Browsers perform better**: when you have a very large CSS file, browsers become slow. When using the JIT compiler, only used CSS will be generated, so inspecting HTML/CSS is quicker.
- **You don't have to worry about purging**: sometimes, when you are purging for production, some classes don't get purged, and your design breaks. When using the JIT compiler, purging is done when developing, so you have the same file.
