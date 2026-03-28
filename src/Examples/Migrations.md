# Migration Examples

Note: Laravel 13 does not use PHP attributes for migrations. However, this document shows migration best practices for Laravel 13.

## Creating Migrations

### Basic Migration

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

## Using Model with New Attributes

After creating migrations, use attributes in models:

```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\Table;
use Illuminate\Database\Eloquent\Attributes\Fillable;
use Illuminate\Database\Eloquent\Attributes\Cast;

#[Table('posts')]
#[Fillable(['title', 'body'])]
#[Cast('created_at', 'datetime')]
#[Cast('updated_at', 'datetime')]
class Post extends Model
{
    // Use with migration-created table
}
```

## Best Practices

1. Use migrations to create database tables
2. Use attributes to configure the Eloquent model
3. Attributes and migrations work together:
   - Migration: Creates/alters database structure
   - Attributes: Configures model behavior

## Common Migration Methods with Attribute Equivalents

| Migration Method | Model Property | Attribute |
|-----------------|----------------|-----------|
| `$table->string('email')` | - | - |
| `$table->foreignId('user_id')` | - | - |
| `$table->timestamps()` | `$timestamps` | Use `#[Table(timestamps: true)]` |
| `$table->softDeletes()` | - | Model needs `SoftDeletes` trait |

## Example: Complete Workflow

### 1. Create Migration

```php
// database/migrations/2026_01_01_000001_create_posts_table.php
return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body')->nullable();
            $table->boolean('is_published')->default(false);
            $table->decimal('price', 10, 2)->nullable();
            $table->foreignId('user_id')->constrained()->onDelete('cascade');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

### 2. Create Model with Attributes

```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Attributes\Table;
use Illuminate\Database\Eloquent\Attributes\Fillable;
use Illuminate\Database\Eloquent\Attributes\Guarded;
use Illuminate\Database\Eloquent\Attributes\Cast;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

#[Table('posts')]
#[Fillable(['title', 'body', 'is_published', 'price', 'user_id'])]
#[Guarded(['id'])]
#[Cast('is_published', 'boolean')]
#[Cast('price', 'decimal:2')]
#[Cast('created_at', 'datetime')]
#[Cast('updated_at', 'datetime')]
class Post extends Model
{
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```
