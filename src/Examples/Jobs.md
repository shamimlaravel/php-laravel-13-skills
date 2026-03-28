# Job Examples

## Basic Job

### Before
```php
class SendWelcomeEmail implements ShouldQueue
{
    public $tries = 5;
    public $timeout = 120;
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Timeout;

#[Tries(5)]
#[Timeout(120)]
class SendWelcomeEmail implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

---

## Job with Exponential Backoff

### Before
```php
class ProcessPayment implements ShouldQueue
{
    public $tries = 3;
    public $timeout = 120;
    public $backoff = [10, 30, 60];
}
```

### After (Laravel 13.0)
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Timeout;
use Illuminate\Queue\Attributes\Backoff;

#[Tries(3)]
#[Timeout(120)]
#[Backoff([10, 30, 60])]
class ProcessPayment implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

### After (Laravel 13.2+ - Variadic)
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Timeout;
use Illuminate\Queue\Attributes\Backoff;

#[Tries(3)]
#[Timeout(120)]
#[Backoff(10, 30, 60)]  // Variadic - new in Laravel 13.2
class ProcessPayment implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

---

## Job with Queue and Connection

### Before
```php
class SyncInventory implements ShouldQueue
{
    public $queue = 'high';
    public $connection = 'redis';
    public $tries = 10;
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Queue;
use Illuminate\Queue\Attributes\Connection;
use Illuminate\Queue\Attributes\Tries;

#[Queue('high')]
#[Connection('redis')]
#[Tries(10)]
class SyncInventory implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

---

## Job with Enum Queue and Connection (Laravel 13.2+)

### After (Using Enums)
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Queue;
use Illuminate\Queue\Attributes\Connection;
use Illuminate\Queue\Attributes\Tries;

enum Queues: string
{
    case HIGH = 'high';
    case LOW = 'low';
    case DEFAULT = 'default';
}

enum Connections: string
{
    case REDIS = 'redis';
    case SYNC = 'sync';
}

#[Queue(Queues::HIGH)]           // Pass enum directly (Laravel 13.2+)
#[Connection(Connections::REDIS)] // Pass enum directly (Laravel 13.2+)
#[Tries(10)]
class SyncInventory implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

---

## Unique Job

### Before
```php
class RebuildSearchIndex implements ShouldQueue, ShouldBeUnique
{
    public $uniqueFor = 3600;
    public $tries = 3;
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Contracts\Queue\ShouldBeUnique;
use Illuminate\Queue\Attributes\UniqueFor;
use Illuminate\Queue\Attributes\Tries;

#[UniqueFor(3600)]
#[Tries(3)]
class RebuildSearchIndex implements ShouldQueue, ShouldBeUnique
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

---

## Job with Max Exceptions

### Before
```php
class ImportCsvRows implements ShouldQueue
{
    public $maxExceptions = 3;
    public $tries = 5;
    public $timeout = 300;
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\MaxExceptions;
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Timeout;

#[MaxExceptions(3)]
#[Tries(5)]
#[Timeout(300)]
class ImportCsvRows implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

---

## Job with Fail on Timeout

### Before
```php
class CallExternalApi implements ShouldQueue
{
    public $failOnTimeout = true;
    public $tries = 3;
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\FailOnTimeout;
use Illuminate\Queue\Attributes\Tries;

#[FailOnTimeout]
#[Tries(3)]
class CallExternalApi implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

---

## Job with Middleware

### Before
```php
class GenerateReport implements ShouldQueue
{
    public $tries = 3;
    public $middleware = ['throttle:60,1', 'cacheable'];
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Middleware;

#[Tries(3)]
#[Middleware('throttle:60,1')]
#[Middleware('cacheable')]
class GenerateReport implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```

---

## Delete When Missing Models

### Before
```php
class SendOrderConfirmation implements ShouldQueue
{
    // Required manual handling
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\DeleteWhenMissingModels;

#[DeleteWhenMissingModels]
class SendOrderConfirmation implements ShouldQueue
{
    public function __construct(private Order $order) {}

    public function handle(): void
    {
        // Send confirmation
    }
}
```

---

## Without Relations

### Before
```php
class ProcessOrder implements ShouldQueue
{
    // Required manual handling to strip relations
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\WithoutRelations;

#[WithoutRelations]
class ProcessOrder implements ShouldQueue
{
    public function __construct(private Order $order) {}

    public function handle(): void
    {
        // Order relations will be stripped before serialization
    }
}
```

---

## Complete Example

### Before
```php
class ProcessWebhook implements ShouldQueue
{
    public $tries = 3;
    public $timeout = 60;
    public $backoff = [5, 15, 30];
    public $maxExceptions = 2;
    public $queue = 'webhooks';
    public $connection = 'redis';
}
```

### After
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Timeout;
use Illuminate\Queue\Attributes\Backoff;
use Illuminate\Queue\Attributes\MaxExceptions;
use Illuminate\Queue\Attributes\Queue;
use Illuminate\Queue\Attributes\Connection;

#[Tries(3)]
#[Timeout(60)]
#[Backoff([5, 15, 30])]
#[MaxExceptions(2)]
#[Queue('webhooks')]
#[Connection('redis')]
class ProcessWebhook implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
}
```
