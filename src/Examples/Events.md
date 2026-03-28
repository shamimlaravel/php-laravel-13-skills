# Event & Listener Examples

Note: Laravel 13 uses different approaches for events. The attributes below help configure model event dispatching.

## Model with Event Dispatching

### Using DispatchesEvents

```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\DispatchesEvents;
use App\Events\PostCreated;
use App\Events\PostUpdated;
use App\Events\PostDeleted;

#[DispatchesEvents]
class Post extends Model
{
    protected function dispatchEventsFor(string $event): void
    {
        match ($event) {
            'created' => event(new PostCreated($this)),
            'updated' => event(new PostUpdated($this)),
            'deleted' => event(new PostDeleted($this)),
            default => null,
        };
    }
}
```

## Event Class

```php
use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class PostCreated implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct(public Post $post)
    {
    }

    public function broadcastOn(): array
    {
        return [
            new Channel('posts'),
        ];
    }

    public function broadcastAs(): string
    {
        return 'post.created';
    }
}
```

## Event with Broadcast Attributes

### Before (Laravel 12 and below)
```php
class PostCreated extends Event
{
    public $broadcastOn = ['posts'];
    public $broadcastAs = 'post.created';
}
```

### After (Using event properties)
```php
use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class PostCreated implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct(public Post $post)
    {
    }

    public function broadcastOn(): array
    {
        return [new Channel('posts')];
    }

    public function broadcastAs(): string
    {
        return 'post.created';
    }
}
```

## Listener with AfterCommit

### Before
```php
class SendPostNotification
{
    public $afterCommit = true;

    public function handle(PostCreated $event)
    {
        // Send notification
    }
}
```

### After (Attribute on listener method)
```php
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\AfterCommit;

class SendPostNotification implements ShouldQueue
{
    #[AfterCommit]
    public function handle(PostCreated $event)
    {
        // Send notification
    }
}
```

## Event Service Provider Registration

### Traditional (still works in Laravel 13)

```php
// app/Providers/EventServiceProvider.php
protected $listen = [
    PostCreated::class => [
        SendPostNotification::class,
        LogPostCreation::class,
    ],
];
```

### Using Event Discovery (auto-discovery)

```php
// In AppServiceProvider
Event::listen(function (PostCreated $event) {
    // Handle event
});
```

## Complete Example: Model with Events

```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\Table;
use Illuminate\Database\Eloquent\Attributes\Fillable;
use Illuminate\Database\Eloquent\Attributes\DispatchesEvents;
use App\Events\PostCreated;
use App\Events\PostUpdated;
use App\Events\PostDeleted;

#[Table('posts')]
#[Fillable(['title', 'body', 'is_published'])]
#[DispatchesEvents]
class Post extends Model
{
    protected function dispatchEventsFor(string $event): void
    {
        match ($event) {
            'created' => event(new PostCreated($this)),
            'updated' => event(new PostUpdated($this)),
            'deleted' => event(new PostDeleted($this)),
            default => null,
        };
    }
}
```
