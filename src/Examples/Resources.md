# HTTP Resource Examples

## Basic Resource

### Before
```php
class PostResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'title' => $this->title,
            'body' => $this->body,
        ];
    }
}
```

### After
```php
use Illuminate\Http\Resources\Json\JsonResource;

class PostResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'title' => $this->title,
            'body' => $this->body,
        ];
    }
}
```

---

## Resource with Collects

### Before
```php
class PostCollection extends ResourceCollection
{
    public $collects = PostResource::class;
}
```

### After
```php
use Illuminate\Http\Resources\Json\ResourceCollection;
use Illuminate\Http\Resources\Attributes\Collects;
use App\Http\Resources\PostResource;

#[Collects(PostResource::class)]
class PostCollection extends ResourceCollection
{
}
```

---

## Resource with PreserveKeys

### Before
```php
class CountryResource extends JsonResource
{
    public $preserveKeys = true;

    public function toArray(Request $request): array
    {
        return [
            'code' => $this->code,
            'name' => $this->name,
        ];
    }
}
```

### After
```php
use Illuminate\Http\Resources\Json\JsonResource;
use Illuminate\Http\Resources\Attributes\PreserveKeys;

#[PreserveKeys]
class CountryResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'code' => $this->code,
            'name' => $this->name,
        ];
    }
}
```

---

## Complete Example

### Before
```php
class UserCollection extends ResourceCollection
{
    public $collects = UserResource::class;
    public $preserveKeys = true;
}
```

### After
```php
use Illuminate\Http\Resources\Json\ResourceCollection;
use Illuminate\Http\Resources\Attributes\Collects;
use Illuminate\Http\Resources\Attributes\PreserveKeys;
use App\Http\Resources\UserResource;

#[Collects(UserResource::class)]
#[PreserveKeys]
class UserCollection extends ResourceCollection
{
}
```
