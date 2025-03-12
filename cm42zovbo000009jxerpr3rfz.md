---
title: "Spam Protection: Implementing Google reCAPTCHA in Laravel Without a Package (With Full MVC Setup)"
datePublished: Fri Nov 29 2024 17:00:18 GMT+0000 (Coordinated Universal Time)
cuid: cm42zovbo000009jxerpr3rfz
slug: spam-protection-implementing-google-recaptcha-in-laravel-without-a-package-with-full-mvc-setup
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1732245556084/c2a9eed5-410f-4414-8eac-e0d63c865dbf.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1732245570459/cf4418db-0985-4176-aca3-7b22e5f684d8.png
tags: laravel, recaptcha, spam-protection

---

This post walks you through implementing Google reCAPTCHA v2 in a Laravel application from scratch. We’ll create a full MVC structure to manage comments, integrate Google reCAPTCHA, and protect against spam. The focus is on providing all the necessary code and instructions for a seamless integration.

---

### **Step 1: Set Up the Laravel Application**

If you don’t have a Laravel project yet, create one:

```bash
composer create-project laravel/laravel laravel-recaptcha
```

Navigate into the project directory:

```bash
cd laravel-recaptcha
```

---

### **Step 2: Create the MVC Structure for Comments**

#### 2.1. Generate the Comment Model and Migration

Run the following Artisan command to create a model and migration:

```bash
php artisan make:model Comment -m
```

Update the migration file `database/migrations/xxxx_xx_xx_create_comments_table.php`:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up()
    {
        Schema::create('comments', function (Blueprint $table) {
            $table->id();
            $table->string('author_name');
            $table->text('content');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('comments');
    }
};
```

Run the migration to create the database table:

```bash
php artisan migrate
```

---

#### 2.2. Create the Comment Controller

Run the following command:

```bash
php artisan make:controller CommentController
```

Update `app/Http/Controllers/CommentController.php` with the following code:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;
use App\Models\Comment;

class CommentController extends Controller
{
    public function create()
    {
        return view('comments.create');
    }

    public function store(Request $request)
    {
        // Validate input and reCAPTCHA response
        $request->validate([
            'author_name' => 'required|string|max:255',
            'content' => 'required|string|max:1000',
            'g-recaptcha-response' => 'required|string',
        ]);

        // Verify reCAPTCHA
        $recaptchaSecret = env('RECAPTCHA_SECRET_KEY');
        $response = Http::asForm()->post('https://www.google.com/recaptcha/api/siteverify', [
            'secret' => $recaptchaSecret,
            'response' => $request->input('g-recaptcha-response'),
            'remoteip' => $request->ip(),
        ]);

        if (!$response->json('success')) {
            return redirect()->back()->withErrors(['g-recaptcha-response' => 'Captcha verification failed.'])->withInput();
        }

        // Store comment
        Comment::create([
            'author_name' => $request->author_name,
            'content' => $request->content,
        ]);

        return redirect()->route('comment.create')->with('success', 'Comment submitted successfully!');
    }
}
```

---

#### 2.3. Define Routes

Add routes for the comment form and submission in `routes/web.php`:

```php
use App\Http\Controllers\CommentController;

Route::get('/comments/create', [CommentController::class, 'create'])->name('comment.create');
Route::post('/comments', [CommentController::class, 'store'])->name('comment.store');
```

---

#### 2.4. Update the Comment Model

Modify `app/Models/Comment.php` to include the necessary fields:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
    use HasFactory;

    protected $fillable = ['author_name', 'content'];
}
```

---

### **Step 3: Add the reCAPTCHA Widget to the Form**

Create a Blade view file for the comment form in `resources/views/comments/create.blade.php`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Comment Form</title>
    <script src="https://www.google.com/recaptcha/api.js" async defer></script>
</head>
<body>
    <h1>Submit a Comment</h1>

    @if (session('success'))
        <p style="color: green;">{{ session('success') }}</p>
    @endif

    <form method="POST" action="{{ route('comment.store') }}">
        @csrf

        <label for="author_name">Name:</label>
        <input type="text" id="author_name" name="author_name" required>
        @error('author_name')
            <p style="color: red;">{{ $message }}</p>
        @enderror

        <label for="content">Comment:</label>
        <textarea id="content" name="content" rows="5" required></textarea>
        @error('content')
            <p style="color: red;">{{ $message }}</p>
        @enderror

        <!-- Google reCAPTCHA widget -->
        <div class="g-recaptcha" data-sitekey="{{ env('RECAPTCHA_SITE_KEY') }}"></div>
        @error('g-recaptcha-response')
            <p style="color: red;">{{ $message }}</p>
        @enderror

        <button type="submit">Submit</button>
    </form>
</body>
</html>
```

---

### **Step 4: Configure reCAPTCHA Keys**

1. * Go to the Google reCAPTCHA Admin Console.
        
        2. Register your application by providing:
            
        
        * **Label**: A name for your site. ( what ever you want, you can name it : example)
            
        * **reCAPTCHA** **Type**: Choose either "reCAPTCHA v2" or "reCAPTCHA v3". For this tutorial, we’ll use **reCAPTCHA v2 (Checkbox)**.
            
        * **Domains**: Specify the domains where you’ll use reCAPTCHA. ( Localhost or 127.0.0.1 )
            
    * Once registered, Google will provide you with:
        
        * A **Site Key**: For your frontend.
            
        * A **Secre****t Key**: For server-side verification.
            
    
    Add the following to your `.``env` file:
    

```php
RECAPTCHA_SITE_KEY=your_site_key_here
RECAPTCHA_SECRET_KEY=your_secret_key_here
```

Replace `your_site_key_here` and `your_secret_key_here` with the keys from Google.

---

### **Step 5: Test the Application**

1. Run the development server:
    

```bash
php artisan serve
```

2. Visit the comment form at `http://127.0.0.1:8000/comments/create`.
    
3. Submit the form:
    
    * Without completing the reCAPTCHA: You should see an error.
        
    * After completing the reCAPTCHA: The comment should be saved in the database.
        

---

This completes the process. 🚀

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732245398025/b47bf393-9741-46e1-95af-d46ab57201fe.png align="center")