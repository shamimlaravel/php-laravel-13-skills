# Form Request Examples

## Basic Form Request

### Before
```php
class CreatePostRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'title' => 'required|max:255',
            'body' => 'required',
        ];
    }
}
```

### After
```php
use Illuminate\Foundation\Http\FormRequest;

class CreatePostRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'title' => 'required|max:255',
            'body' => 'required',
        ];
    }
}
```

---

## Form Request with Error Bag

### Before
```php
class CreatePostRequest extends FormRequest
{
    protected $errorBag = 'createPost';

    public function rules(): array
    {
        return ['title' => 'required|max:255'];
    }
}
```

### After
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Foundation\Http\Attributes\ErrorBag;

#[ErrorBag('createPost')]
class CreatePostRequest extends FormRequest
{
    public function rules(): array
    {
        return ['title' => 'required|max:255'];
    }
}
```

---

## Form Request with Redirect

### Before
```php
class StorePostRequest extends FormRequest
{
    protected $redirect = '/posts/create';

    public function rules(): array
    {
        return ['title' => 'required'];
    }
}
```

### After
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Foundation\Http\Attributes\RedirectTo;

#[RedirectTo('/posts/create')]
class StorePostRequest extends FormRequest
{
    public function rules(): array
    {
        return ['title' => 'required'];
    }
}
```

---

## Form Request with Named Route Redirect

### Before
```php
class StorePostRequest extends FormRequest
{
    protected $redirectRoute = 'posts.create';

    public function rules(): array
    {
        return ['title' => 'required'];
    }
}
```

### After
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Foundation\Http\Attributes\RedirectToRoute;

#[RedirectToRoute('posts.create')]
class StorePostRequest extends FormRequest
{
    public function rules(): array
    {
        return ['title' => 'required'];
    }
}
```

---

## Form Request with Action Redirect

### Before
```php
class StorePostRequest extends FormRequest
{
    protected $redirectAction = [PostController::class, 'create'];

    public function rules(): array
    {
        return ['title' => 'required'];
    }
}
```

### After
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Foundation\Http\Attributes\RedirectToAction;
use App\Http\Controllers\PostController;

#[RedirectToAction(PostController::class, 'create')]
class StorePostRequest extends FormRequest
{
    public function rules(): array
    {
        return ['title' => 'required'];
    }
}
```

---

## Form Request with Stop on First Failure

### Before
```php
class ImportRequest extends FormRequest
{
    protected $stopOnFirstFailure = true;

    public function rules(): array
    {
        return [
            'file' => 'required|file|mimes:csv',
            'mapping' => 'required|array',
        ];
    }
}
```

### After
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Foundation\Http\Attributes\StopOnFirstFailure;

#[StopOnFirstFailure]
class ImportRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'file' => 'required|file|mimes:csv',
            'mapping' => 'required|array',
        ];
    }
}
```

---

## Complete Example

### Before
```php
class UpdateUserRequest extends FormRequest
{
    protected $errorBag = 'updateUser';
    protected $redirectRoute = 'users.edit';
    protected $stopOnFirstFailure = true;

    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email,' . $this->user->id,
            'avatar' => 'nullable|image|max:1024',
        ];
    }
}
```

### After
```php
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Foundation\Http\Attributes\ErrorBag;
use Illuminate\Foundation\Http\Attributes\RedirectToRoute;
use Illuminate\Foundation\Http\Attributes\StopOnFirstFailure;

#[ErrorBag('updateUser')]
#[RedirectToRoute('users.edit')]
#[StopOnFirstFailure]
class UpdateUserRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email,' . $this->user->id,
            'avatar' => 'nullable|image|max:1024',
        ];
    }
}
```
