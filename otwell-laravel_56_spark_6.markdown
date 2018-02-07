Laralve 5.6 and Spark 6
-----------------------

_Taylor Otwell, February 7, 2018, 12:15pm EST_

### Introduction

This will be a recap of what's been going on with development of Laravel 5.6
and Spark 6.  (Laravel 5.6 was released this morning.)  Lumen 5.6 is coming
shortly on the heels of 5.6.

### Laravel 5.6

This is a smaller release than the last few; it is more of a maintenance release
with a few quality-of-life improvements.  Upgrade time should be 5-15 minutes.

(By the way, if you have an idea for a feature, jump in - contact Taylor; you
can also contribute to Laravel docs easily, it's just Markdown.)

_Color Scheme: 'Inspired Github' in Sublime; Font: OperatorMono_

#### Logging

Previously, for passing a logger in, you might have had to drop in to
**bootstrap/app.php** and add a closure prior to return $app; now in 5.6, there
is a **config/logging.php** file.

There is a default logging 'channel' (similar to caching), but multiple channels
can be configured per-application.  The default channel is 'single' (i.e., a
single log file), at the 'debug' level; there are out-of-the-box options for
'daily', 'slack' (now with easy configuration!), 'syslog', 'errorlog', and
**'stack'** - which allows for sending at once to multiple channels.

```
// routes/web.php:
Route::get('/log', function () {
    Log::info('Something happened!);
});

Route::get('/production', function () {
    Log::info('Debug info'), ['user_id' => 1];
    Log::critical('The system is down!'), ['user_id' => 1];
});

// for a stack configuration:
Route::get('/stack', function () {
    Log::stack(['daily', 'single'])->info('Something happened!);
});
```

There are two customization features for logging:

1. Log taps (existing driver does what I want, but I want to tweak minor options)
2. Creating your own instance from scratch

Both use similar setups in **config/logging.php**, just adding to the 'channels'
array.

#### Single-Task Server Scheduling

If you have multiple servers running the artisan scheduler, you have some jobs
that need to run across individual servers, but some jobs that should only run once
(regardless of where it runs).  For the latter situation:

```
// memcache or redis are required in order to use this feature:

// in Kernel.php:
...
protected function schedule(Schedule $schedule)
{
    $schedule->command('sleep')
            ->onOneServer();  <-- THIS
}
```

#### Dynamic Rate Limiting

```
// routes/web.php - ALREADY THERE BEFORE:
Route::get('/throttle', function () {
    return 'Access Granted!';
})->middleware('throttle:2,1');     <-- can make 2 requests per 1 minute

// NEW:
Route::get('/user-throttle', function () {
    return 'Access Granted!';
})->middleware('throttle:rate_limit,1');    <-- depends on logged-in user rate limit

```

#### Storage::download

So convenient.  To make a route for a file download:

```
// routes/web.php
Route::get('/download', function () {
    return Storage::download('foo.txt');
    return Storage::download('foo.txt', 'custom_filename', [ $headers ]);
});
```

#### Channel Classes

```
Broadcast::channel('App.User.{id}, function ($user, $id) {
    return (int) $user->id === (int) $id;
});

Broadcast::channel('invoice.{invoice}', InvoiceChannel::class);
```

#### API Controller Generation

These existed in 5.5 (**Route::apiResource** - doesn't build 'create' or 'edit'
resources); now there is a convenience function to let you create a controller
for these sorts of resources.

```
php artisan make:controller FlightController --api
```

#### Eloquent Date Casting

```
Route::get('/date', function () {
    return factory(App\Project::class)->create();
});

...
// Project.php:
protected $casts = [
    'last_seen_at' => 'datetime:Y-m-d';
]
```

#### Argon2 Hashing

Argon2 is new in PHP 7.2, and is now supported in Laravel as of 5.6

```
// Needs PHP 7.2

Route::get('/argon', function () {
    // return Hash::driver('argon')->make('secret');

    return Hash::driver('argon')->make('secret', [
        'threads' => 4,
        'memory' => 2048,
        'time' => 2000
    ]);
});
```

#### Collision

This prettifies artisan's Exception / failure output akin to Whoops!

#### Bootstrap 4

The auth scaffolding / pagination have all been updated to use Bootstrap 4.

```
Paginator::useBootstrapThree() if you still need that after upgrades
```

### Spark 6

This is the first major upgrade for Spark since initial release.  Spark
scaffolds out the necessary infrastructure for a SaaS project and thus
saves hours of developer time.

The whole UI has been redone by Steve Schoger and has a cleaner look.

#### Localization

Spark is now totally localizable (used to be hard-coded to English), and the
whole theme can be swapped to read right-to-left.

#### Billing

Per-seat billing has been one of the most-requested features, and it has now
been implemented.  Per-project / customer billing has also been implemented.

[Quick demonstration of use cases, ->addSeat(), ->removeSeat()]

_Spark 6 releases next week!_
