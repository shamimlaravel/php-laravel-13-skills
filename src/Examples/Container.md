# Container / Dependency Injection Examples

## Config Injection

### Before
```php
class PaymentService
{
    private string $stripeSecret;

    public function __construct()
    {
        $this->stripeSecret = config('services.stripe.secret');
    }
}
```

### After
```php
use Illuminate\Container\Attributes\Config;

class PaymentService
{
    public function __construct(
        #[Config('services.stripe.secret')] private string $stripeSecret,
    ) {
    }
}
```

---

## Current User Injection

### Before
```php
class DashboardController extends Controller
{
    public function index()
    {
        $user = auth()->user();
        return view('dashboard', compact('user'));
    }
}
```

### After
```php
use Illuminate\Container\Attributes\CurrentUser;
use Illuminate\Routing\Controllers\Controller;

class DashboardController extends Controller
{
    public function index(#[CurrentUser] $user)
    {
        return view('dashboard', compact('user'));
    }
}
```

---

## Database Connection Injection

### Before
```php
class AnalyticsService
{
    private $db;

    public function __construct()
    {
        $this->db = DB::connection('analytics');
    }
}
```

### After
```php
use Illuminate\Container\Attributes\DB;
use Illuminate\Database\Connection;

class AnalyticsService
{
    public function __construct(
        #[DB('analytics')] private Connection $db,
    ) {
    }
}
```

---

## Cache Injection

### Before
```php
class ReportService
{
    private $cache;

    public function __construct()
    {
        $this->cache = Cache::store('redis');
    }
}
```

### After
```php
use Illuminate\Container\Attributes\Cache;
use Illuminate\Contracts\Cache\Repository;

class ReportService
{
    public function __construct(
        #[Cache('redis')] private Repository $cache,
    ) {
    }
}
```

---

## Log Injection

### Before
```php
class PaymentService
{
    private $logger;

    public function __construct()
    {
        $this->logger = Log::channel('payments');
    }
}
```

### After
```php
use Illuminate\Container\Attributes\Log;
use Psr\Log\LoggerInterface;

class PaymentService
{
    public function __construct(
        #[Log('payments')] private LoggerInterface $logger,
    ) {
    }
}
```

---

## Storage Injection

### Before
```php
class UploadService
{
    private $disk;

    public function __construct()
    {
        $this->disk = Storage::disk('s3');
    }
}
```

### After
```php
use Illuminate\Container\Attributes\Storage;
use Illuminate\Contracts\Filesystem\Filesystem;

class UploadService
{
    public function __construct(
        #[Storage('s3')] private Filesystem $disk,
    ) {
    }
}
```

---

## Auth Guard Injection

### Before
```php
class ApiController extends Controller
{
    public function __construct()
    {
        $guard = Auth::guard('api');
    }
}
```

### After
```php
use Illuminate\Container\Attributes\Auth;
use Illuminate\Contracts\Auth\Guard;
use Illuminate\Routing\Controllers\Controller;

class ApiController extends Controller
{
    public function __construct(
        #[Auth('api')] private Guard $auth,
    ) {
    }
}
```

---

## Route Parameter Injection

### Before
```php
class InvoiceController extends Controller
{
    public function show(Request $request)
    {
        $invoiceId = $request->route('invoice');
    }
}
```

### After
```php
use Illuminate\Container\Attributes\RouteParameter;
use Illuminate\Routing\Controllers\Controller;

class InvoiceController extends Controller
{
    public function show(#[RouteParameter('invoice')] string $invoiceId)
    {
        // ...
    }
}
```

---

## Service Binding with Bind

### Before
```php
// In a service provider
$this->app->bind(InvoiceGenerator::class, function ($app) {
    return new InvoiceGenerator();
});
```

### After
```php
use Illuminate\Container\Attributes\Bind;

#[Bind]
class InvoiceGenerator
{
    public function generate(Order $order): Invoice
    {
        // A new instance is created each time
    }
}
```

---

## Singleton Binding

### Before
```php
// In a service provider
$this->app->singleton(FeatureFlagService::class, function ($app) {
    return new FeatureFlagService();
});
```

### After
```php
use Illuminate\Container\Attributes\Singleton;

#[Singleton]
class FeatureFlagService
{
    private array $flags = [];

    public function isEnabled(string $flag): bool
    {
        return $this->flags[$flag] ?? false;
    }
}
```

---

## Scoped Binding

### Before
```php
// In a service provider
$this->app->scoped(RequestContext::class, function ($app) {
    return new RequestContext();
});
```

### After
```php
use Illuminate\Container\Attributes\Scoped;

#[Scoped]
class RequestContext
{
    public ?string $traceId = null;
}
```

---

## Complete Example

### Before
```php
class PaymentService
{
    private string $stripeKey;
    private LoggerInterface $logger;
    private Connection $database;

    public function __construct()
    {
        $this->stripeKey = config('services.stripe.secret');
        $this->logger = Log::channel('payments');
        $this->database = DB::connection('payments');
    }
}
```

### After
```php
use Illuminate\Container\Attributes\Config;
use Illuminate\Container\Attributes\Log;
use Illuminate\Container\Attributes\DB;
use Illuminate\Database\Connection;
use Psr\Log\LoggerInterface;

class PaymentService
{
    public function __construct(
        #[Config('services.stripe.secret')] private string $stripeKey,
        #[Log('payments')] private LoggerInterface $logger,
        #[DB('payments')] private Connection $database,
    ) {
    }
}
```

---

## Authenticated User Injection

### After
```php
use Illuminate\Container\Attributes\Authenticated;
use Illuminate\Contracts\Auth\Authenticatable;
use Illuminate\Routing\Controllers\Controller;

class ProfileController extends Controller
{
    public function edit(#[Authenticated('web')] Authenticatable $user)
    {
        return view('profile.edit', ['user' => $user]);
    }
}
```

---

## Database Alias Injection

### After
```php
use Illuminate\Container\Attributes\Database;
use Illuminate\Database\Connection;

class LegacyService
{
    public function __construct(
        #[Database('legacy_mysql')] private Connection $db,
    ) {
    }
}
```

---

## Give Specific Implementation

### After
```php
use Illuminate\Container\Attributes\Give;
use App\Contracts\PaymentGateway;

class OrderProcessor
{
    public function __construct(
        #[Give(StripePaymentGateway::class)] private PaymentGateway $gateway,
    ) {
    }
}
```

---

## Tagged Services Injection

### After
```php
use Illuminate\Container\Attributes\Tag;

class NotificationSender
{
    public function __construct(
        #[Tag('notification.channels')] private iterable $channels,
    ) {
    }

    public function send(string $message): void
    {
        foreach ($this->channels as $channel) {
            $channel->send($message);
        }
    }
}
```
