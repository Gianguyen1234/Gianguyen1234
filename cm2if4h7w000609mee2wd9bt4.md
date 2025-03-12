---
title: "Custom Visitor Tracking in Laravel (Localhost)"
datePublished: Mon Oct 21 2024 02:49:29 GMT+0000 (Coordinated Universal Time)
cuid: cm2if4h7w000609mee2wd9bt4
slug: custom-visitor-tracking-in-laravel-localhost
tags: laravel

---

If you're running your Laravel blog on [localhost](http://localhost) and want to track visits without using a service like Google Analytics, you can create a simple tracking system within your Laravel application.

You can track the number of visits to your pages by saving the visitor information (like IP address, page viewed, etc.) to your database. Here's a basic example:

#### Step 1: **Create a Migration for Tracking Visits**

First, create a migration to store the visitor data:

```php
php artisan make:migration create_visits_table
```

Then, define the columns in the migration file (`database/migrations/xxxx_xx_xx_create_visits_table.php`):

```php
public function up()
{
    Schema::create('visits', function (Blueprint $table) {
        $table->id();
        $table->string('ip_address')->nullable();
        $table->string('url');
        $table->timestamps();
    });
}

public function down()
{
    Schema::dropIfExists('visits');
}
```

Run the migration:

```php
php artisan migrate
```

#### Step 2: **Create a Model for Visits**

Next, create a model to interact with the `visits` table:

```php
php artisan make:model Visit
```

In the model file (`app/Models/Visit.php`):

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Visit extends Model
{
    protected $fillable = ['ip_address', 'url'];
}
```

#### Step 3: **Log Visits in Your Controller or Middleware**

You can track visits by logging data to the database every time a user visits a page. A good way to do this is by using a middleware:

1. **Create Middleware to Track Visits:**
    

```php
php artisan make:middleware TrackVisits
```

In the middleware (`app/Http/Middleware/TrackVisits.php`):

```php
namespace App\Http\Middleware;

use Closure;
use App\Models\Visit;
use Illuminate\Support\Facades\Request;

class TrackVisits
{
    public function handle($request, Closure $next)
    {
        // Log the visit
        Visit::create([
            'ip_address' => $request->ip(),
            'url' => $request->url(),
        ]);

        return $next($request);
    }
}
```

2. **Register the Middleware:**
    

In `app/Http/Kernel.php`, add the `TrackVisits` middleware to either the global or route-specific middleware section:

```php
protected $middleware = [
    // Other middleware
    \App\Http\Middleware\TrackVisits::class,
];
```

#### Step 4: **Display the Visitor Data**

You can now query the `visits` table to display the visitor count. For example, to show the total number of visits on your blog, you could add this to your view:

```php
$totalVisits = \App\Models\Visit::count();
echo "Total Visits: " . $totalVisits;
```

Or display visit data in your admin panel by querying the `Visit` model:

```php
$visits = \App\Models\Visit::all();  // To get all visits

// Display visit data in your view
foreach ($visits as $visit) {
    echo "IP: " . $visit->ip_address . ", URL: " . $visit->url . ", Time: " . $visit->created_at . "<br>";
}
```

This approach will help you track visits while running on [`localhost`](http://localhost) without needing third-party services like Google Analytics.

### Optional: Filter Unique Visitors

You can track unique visitors by saving the visitor's IP address, then query the number of unique IPs:

```php
$uniqueVisitors = \App\Models\Visit::distinct('ip_address')->count('ip_address');
echo "Unique Visitors: " . $uniqueVisitors;
```

This setup will give you basic visit tracking on your local Laravel application.