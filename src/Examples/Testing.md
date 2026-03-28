# Testing Examples

## Test with Seed

### Before
```php
class ProductPageTest extends TestCase
{
    protected $seed = true;

    public function test_products_page_shows_seeded_data(): void
    {
        $this->get('/products')->assertOk();
    }
}
```

### After
```php
use Illuminate\Foundation\Testing\Attributes\Seed;
use Tests\TestCase;

#[Seed]
class ProductPageTest extends TestCase
{
    public function test_products_page_shows_seeded_data(): void
    {
        $this->get('/products')->assertOk();
    }
}
```

---

## Test with Specific Seeder

### Before
```php
class AdminAccessTest extends TestCase
{
    protected $seeder = RoleAndPermissionSeeder::class;

    public function test_admin_can_access_dashboard(): void
    {
        $admin = User::factory()->admin()->create();
        $this->actingAs($admin)->get('/admin')->assertOk();
    }
}
```

### After
```php
use Illuminate\Foundation\Testing\Attributes\Seeder;
use Tests\TestCase;
use Database\Seeders\RoleAndPermissionSeeder;

#[Seeder(RoleAndPermissionSeeder::class)]
class AdminAccessTest extends TestCase
{
    public function test_admin_can_access_dashboard(): void
    {
        $admin = User::factory()->admin()->create();
        $this->actingAs($admin)->get('/admin')->assertOk();
    }
}
```

---

## Test with SetUp Attribute

### Before
```php
class OrderTest extends TestCase
{
    private User $user;

    protected function setUp(): void
    {
        parent::setUp();
        $this->user = User::factory()->create();
    }

    public function test_user_can_place_order(): void
    {
        $this->actingAs($this->user)
            ->post('/orders', ['product_id' => 1])
            ->assertCreated();
    }
}
```

### After
```php
use Illuminate\Foundation\Testing\Attributes\SetUp;
use Tests\TestCase;
use App\Models\User;

class OrderTest extends TestCase
{
    private User $user;

    #[SetUp]
    public function createUser(): void
    {
        $this->user = User::factory()->create();
    }

    public function test_user_can_place_order(): void
    {
        $this->actingAs($this->user)
            ->post('/orders', ['product_id' => 1])
            ->assertCreated();
    }
}
```

---

## Test with TearDown Attribute

### Before
```php
class ExternalApiTest extends TestCase
{
    protected function tearDown(): void
    {
        Cache::tags('external-api')->flush();
        parent::tearDown();
    }

    public function test_api_returns_data(): void
    {
        // Test code
    }
}
```

### After
```php
use Illuminate\Foundation\Testing\Attributes\TearDown;
use Tests\TestCase;
use Illuminate\Support\Facades\Cache;

class ExternalApiTest extends TestCase
{
    #[TearDown]
    public function clearApiCache(): void
    {
        Cache::tags('external-api')->flush();
    }

    public function test_api_returns_data(): void
    {
        // Test code
    }
}
```

---

## Complete Example

### Before
```php
class UserManagementTest extends TestCase
{
    protected $seed = true;
    protected $seeder = UserSeeder::class;
    private User $admin;
    private User $user;

    protected function setUp(): void
    {
        parent::setUp();
        $this->admin = User::where('email', 'admin@example.com')->first();
        $this->user = User::factory()->create();
    }

    public function test_admin_can_view_users(): void
    {
        $this->actingAs($this->admin)->get('/users')->assertOk();
    }

    public function test_regular_user_cannot_view_users(): void
    {
        $this->actingAs($this->user)->get('/users')->assertForbidden();
    }
}
```

### After
```php
use Illuminate\Foundation\Testing\Attributes\Seed;
use Illuminate\Foundation\Testing\Attributes\Seeder;
use Illuminate\Foundation\Testing\Attributes\SetUp;
use Tests\TestCase;
use App\Models\User;
use Database\Seeders\UserSeeder;

#[Seed]
#[Seeder(UserSeeder::class)]
class UserManagementTest extends TestCase
{
    private User $admin;
    private User $user;

    #[SetUp]
    public function setupUsers(): void
    {
        $this->admin = User::where('email', 'admin@example.com')->first();
        $this->user = User::factory()->create();
    }

    public function test_admin_can_view_users(): void
    {
        $this->actingAs($this->admin)->get('/users')->assertOk();
    }

    public function test_regular_user_cannot_view_users(): void
    {
        $this->actingAs($this->user)->get('/users')->assertForbidden();
    }
}
```
