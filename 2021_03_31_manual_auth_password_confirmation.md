---
title: 'Manual auth in Laravel: password confirmation'
slug: manual-auth-password-confirmation
publish_date: 2021-03-31
tags: Manual auth, Authentication
---

With the arrival of Laravel 8, new ways for authentication have been added to the Laravel ecosystem. [Fortify](https://laravel.com/docs/8.x/fortify), [Jetstream](https://jetstream.laravel.com/2.x/introduction.html) and [Breeze](https://github.com/laravel/breeze). Although these tools can save you a lot of time, often when you want something more complex they cost you more time.

Fortunately, Laravel allows you to add manual auth without the use of any package, just Laravel's core. In this series, we're going to learn how to add manual auth in Laravel.

These topics will be covered:

- [Registering](https://www.jeroenvanrensen.nl/blog/manual-auth-registering)
- [Signing in and signing out](https://www.jeroenvanrensen.nl/blog/manual-auth-signing-in-out)
- **Password confirmation**
- Email verification
- Password reset

**Note**: For the examples in this series, I've chosen to use controllers and blade views. But you can also use other technologies, like [Livewire](https://laravel-livewire.com/) or [Inertia.js](https://inertiajs.com/).

## Getting started

First, we'll create a controller to load a view:

```php
// app/Http/Controllers/Auth/PasswordConfirmationController.php

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;

class PasswordConfirmationController extends Controller
{
    public function show()
    {
        return view('auth.confirm-password');
    }

    public function handle()
    {
        // Handling the response
    }
}
```

## Routing

Next, we'll add routes:

```php
// routes/web.php

use App\Http\Controllers\Auth\PasswordConfirmationController;
use Illuminate\Support\Facades\Route;

Route::get('/confirm-password', [PasswordConfirmationController::class, 'show'])
    ->middleware('auth')
    ->name('password.confirm');

Route::post('/confirm-password', [PasswordConfirmationController::class, 'handle'])
    ->middleware('auth')
    ->name('password.confirm');
```

## Views

After routing, we create a form for the user to fill in their password. For example:

```html
<!-- resources/views/auth/confirm-password.blade.php -->

<h1>Confirm Password</h1>

<form  action="{{ route('password.confirm') }}" method="post">
    @csrf

    <!-- Password -->
    <label for="password">Password</label>
    <input type="password" name="password" id="password"  />

    <!-- Submit button -->
    <button type="submit">Confirm Password</button>
</form>
```

## Controller logic

Finally, we'll add some code to the `handle` method:

First, we check if the password is correct:

```php
// app/Http/Controllers/Auth/PasswordConfirmationController.php

use Illuminate\Support\Facades\Hash;

if (!Hash::check(request()->password, auth()->user()->password)) {
    return back()->withErrors(['password' => 'The provided password does not match our records.']);
}
```

If the password was correct, we will tell Laravel that the password was correct.

```php
// app/Http/Controllers/Auth/PasswordConfirmationController.php

session()->passwordConfirmed();
```

Finally, we will redirect the user as intended after a success.

```php
// app/Http/Controllers/Auth/PasswordConfirmationController.php

return redirect()->intended();
```

## Conclusion

Whereas registering and signing in and out does not use much of Laravel's authentication features, confirming a password does. However, you still have a lot of freedom as to how you want to implement it.

If you at some point couldn't follow the tutorial anymore, this is the finished `Auth\PasswordConfirmationController`:

```php
<?php

// app/Http/Controller/Auth/PasswordConfirmationController.php

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use Illuminate\Support\Facades\Hash;

class PasswordConfirmationController extends Controller
{
    public function show()
    {
        return view('auth.confirm-password');
    }

    public function handle()
    {
        if (!Hash::check(request()->password, auth()->user()->password)) {
            return back()->withErrors(['password' => 'The provided password does not match our records.']);
        }

        session()->passwordConfirmed();

        return redirect()->intended();
    }
}
```
