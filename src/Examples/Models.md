# Model Examples

## Basic Model

### Before
```php
class Post extends Model
{
    protected $table = 'posts';
    protected $fillable = ['title', 'body'];
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\Table;
use Illuminate\Database\Eloquent\Attributes\Fillable;

#[Table('posts')]
#[Fillable(['title', 'body'])]
class Post extends Model
{
}
```

---

## Model with Multiple Casts

### Before
```php
class Product extends Model
{
    protected $casts = [
        'price' => 'decimal:2',
        'is_active' => 'boolean',
        'published_at' => 'datetime',
    ];
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\Cast;

#[Cast('price', 'decimal:2')]
#[Cast('is_active', 'boolean')]
#[Cast('published_at', 'datetime')]
class Product extends Model
{
}
```

---

## Model with Full Table Configuration

### Before
```php
class Order extends Model
{
    protected $table = 'orders';
    protected $primaryKey = 'order_id';
    protected $keyType = 'string';
    public $incrementing = false;
    public $timestamps = false;
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\Table;

#[Table(
    name: 'orders',
    key: 'order_id',
    keyType: 'string',
    incrementing: false,
    timestamps: false,
)]
class Order extends Model
{
}
```

---

## Model with Relationships

### Before
```php
class Comment extends Model
{
    protected $with = ['user', 'post'];
    protected $appends = ['full_comment'];
    protected $touches = ['post'];
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\With;
use Illuminate\Database\Eloquent\Attributes\Appends;
use Illuminate\Database\Eloquent\Attributes\Touches;

#[With(['user', 'post'])]
#[Appends(['full_comment'])]
#[Touches(['post'])]
class Comment extends Model
{
}
```

---

## Model with Observers

### Before
```php
class Post extends Model
{
    protected $dispatchesEvents = [
        'created' => PostCreated::class,
        'updated' => PostUpdated::class,
    ];
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\DispatchesEvents;

#[DispatchesEvents]
class Post extends Model
{
    protected function dispatchEventsFor(string $event): void
    {
        match ($event) {
            'created' => event(new PostCreated($this)),
            'updated' => event(new PostUpdated($this)),
            default => null,
        };
    }
}
```

---

## Model with Factory

### Before
```php
class User extends Model
{
    // This was typically in the factory
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\UseFactory;
use Database\Factories\UserFactory;

#[UseFactory(UserFactory::class)]
class User extends Model
{
}
```

---

## Model with Policy

### Before
```php
// Old way - in AuthServiceProvider
Gate::policy(Post::class, PostPolicy::class);
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\UsePolicy;
use App\Policies\PostPolicy;

#[UsePolicy(PostPolicy::class)]
class Post extends Model
{
}
```

---

## Model with Scope

### Before
```php
class Post extends Model
{
    public function scopePublished($query)
    {
        return $query->whereNotNull('published_at');
    }
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Attributes\Scope;

class Post extends Model
{
    #[Scope]
    public function published(Builder $query): void
    {
        $query->whereNotNull('published_at');
    }
}
```

---

## Model with DateFormat

### Before
```php
class Event extends Model
{
    protected $dateFormat = 'Y-m-d H:i:s';
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\DateFormat;

#[DateFormat('Y-m-d H:i:s')]
class Event extends Model
{
}
```

---

## Model with WithoutIncrementing

### Before
```php
class UUIDModel extends Model
{
    public $incrementing = false;
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\WithoutIncrementing;

#[WithoutIncrementing]
class UUIDModel extends Model
{
}
```

---

## Model with WithoutTimestamps

### Before
```php
class LogEntry extends Model
{
    public $timestamps = false;
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\WithoutTimestamps;

#[WithoutTimestamps]
class LogEntry extends Model
{
}
```

---

## Complete Example

### Before
```php
class Article extends Model
{
    protected $table = 'articles';
    protected $primaryKey = 'article_id';
    protected $fillable = ['title', 'slug', 'content', 'author_id'];
    protected $guarded = ['id', 'is_featured'];
    protected $hidden = ['author_id', 'deleted_at'];
    protected $visible = ['title', 'slug', 'content', 'published_at'];
    protected $casts = [
        'is_featured' => 'boolean',
        'published_at' => 'datetime',
        'metadata' => 'array',
    ];
    protected $with = ['author'];
    protected $appends = ['full_title'];
    protected $touches = ['category'];
    public $timestamps = true;
}
```

### After
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\Table;
use Illuminate\Database\Eloquent\Attributes\Fillable;
use Illuminate\Database\Eloquent\Attributes\Guarded;
use Illuminate\Database\Eloquent\Attributes\Hidden;
use Illuminate\Database\Eloquent\Attributes\Visible;
use Illuminate\Database\Eloquent\Attributes\Cast;
use Illuminate\Database\Eloquent\Attributes\With;
use Illuminate\Database\Eloquent\Attributes\Appends;
use Illuminate\Database\Eloquent\Attributes\Touches;

#[Table(
    name: 'articles',
    key: 'article_id',
    timestamps: true,
)]
#[Fillable(['title', 'slug', 'content', 'author_id'])]
#[Guarded(['id', 'is_featured'])]
#[Hidden(['author_id', 'deleted_at'])]
#[Visible(['title', 'slug', 'content', 'published_at'])]
#[Cast('is_featured', 'boolean')]
#[Cast('published_at', 'datetime')]
#[Cast('metadata', 'array')]
#[With(['author'])]
#[Appends(['full_title'])]
#[Touches(['category'])]
class Article extends Model
{
}
```
