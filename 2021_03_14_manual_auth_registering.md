---
title: Manual auth in Laravel: registering
slug: manual-auth-registering
publish_date: 2021-03-14
tags: laravel, manual auth, auth
---

With the arrival of Laravel 8, new ways for authentication have been added to the Laravel ecosystem. [Fortify](https://laravel.com/docs/8.x/fortify), [Jetstream](https://jetstream.laravel.com/2.x/introduction.html) and [Breeze](https://github.com/laravel/breeze). Although these tools can save you a lot of time, often when you want something more complex they cost you more time.

Fortunately, Laravel allows you to add manual auth without the use of any package, just Laravel's core. In this series, we're going to learn how to add manual auth in Laravel.

These topics will be covered:

- **Registering**
- [Signing in and signing out](https://www.jeroenvanrensen.nl/blog/manual-auth-signing-in-out)
- Password confirmation
- Email verification
- Password reset

**Note**: For the examples in this series, I've chosen to use controllers and blade views. But you can also use other technologies, like [Livewire](https://laravel-livewire.com/) or [Inertia.js](https://inertiajs.com/).

## Getting started

Registering a new user is by far the easiest of all authentication features in Laravel. You just create a new `User` model.

```php
// app/Http/Controllers/Auth/RegisterController.php

use App\Http\Controllers\Controller;
use App\Models\User;
use  Illuminate\Support\Facades\Hash;

class RegisterController extends Controller
{
    public function show()
    {
        return view('auth.register');
    }

    public function handle()
    {
        $user = User::create([
            'name' => request('name'),
            'email' => request('email'),
            'password' => Hash::make(request('password'))
        ]);
    }
}
```

Be sure to hash the password before storing it in the database.

## Validating

Next, we'll add some validation.

```php
// app/Http/Controllers/Auth/RegisterController.php

request()->validate([
    'name' => ['required', 'string', 'max:255'],
    'email' => ['required', 'email', 'max:255'],
    'password' => ['required', 'string', 'min:8', 'confirmed']
]);
```

We use the [`confirmed`](https://laravel.com/docs/8.x/validation#rule-confirmed) validation rule to ensure that the user has confirmed the password. This rule fails when there is no `password_confirmation` in the request, so be sure to add it to your form.

## Events

When registering, dispatch the `Registered` event so that the user will get an email verification link sent to them.

```php
// app/Http/Controllers/Auth/RegisterController.php

use Illuminate\Auth\Events\Registered;

event(new Registered($user));
```

## Signing in

When using a Laravel package for authentication, the user is signed in after registering. It provides a better user experience because they don't have to login directly after registering.

```php
// app/Http/Controllers/Auth/RegisterController.php

use Illuminate\Support\Facades\Auth;

Auth::login($user);
```

## Redirecting

Of course, after a new user is created, you want to redirect them to a welcome or dashboard page. Add this at the end of the `handle` method:

```php
// app/Http/Controllers/Auth/RegisterController.php

use App\Providers\RouteServiceProvider;

return redirect()->to(RouteServiceProvider::HOME);
```

## Routing

Now that our register feature is done, we'll register the needed routes.

```php
// routes/web.php

use App\Http\Controllers\Auth\RegisterController;
use Illuminate\Support\Facades\Route;

Route::get('/register', [RegisterController::class, 'show'])
    ->name('register');

Route::post('/register', [RegisterController::class, 'handle'])
    ->name('register');
```

## Views

Finally, create an `auth.register` view with a form. For example:

```html
<!-- resources/views/auth/register.blade.php -->

<h1>Register</h1>

<form  action="{{ route('register') }}"  method="post">
    @csrf

    <!-- Name -->
    <label for="name">Name</label>
    <input type="text" name="name" id="name"  />

    <!-- Email-->
    <label for="email">Email</label>
    <input type="email" name="email" id="email"  />

    <!-- Password -->
    <label for="password">Password</label>
    <input type="password" name="password" id="password"  />

    <!-- Confirm password -->
    <label for="password_confirmation">Confirm password</label>
    <input type="password" name="password_confirmation"  id="password_confirmation" />

    <!-- Submit button -->
    <button type="submit">Register</button>
</form>
```

## Conclusion

Registering in Laravel is one of the easiest features. If you at some point didn't follow the tutorial, here's the completed `RegisterController`:

```php
<?php

// app/Http/Controller/Auth/RegisterController.php

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use App\Models\User;
use App\Providers\RouteServiceProvider;
use Illuminate\Auth\Events\Registered;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;

class RegisterController extends Controller
{
    public function show()
    {
        return  view('auth.register');
    }

    public function handle()
    {
        request()->validate([
            'name' => ['required', 'string', 'max:255'],
            'email' => ['required', 'email', 'max:255'],
            'password' => ['required', 'string', 'min:8', 'confirmed']
        ]);

        $user = User::create([
            'name' => request('name'),
            'email' => request('email'),
            'password' => Hash::make(request('password'))
        ]);

        event(new Registered($user));

        Auth::login($user);

        return redirect()->to(RouteServiceProvider::HOME);
    }
}
```
