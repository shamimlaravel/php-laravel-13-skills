# Controller Examples

## Basic Controller with Middleware

### Before
```php
class DashboardController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');
    }

    public function index()
    {
        return view('dashboard');
    }
}
```

### After
```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

#[Middleware('auth')]
class DashboardController extends Controller
{
    public function index()
    {
        return view('dashboard');
    }
}
```

---

## Controller with Multiple Middleware

### Before
```php
class AdminController extends Controller
{
    public function __construct()
    {
        $this->middleware(['auth', 'verified']);
        $this->middleware('admin', only: ['destroy']);
    }

    public function index() { /* ... */ }
    public function destroy($id) { /* ... */ }
}
```

### After
```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

#[Middleware('auth')]
#[Middleware('verified')]
#[Middleware('admin', only: ['destroy'])]
class AdminController extends Controller
{
    public function index() { /* ... */ }
    public function destroy($id) { /* ... */ }
}
```

---

## Controller with Middleware on Methods

### Before
```php
class ReportController extends Controller
{
    public function __construct()
    {
        $this->middleware('cache.headers:public;max_age=3600')->only('show');
    }

    public function show(Report $report)
    {
        return view('reports.show');
    }
}
```

### After
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
}
```

---

## Controller with Authorization

### Before
```php
class PostController extends Controller
{
    public function __construct()
    {
        $this->authorizeResource(Post::class, 'post');
    }

    public function index() { /* ... */ }
    public function edit(Post $post) { /* ... */ }
    public function update(Request $request, Post $post) { /* ... */ }
    public function destroy(Post $post) { /* ... */ }
}
```

### After
```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Authorize;
use App\Models\Post;

#[Authorize('viewAny', Post::class)]
class PostController extends Controller
{
    public function index() { /* ... */ }

    #[Authorize('update', Post::class)]
    public function edit(Post $post) { /* ... */ }

    #[Authorize('update', Post::class)]
    public function update(Request $request, Post $post) { /* ... */ }

    #[Authorize('delete', Post::class)]
    public function destroy(Post $post) { /* ... */ }
}
```

---

## Controller with Middleware Except

### Before
```php
class PublicController extends Controller
{
    public function __construct()
    {
        $this->middleware('throttle:60,1')->except(['index', 'show']);
    }

    public function index() { /* ... */ }
    public function show($id) { /* ... */ }
    public function store(Request $request) { /* ... */ }
}
```

### After
```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;

#[Middleware('throttle:60,1', except: ['index', 'show'])]
class PublicController extends Controller
{
    public function index() { /* ... */ }
    public function show($id) { /* ... */ }
    public function store(Request $request) { /* ... */ }
}
```

---

## Complete Example

### Before
```php
class UserController extends Controller
{
    public function __construct()
    {
        $this->middleware(['auth', 'verified']);
        $this->middleware('role:admin', only: ['destroy']);
        $this->middleware('throttle:60,1', except: ['index', 'show']);
    }

    public function index()
    {
        return UserResource::collection(User::all());
    }

    public function show(User $user)
    {
        return new UserResource($user);
    }

    public function update(Request $request, User $user)
    {
        $user->update($request->validated());
        return new UserResource($user);
    }

    public function destroy(User $user)
    {
        $user->delete();
        return response()->noContent();
    }
}
```

### After
```php
use Illuminate\Routing\Controllers\Controller;
use Illuminate\Routing\Attributes\Controllers\Middleware;
use Illuminate\Routing\Attributes\Controllers\Authorize;
use App\Models\User;
use App\Http\Resources\UserResource;

#[Middleware('auth')]
#[Middleware('verified')]
#[Middleware('role:admin', only: ['destroy'])]
#[Middleware('throttle:60,1', except: ['index', 'show'])]
class UserController extends Controller
{
    public function index()
    {
        return UserResource::collection(User::all());
    }

    public function show(User $user)
    {
        return new UserResource($user);
    }

    #[Authorize('update', User::class)]
    public function update(Request $request, User $user)
    {
        $user->update($request->validated());
        return new UserResource($user);
    }

    #[Authorize('delete', User::class)]
    public function destroy(User $user)
    {
        $user->delete();
        return response()->noContent();
    }
}
```
