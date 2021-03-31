---
title: 'Manual auth in Laravel: signing in and out'
slug: manual-auth-signing-in-out
publish_date: 2021-03-18
tags: Laravel, Manual auth, Authentication
---

With the arrival of Laravel 8, new ways for authentication have been added to the Laravel ecosystem. [Fortify](https://laravel.com/docs/8.x/fortify), [Jetstream](https://jetstream.laravel.com/2.x/introduction.html) and [Breeze](https://github.com/laravel/breeze). Although these tools can save you a lot of time, often when you want something more complex they cost you more time.

Fortunately, Laravel allows you to add manual auth without the use of any package, just Laravel's core. In this series, we're going to learn how to add manual auth in Laravel.

These topics will be covered:

- [Registering](https://www.jeroenvanrensen.nl/blog/manual-auth-registering)
- **Signing in and signing out**
- Password confirmation
- Email verification
- Password reset

**Note**: For the examples in this series, I've chosen to use controllers and blade views. But you can also use other technologies, like [Livewire](https://laravel-livewire.com/) or [Inertia.js](https://inertiajs.com/).

## Signing in

For this tutorial, we'll create an `Auth\LoginController` for the login functionality.

```php
// app/Http/Controllers/Auth/LoginController.php

use App\Http\Controllers\Controller;

class LoginController extends Controller
{
    public function show()
    {
        return view('auth.login');
    }

    public function handle()
    {
        // Signing in...
    }
}
```

Now we'll use the `Auth` facade to try to sign in the user:

```php
// app/Http/Controllers/Auth/LoginController.php

$success = auth()->attempt([
    'email' => request('email'),
    'password' => request('password')
], request()->has('remember'));
```

**Note**: after calling `auth()->attempt()` the user is automatically signed in.

In this case, I assume you have a "remember me" checkbox. If that is checked, it will be in the request, and if not it won't. You can also hardcode `true` or `false` if you don't have such a checkbox.

Next, you probably want to redirect the users after a successful login and show errors if it failed.

```php
// app/Http/Controllers/Auth/LoginController.php

use App\Providers\RouteServiceProvider;

if($success) {
    return redirect()->to(RouteServiceProvider::HOME);
}

return back()->withErrors([
    'email' => 'The provided credentials do not match our records.',
]);
```

### Views

Next, create an `auth.login` view and add a form, for example:

```html
<!-- resources/views/auth/login.blade.php -->

<h1>Login</h1>

<form  action="{{ route('login') }}"  method="post">
    @csrf

    <!-- Email-->
    <label for="email">Email</label>
    <input type="email" name="email" id="email"  />

    <!-- Password -->
    <label for="password">Password</label>
    <input type="password" name="password" id="password"  />

    <!-- Submit button -->
    <button type="submit">Login</button>
</form>
```

### Routing

The last step is to add login routes:

```php
// routes/web.php

use App\Http\Controllers\Auth\LoginController;
use Illuminate\Support\Facades\Route;

Route::get('/login', [LoginController::class, 'show'])
    ->name('login');

Route::post('/login', [LoginController::class, 'handle'])
    ->name('login');
```

And now you're done! Users can now login!

### Finished controller

If something went too quickly, here is the full finished `LoginController`:

```php
<?php

// app/Http/Controllers/Auth/LoginController.php

namespace App\Http\Controllers\Auth\LoginController;

use App\Http\Controllers\Controller;
use App\Providers\RouteServiceProvider;

class LoginController extends Controller
{
    public function show()
    {
        return view('auth.login');
    }

    public function handle()
    {
        $success = auth()->attempt([
            'email' => request('email'),
            'password' => request('password')
        ], request()->has('remember'));
        
        if($success) {
            return redirect()->to(RouteServiceProvider::HOME);
        }

        return back()->withErrors([
            'email' => 'The provided credentials do not match our records.',
        ]);
    }
}
```

## Signing out

Signing out is really simple:

```
auth()->logout();
```

If you want it in a controller, you can use one similar to this one:

```php
<?php

// app/Http/Controllers/Auth/LogoutController.php

namespace App\Http\Controllers\Auth\LogoutController;

use App\Http\Controllers\Controller;

class LogoutController extends Controller
{
    public function handle()
    {
        auth()->logout();

        return redirect()->route('login');
    }
}
```

### Routing

Next, add the route:

```php
// routes/web.php

use App\Http\Controllers\Auth\LogoutController;
use Illuminate\Support\Facades\Route;

Route::post('/logout', [LogoutController::class, 'handle'])
    ->name('logout');
```

### Views

And finally, add this in your view:

```html
<!-- resources/views/layouts/app.blade.php -->

<form action="{{ route('logout') }}" method="post">
    @csrf

    <button type="submit">Logout</button>
</form>
```

## Conclusion

Signing in and out in Laravel is really simple. The next time we are going to take a look at password confirmation. Please subscribe to my newsletter to get updated when a new post is published.
