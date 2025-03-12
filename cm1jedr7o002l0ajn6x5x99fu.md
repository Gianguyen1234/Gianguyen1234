---
title: "Guide: Fetching Data from Database with ViewServiceProvider in Laravel (Example: Display Categories in Menu)"
datePublished: Thu Sep 26 2024 14:36:46 GMT+0000 (Coordinated Universal Time)
cuid: cm1jedr7o002l0ajn6x5x99fu
slug: guide-fetching-data-from-database-with-viewserviceprovider-in-laravel-example-display-categories-in-menu
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1727359753420/089dec45-e7d0-4eca-a048-a1086f827065.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1727361316788/a4703e11-c2da-436c-a15a-86ed08eb9708.png
tags: laravel, programming, tips

---

In this guide, we’ll walk through how to fetch data from a database in Laravel and share it with a view using the `ViewServiceProvider`. Specifically, we’ll demonstrate how to fetch categories from a database and display them in a website’s navigation menu.

### What is `ViewServiceProvider` in Laravel?

Laravel’s `ViewServiceProvider` is a service provider that allows you to share data with multiple views. It provides a convenient way to inject data into all views or specific ones, without the need to pass it from each controller manually.

### Why use `ViewServiceProvider` instead of passing data through Controllers?

* **Global availability**: If you want to make certain data available to multiple views (e.g., categories for a menu), it’s inefficient to load it in every controller. Using `ViewServiceProvider`, you can share it globally or to specific views.
    
* **Code clarity**: It keeps controllers focused on their primary responsibilities and reduces code duplication.
    

Suppose you already have your database populated with categories, so the focus will be on fetching these categories and making them available globally (or in specific views) through the `ViewServiceProvider`.

### <mark>Steps to Fetch and Display Categories:</mark>

### Step 1: Define the `ViewServiceProvider`

You may already have the `ViewServiceProvider`, but if not, you can create one.

1. **Create** `ViewServiceProvider`:
    
    Run the following command to create ViewServiceProvider:
    
    `php artisan make:provider ViewServiceProvider`
    
2. **Register the Provider:**
    
    In `config/app.php`, add the `ViewServiceProvider` to the `providers` array:
    
    ```php
     'providers' => ServiceProvider::defaultProviders()->merge([
            /*
             * Package Service Providers...
             */
    
            /*
             * Application Service Providers...
             */
            App\Providers\AppServiceProvider::class,
            App\Providers\AuthServiceProvider::class,
            // App\Providers\BroadcastServiceProvider::class,
            App\Providers\EventServiceProvider::class,
            App\Providers\RouteServiceProvider::class,
            App\Providers\ViewServiceProvider::class,
        ])->toArray(),
    ```
    
3. **Fetch Categories in** `ViewServiceProvider`**:**
    
    Open `app/Providers/ViewServiceProvider.php` and modify the `boot()` method to fetch categories from the `Category` model and share them with the views.
    
    ```php
    namespace App\Providers;
    
    use Illuminate\Support\Facades\View;
    use Illuminate\Support\ServiceProvider;
    use App\Models\Category;
    
    class ViewServiceProvider extends ServiceProvider
    {
        public function boot()
        {
            // Fetch categories from the database
             View::composer('layout', function ($view) {
                $categories = Category::all(); 
                $view->with('categories', $categories); 
            });
        }
    
        public function register()
        {
            //
        }
    }
    ```
    
    In this code:
    
    * `View::composer('layout', ...)` ensures the `categories` variable is available globally in all views.
        
    * `Category::all()` fetches all the category records from the database.
        
    
    If you only want to share the categories with specific views, replace `'*'` with the name of the view (e.g., `'`[`layout`](http://layouts.app)`.blade.php'`).
    

### Step 2: Modify Your Blade View (Menu)

Next, go to the Blade template where you want to display the categories in the menu. For example, if you have a layout file at `resources/views/layout.blade.php`, you can include the following Blade syntax to display the categories in the navigation:

```php
<li class="nav-item dropdown">
    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-expanded="false">
        Categories
    </a>
    <div class="dropdown-menu" aria-labelledby="navbarDropdown">
        @foreach ($categories as $category)
            <a class="dropdown-item" href="{{ url('/category', $category->id) }}">{{ $category->name }}</a>
        @endforeach
    </div>
</li>
```

Now you can run the app with command: `php artisan serve`

Make sure you have a route that handles displaying posts or items for a specific category. For example:

```php
phpCopy codeRoute::get('/category/{id}', [CategoryController::class, 'show'])->name('category.show');
```

In your `CategoryController`, you can handle the logic for showing the posts/items under each category.

This approach ensures that the `categories` are available in your layout view globally without having to pass them explicitly from individual controllers.