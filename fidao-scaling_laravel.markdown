Scaling Laravel
---------------

_Chris Fidao, February 7, 2018, 1:15pm EST_

### Introduction

What is scaling?

- Vertical scaling: like optimization, adding more power
- Scaling out: (horizontal) adding more servers

We're going to talk about optimizing parts of Laravel and the server to
increase the load that your server can handle.  (Vertical)

#### Optimization / Vertical Scaling

Goal: efficiency

- increase requests-per-second
- increase speed of response (reduce response time)
- decrease resource usage

#### Scaling Out

Goal: consistency

- Consistent requests per second
- Consistent speed of response
- Consistent despite increasing traffic

### Diving in to Optimization

- Eager loading (n+1 problem)
- Object Caching (decorator pattern)
- PHP-FPM (process management)
- OpCache
- Server Specialization (mysql optimization)

#### Eager Loading (n+1 problem)

The initial query returns a collection; +1 query per object in collection

```
// 3 topics, 6 series underneath
class Topic extends Model
{
    public function series()
    {
        return $this->hasMany(Series::class);
    }
}
...
public function index()
{
    return view('topic, [ 'topics' => Topic::all() ]);
} // then loop through each topic, get series, get content (+3, +6)
```

Thus in the example, 10 total queries are run if lazy-loaded.  To make this
more efficient:

```
// Eager load the series for all topics in steps:
public function index()
{
    return view('topic, [ 'topics' => Topic::with('series')->get() ]);
}  // now down to 8 total queries
...
public function index()
{
    return view('topic, [
        'topics' => Topic::with([
            'series',
            'series.content'
            ])->get(),
        ]);
}  // now down to 3 total queries
```

#### Object Caching

(Turn 3 queries to 0 queries)

```
// Naive attempt:
$topics = Cache::remember('topics', 60, function()
{
    return Topic::with([
        'series',
        'series.content'
        ])->get(),
});

return view('topic', [ 'topics' => $topics ]);

...

// Decorator pattern - add behavior without modifying code
//    interface -> modifiers -> root object

interface TopicQuery implements Topic {
    public function topics()
    {
        return Topic::with([
            'series',
            'series.content'
            ])->get(),
    }
}

// [Too much code to summarize the example - nesting Russian dolls]
```

#### Optimizing PHP-FPM Process Management

Note that default config for php-fpm is based on a server with few resources.

```
; in /etc/php/{version}/fpm/pool.d/www.conf
;
; Use this to enable more requests per second
pm.max_children = 

; Rule: (RAM - Overhead) / Memory per Request
; Be sure to check CPU usage after changing this setting!

; Number of child processes created on startup
pm.start_servers = 

; Desired minimum number of idle server processes
pm.min_spare_servers =

; Desired maximum number of idle server processes
pm.max_spare_servers =

; Number of requests each child process should exec before respawning
pm.max_requests =
```

Cool admin tool referenced: **htop** (apt-get install htop for Debian-based
distributions)

#### OpCache

PHP-FPM converts everything it reads into bytecode. OpCache
caches that bytecode.

```
; in /etc/php/{version}/fpm/php.ini
opcache.enable=1

; Max MB memory used
opcache.memory_consumption=192

; Reduce mem usage from similar strings
opcache.interned_strings_buffer=16

; Max PHP files to cache
opcache.max_accelerated_files=10000

; Don't auto-invalidate the cache  <-- so OpCache doesn't know when files
opcache.revalidate_freq=0              have changed; we'll re-cache on deploy
opcache.validate_timestamps=0

opcache.save_comments=0
```

To do this you must reload PHP-FPM after _any code change_.  Yes, you know
we know that you're editing files in production.

#### Split Out Services

- Give MySQL its own server (BIG DEAL)
    - tools.percona.com/wizard (generate a MySQL config for your server)
        - Focus on Cache + InnoDB sections
- Give Redis its own server
- Give workers their own server
- You get the idea

### Scaling Out

- Load Balancing
- Proxying
- Queues

### Load Balancing

Spread traffic across multiple servers for consistency; but we have to decide
what to do with ancillary services.

- Decentralizing files (...or centralizing them elsewhere)
    - Sessions
    - User uploads (S3, NFS)
- Decentralizing services 
    - Redis (session or object cache)
    - MySQL
    - Any other services (search, cron, etc.)

Example: **HAProxy**

It has a frontend that accepts incoming connections and can be configured to
rewrite all to HTTPS, and multiple possible backend configurations out of the
box.

The backend sets headers (in the example case) and passes the traffic into the
load-balanced array (**balance roundrobin**).

Laravel then has to be set up to read the incoming proxied connections correctly.

[Detailed configuration example showing issue with SSL + LB connections on port 80]

```
class TrustedProxies extends Middleware
{
    protected $proxies = ['172.31.0.238'];
}
```

Trusted proxies have to be set for client detection, cookies / CSRF tokens,
correct URL and form URI generation (think url(), action()).

#### Queues

These push work outside the HTTP cycle and don't make the users wait.  They
avoid timeouts and similar artificial constraints.

```
QUEUE_DRIVER-database

php artisan queue:table
php artisan migrate
```

- Trick 1: priority
- Trick 2: segmentation

[Moved too quickly here for details to be noted]

www.scalinglaravel.com

www.serversforhackers.com
