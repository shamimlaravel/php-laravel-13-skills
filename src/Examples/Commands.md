# Command Examples

## Basic Command

### Before
```php
class SendEmails extends Command
{
    protected $signature = 'mail:send {user}';
    protected $description = 'Send emails to a user';
}
```

### After
```php
use Illuminate\Console\Command;
use Illuminate\Console\Attributes\Signature;
use Illuminate\Console\Attributes\Description;

#[Signature('mail:send {user}')]
#[Description('Send emails to a user')]
class SendEmails extends Command
{
}
```

---

## Command with Options

### Before
```php
class PruneUsers extends Command
{
    protected $signature = 'users:prune {--days=30 : Days of inactivity} {--dry-run : Run without deleting}';
    protected $description = 'Prune inactive user accounts';
}
```

### After
```php
use Illuminate\Console\Command;
use Illuminate\Console\Attributes\Signature;
use Illuminate\Console\Attributes\Description;

#[Signature('users:prune {--days=30 : Days of inactivity} {--dry-run : Run without deleting}')]
#[Description('Prune inactive user accounts')]
class PruneUsers extends Command
{
    public function handle(): int
    {
        $days = $this->option('days');
        $dryRun = $this->option('dry-run');
        // ...
        return Command::SUCCESS;
    }
}
```

---

## Command with Help Text

### Before
```php
class ProcessOrders extends Command
{
    protected $signature = 'orders:process';
    protected $description = 'Process pending orders';
    protected $help = 'This command processes all pending orders and sends confirmation emails. Run during off-peak hours.';
}
```

### After
```php
use Illuminate\Console\Command;
use Illuminate\Console\Attributes\Signature;
use Illuminate\Console\Attributes\Description;
use Illuminate\Console\Attributes\Help;

#[Signature('orders:process')]
#[Description('Process pending orders')]
#[Help('This command processes all pending orders and sends confirmation emails. Run during off-peak hours.')]
class ProcessOrders extends Command
{
}
```

---

## Hidden Command

### Before
```php
class DebugInternals extends Command
{
    protected $signature = 'debug:internal';
    protected $description = 'Internal debug command';
    protected $hidden = true;
}
```

### After
```php
use Illuminate\Console\Command;
use Illuminate\Console\Attributes\Signature;
use Illuminate\Console\Attributes\Description;
use Illuminate\Console\Attributes\Hidden;

#[Signature('debug:internal')]
#[Description('Internal debug command')]
#[Hidden]
class DebugInternals extends Command
{
}
```

---

## Command with Aliases

### Before
```php
class WarmCache extends Command
{
    protected $signature = 'cache:warm';
    protected $description = 'Warm up the cache';
    protected $aliases = ['warm-cache'];
}
```

### After
```php
use Illuminate\Console\Command;
use Illuminate\Console\Attributes\Signature;
use Illuminate\Console\Attributes\Description;
use Illuminate\Console\Attributes\Aliases;

#[Signature('cache:warm')]
#[Description('Warm up the cache')]
#[Aliases(['warm-cache'])]
class WarmCache extends Command
{
}
```

---

## Command with Usage Examples

### Before
```php
class DatabaseSeed extends Command
{
    protected $signature = 'db:seed';
    protected $description = 'Seed the database';
    protected $help = <<<'HELP'
        Usage examples:
          php artisan db:seed
          php artisan db:seed --class=UserSeeder
          php artisan db:seed --force
        HELP;
}
```

### After
```php
use Illuminate\Console\Command;
use Illuminate\Console\Attributes\Signature;
use Illuminate\Console\Attributes\Description;
use Illuminate\Console\Attributes\Help;
use Illuminate\Console\Attributes\Usage;

#[Signature('db:seed')]
#[Description('Seed the database')]
#[Help('Seed the database with sample data')]
#[Usage('php artisan db:seed')]
#[Usage('php artisan db:seed --class=UserSeeder')]
#[Usage('php artisan db:seed --force')]
class DatabaseSeed extends Command
{
}
```

---

## Complete Example

### Before
```php
class SendMarketingEmails extends Command
{
    protected $signature = 'mail:marketing {user* : The user IDs to send to} {--subject= : Email subject} {--template= : Email template}';
    protected $description = 'Send marketing emails to users';
    protected $help = 'This command sends marketing emails. Use user IDs separated by spaces.';
    protected $hidden = false;
    protected $aliases = ['mail:marketing'];
}
```

### After
```php
use Illuminate\Console\Command;
use Illuminate\Console\Attributes\Signature;
use Illuminate\Console\Attributes\Description;
use Illuminate\Console\Attributes\Help;
use Illuminate\Console\Attributes\Hidden;
use Illuminate\Console\Attributes\Aliases;

#[Signature('mail:marketing {user* : The user IDs to send to} {--subject= : Email subject} {--template= : Email template}')]
#[Description('Send marketing emails to users')]
#[Help('This command sends marketing emails. Use user IDs separated by spaces.')]
#[Hidden(false)]
#[Aliases(['mail:marketing'])]
class SendMarketingEmails extends Command
{
    public function handle(): int
    {
        $users = $this->arguments('user');
        $subject = $this->option('subject');
        $template = $this->option('template');
        
        // Business logic here
        
        return Command::SUCCESS;
    }
}
```
