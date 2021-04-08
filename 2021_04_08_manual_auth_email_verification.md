---
title: 'Manual auth in Laravel: email verification'
slug: manual-auth-email-verification
publish_date: 2021-04-08
tags: Manual auth, Authentication
---

With the arrival of Laravel 8, new ways for authentication have been added to the Laravel ecosystem. [Fortify](https://laravel.com/docs/8.x/fortify), [Jetstream](https://jetstream.laravel.com/2.x/introduction.html) and [Breeze](https://github.com/laravel/breeze). Although these tools can save you a lot of time, often when you want something more complex they cost you more time.

Fortunately, Laravel allows you to add manual auth without the use of any package, just Laravel's core. In this series, we're going to learn how to add manual auth in Laravel.

These topics will be covered:

- [Registering](https://www.jeroenvanrensen.nl/blog/manual-auth-registering)
- [Signing in and signing out](https://www.jeroenvanrensen.nl/blog/manual-auth-signing-in-out)
- [Password confirmation](https://www.jeroenvanrensen.nl/blog/manual-auth-password-confirmation)
- **Email verification**
- Password reset

**Note**: For the examples in this series, I've chosen to use controllers and blade views. But you can also use other technologies, like [Livewire](https://laravel-livewire.com/) or [Inertia.js](https://inertiajs.com/).

## Preparation

Before adding the verification functionality, we first have to prepare the `User` model.

Add `MustVerifyEmail` in your `User` model:

```php
// app/Models/User.php

use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable implements MustVerifyEmail
{
    //
}
```

Next, verify that the `Registered` event is dispatched after registering:

```php
// app/Http/Controllers/Auth/RegisterController.php

use App\Http\Controllers\Controller;
use Illuminate\Auth\Events\Registered;

class RegisterController extends Controller
{
    public function show()
    {
        //
    }

    public function handle()
    {
        //

        event(new Registered($user));
    }
}
```

## Getting started

Now that we've done the preparation, we can get started.

The email verification feature exists of three parts:

1. A page to tell the user that they have to verify their email address
2. A button where a user can click to request another link
3. A route to verify the email address after clicking on a button in an email

## 1. A page to tell the user that they have to verify their email address

First, we'll create a controller called `Auth\EmailVerificationController`:

```php
// app/Http/Controllers/Auth/EmailVerificationController.php

use App\Http\Controllers\Controller;

class EmailVerificationController extends Controller
{
    public function show()
    {
        return view('auth.verify-email');
    }
}
```

Next, we'll create a view to tell the user that they have to verify their email address. For example:

```html
<!-- resources/views/auth/verify-email.blade.php -->

<h1>Verify email</h1>

<p>Please verify your email address by clicking the link in the mail we just sent you. Thanks!</p>
```

Finally, we'll add the necessary route:

```php
// routes/web.php

use Illuminate\Support\Facades\Route;

Route::get('/verify-email', [EmailVerificationController::class, 'show'])
    ->middleware('auth')
    ->name('verification.notice'); // <-- don't change the route name
```

## 2. A button where a user can click to request another link

In case the user can't find the link anymore, or it has expired, the user should be able to request another link.

First, we'll add the logic in the `EmailVerificationController`:

```php
// app/Http/Controllers/Auth/EmailVerificationController.php

use App\Http\Controllers\Controller;

class EmailVerificationController extends Controller
{
    public function request()
    {
        auth()->user()->sendEmailVerificationNotification();

        return back()
            ->with('success', 'Verification link sent!');
    }
}
```

Next, we'll add a form in our view to allow the user to request another link:

```html
<!-- resources/views/auth/verify-email.blade.php -->

<form action="{{ route('verification.request') }}" method="post">
    <button type="submit">Request a new link</button>
</form>
```

And finally, we'll add the necessary route to make this work:

```php
// routes/web.php

use Illuminate\Support\Facades\Route;

Route::post('/verify-email/request', [EmailVerificationController::class, 'request'])
    ->middleware('auth')
    ->name('verification.request');
```

## 3. A route to verify the email address after clicking on a button in an email

The last and most important step is to allow the user to click the link in the email we sent.

As always, we'll first add the controller method:

```php
// app/Http/Controllers/Auth/EmailVerificationController.php

use App\Http\Controllers\Controller;
use Illuminate\Foundation\Auth\EmailVerificationRequest;

class EmailVerificationController extends Controller
{
    public function verify(EmailVerificationRequest $request)
    {
        $request->fulfill();

        return redirect()->to('/home'); // <-- change this to whatever you want
    }
}
```

Afterward, we'll add the routing:

```php
// routes/web.php

use Illuminate\Support\Facades\Route;

Route::post('/verify-email/{id}/{hash}', [EmailVerificationController::class, 'verify'])
    ->middleware(['auth', 'signed']) // <-- don't remove "signed"
    ->name('verification.verify'); // <-- don't change the route name
```

## Protecting routes

For every route that you want to protect from unverified users, add the `verified` middleware. For example:

```php
// routes/web.php

use Illuminate\Support\Facades\Route;

Route::post('/posts', [PostController::class, 'create'])
    ->middleware(['auth', 'verified']) // <!-- add the "verified" middleware
    ->name('posts.create');
```

## Conclusion

This is the end of this tutorial. Thanks for reading!
