# Middleware Examples

Note: While Laravel 13 doesn't have PHP attributes specifically for defining middleware classes, you can use controller-level attributes to apply middleware. This document shows how to use `#[Middleware]` on controllers.

## Controller with Middleware Attributes

### Multiple Middleware

```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

#[Middleware('auth')]
#[Middleware('verified')]
class DashboardController extends Controller
{
    public function index()
    {
        return view('dashboard');
    }
}
```

### Middleware with Options

```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

#[Middleware('auth')]
#[Middleware('throttle:60,1', only: ['store', 'update'])]
#[Middleware('cache.headers:public;max_age=3600', except: ['destroy'])]
class PostController extends Controller
{
    public function index() { /* ... */ }
    public function show() { /* ... */ }
    public function store() { /* ... */ }
    public function update() { /* ... */ }
    public function destroy() { /* ... */ }
}
```

### Middleware on Specific Methods

```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

class ReportController extends Controller
{
    #[Middleware('cache.headers:public;max_age=3600')]
    public function show(Report $report)
    {
        return view('reports.show');
    }

    #[Middleware('auth:admin')]
    public function generate()
    {
        return view('reports.generate');
    }
}
```

## Creating Custom Middleware

### Basic Middleware

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class CheckAge
{
    public function handle(Request $request, Closure $next): Response
    {
        if ($request->age < 18) {
            return redirect('/home');
        }

        return $next($request);
    }
}
```

### Middleware with Parameters

```php
class CheckRole
{
    public function handle(Request $request, Closure $next, string $role): Response
    {
        if (!$request->user() || !$request->user()->hasRole($role)) {
            abort(403);
        }

        return $next($request);
    }
}
```

## Using Middleware with Attributes

### Register Middleware in Kernel

```php
// app/Http/Kernel.php
protected $middlewareAliases = [
    'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    'role' => \App\Http\Middleware\CheckRole::class,
    'age' => \App\Http\Middleware\CheckAge::class,
];
```

### Apply Using Attributes

```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

#[Middleware('auth')]
#[Middleware('role:admin')]
class AdminController extends Controller
{
    public function index()
    {
        return view('admin.dashboard');
    }
}
```

## Middleware Groups

```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

#[Middleware('web')]
#[Middleware('auth')]
class UserController extends Controller
{
    // Available to authenticated users
}
```

## Complete Example

```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

#[Middleware('auth')]
#[Middleware('verified')]
#[Middleware('throttle:60,1', only: ['store', 'update', 'destroy'])]
#[Middleware('role:admin', only: ['destroy', 'restore', 'forceDelete'])]
class ArticleController extends Controller
{
    public function index()
    {
        return ArticleResource::collection(Article::paginate());
    }

    public function store(Request $request)
    {
        // Rate limited
    }

    public function update(Request $request, Article $article)
    {
        // Rate limited
    }

    #[Middleware('role:editor')]
    public function destroy(Article $article)
    {
        // Admin only
    }
}
```
