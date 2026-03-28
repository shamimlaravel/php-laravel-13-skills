# Notification Examples

Laravel 13 Notifications can be enhanced with attributes. Here's how to use them.

## Basic Notification

```php
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Notification;

class OrderShippedNotification extends Notification implements ShouldQueue
{
    use Queueable;

    public function __construct(public Order $order)
    {
    }

    public function via(object $notifiable): array
    {
        return ['mail', 'database'];
    }

    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
            ->line('Your order has been shipped!')
            ->action('View Order', url('/orders/' . $this->order->id));
    }

    public function toArray(object $notifiable): array
    {
        return [
            'order_id' => $this->order->id,
            'message' => 'Your order has been shipped',
        ];
    }
}
```

## Notification with Queue Attributes

### Before
```php
class SendReminderNotification extends Notification implements ShouldQueue
{
    public $tries = 3;
    public $timeout = 120;
    public $queue = 'notifications';
}
```

### After
```php
use Illuminate\Notifications\Notification;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Timeout;
use Illuminate\Queue\Attributes\Queue;

#[Tries(3)]
#[Timeout(120)]
#[Queue('notifications')]
class SendReminderNotification extends Notification implements ShouldQueue
{
    // Notification methods
}
```

## Sending Notifications

### Using the Notifiable trait

```php
use Illuminate\Notifications\Notifiable;

class User extends Model
{
    use Notifiable;
}

// Send notification
$user->notify(new OrderShippedNotification($order));
```

### Using Notification facade

```php
use Illuminate\Support\Facades\Notification;

Notification::send($users, new OrderShippedNotification($order));
```

## Channel-Specific Queue Configuration

```php
use Illuminate\Notifications\Notification;
use Illuminate\Contracts\Queue\ShouldQueue;

class DailyDigestNotification extends Notification implements ShouldQueue
{
    public function viaQueues(): array
    {
        return [
            'mail' => 'notifications',
            'database' => 'default',
        ];
    }
}
```

## Complete Example with Job Attributes

```php
use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Notification;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\Attributes\Tries;
use Illuminate\Queue\Attributes\Timeout;
use Illuminate\Queue\Attributes\Backoff;
use Illuminate\Queue\Attributes\Queue;
use Illuminate\Queue\Attributes\Connection;

#[Tries(3)]
#[Timeout(120)]
#[Backoff(30)]
#[Queue('notifications')]
#[Connection('redis')]
class WelcomeNotification extends Notification implements ShouldQueue
{
    use Queueable;

    public function __construct(public string $userName)
    {
    }

    public function via(object $notifiable): array
    {
        return ['mail'];
    }

    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
            ->subject('Welcome ' . $this->userName)
            ->line('Welcome to our platform!')
            ->action('Get Started', url('/dashboard'));
    }
}
```
