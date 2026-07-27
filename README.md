# ⚡Testing-and-Quality-Assurance-Guide-Part-1

This project demonstrates the fundamentals of **Unit Testing in Laravel 10** using **PHPUnit**, **Pest**, **Assertions**, and **Mocking**.

Testing allows developers to verify application behavior automatically and ensures that new changes do not break existing functionality.

Instead of manually checking every feature after development, automated tests validate the application's logic, database operations, and external service interactions.

---

# ❓ Why Use Unit Testing?

Unit Testing is useful when:

- You want to verify application logic automatically.
- You want safer code changes.
- You want to identify bugs early.
- You want reliable and maintainable applications.
- You want confidence before deploying to production.

Common testing scenarios:

- User Registration
- User Login
- API Responses
- Database Operations
- Payment Processing
- Order Creation
- Email Notifications
- Background Jobs

---

# 🧩 What This Project Contains

✅ PHPUnit Testing  
✅ Pest Testing  
✅ Feature Testing  
✅ Assertions  
✅ Database Testing  
✅ Mocking  
✅ Mail Fake Testing  
✅ Queue Fake Testing  
✅ Event Fake Testing  
✅ Notification Fake Testing  

---

# 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Laravel 10 | PHP Framework |
| PHPUnit | Default Testing Framework |
| Pest | Modern Testing Framework |
| Eloquent ORM | Database Testing |
| MySQL | Database |
| Laravel Testing Tools | Application Testing |

---

# 🚀 Important Steps

---

# 1️⃣ Laravel PHPUnit

## What is PHPUnit?

PHPUnit is the default testing framework included with Laravel.

It allows developers to write automated tests for:

- Controllers
- Models
- Services
- APIs
- Application Logic

Think:

> "Code that tests your code."

---

## Create Test

Generate a test file:

```bash
php artisan make:test UserTest
```

Creates:

```
tests/Feature/UserTest.php
```

---

## Example PHPUnit Test

```php
namespace Tests\Feature;

use Tests\TestCase;

class UserTest extends TestCase
{

    public function test_example()
    {

        $result = true;

        $this->assertTrue($result);

    }

}
```

Run tests:

```bash
php artisan test
```

---

# 2️⃣ Feature Testing

Feature tests verify complete application workflows.

Example:

Testing user registration.

---

## User Registration Test

```php
use App\Models\User;


public function test_user_can_register()
{

    $response = $this->post('/register', [

        'name' => 'John',

        'email' => 'john@test.com',

        'password' => 'password',

        'password_confirmation' => 'password'

    ]);


    $response->assertStatus(302);


    $this->assertDatabaseHas('users', [

        'email' => 'john@test.com'

    ]);

}
```

---

## Testing Flow

```
User Registration Request

        │

        ▼

Validate Data

        │

        ▼

Create User

        │

        ▼

Check Database

        │

        ▼

Test Passed ✅
```

---

# 3️⃣ Laravel Assertions

## What are Assertions?

Assertions check whether the actual result matches the expected result.

Example:

```
Expected Result = 5

Actual Result   = 5

PASS ✅
```

---

# Common Assertions

## Check Values

```php
$this->assertEquals(
    10,
    $total
);
```

---

## Check True

```php
$this->assertTrue(
    $user->is_active
);
```

---

## Check False

```php
$this->assertFalse(
    $user->is_blocked
);
```

---

## Check Database Record

```php
$this->assertDatabaseHas(
    'users',
    [
        'email'=>'test@test.com'
    ]
);
```

---

## Check Database Count

```php
$this->assertDatabaseCount(
    'users',
    5
);
```

---

## Check Redirect

```php
$response->assertRedirect('/dashboard');
```

---

## Check HTTP Status

```php
$response->assertStatus(200);
```

---

# 4️⃣ Testing Authentication

Example:

Testing user login.

```php
use App\Models\User;


public function test_user_can_login()
{

    $user = User::factory()->create([

        'password'=>bcrypt('password')

    ]);


    $response = $this->post('/login',[

        'email'=>$user->email,

        'password'=>'password'

    ]);


    $response->assertRedirect('/dashboard');


    $this->assertAuthenticated();

}
```

---

# 5️⃣ Laravel Pest

## What is Pest?

Pest is a modern testing framework built on top of PHPUnit.

It provides cleaner syntax and improves developer experience.

---

## Install Pest

```bash
composer require pestphp/pest --dev
```

Laravel plugin:

```bash
composer require pestphp/pest-plugin-laravel --dev
```

---

# PHPUnit Style

```php
public function test_user_exists()
{

    $this->assertTrue(true);

}
```

---

# Pest Style

```php
test('user exists', function () {

    expect(true)->toBeTrue();

});
```

---

# Pest Laravel Example

```php
use App\Models\User;


test('authenticated user exists', function () {


    $user = User::factory()->create();


    $this->actingAs($user);


    expect(auth()->check())
        ->toBeTrue();


});
```

---

# 6️⃣ Mocking

## What is Mocking?

Mocking creates fake versions of external services during testing.

It prevents tests from performing real operations.

Examples:

- Sending emails
- Processing queues
- Sending notifications
- Triggering events

---

## Without Mocking

```
Test

 |

 ▼

Real Email Sent

 |

 ▼

Slow Testing
```

---

## With Mocking

```
Test

 |

 ▼

Fake Email

 |

 ▼

Fast Testing
```

---

# 7️⃣ Mail Mocking

Controller:

```php
Mail::to($user->email)
    ->send(new WelcomeMail());
```

Test:

```php
use Illuminate\Support\Facades\Mail;


Mail::fake();


$this->post('/register');


Mail::assertSent(
    WelcomeMail::class
);
```

Result:

```
Email was not actually sent.

Laravel only verified the action.
```

---

# 8️⃣ Notification Mocking

```php
use Illuminate\Support\Facades\Notification;


Notification::fake();


Notification::assertSentTo(
    $user,
    OrderNotification::class
);
```

---

# 9️⃣ Queue Mocking

Testing background jobs.

```php
use Illuminate\Support\Facades\Queue;


Queue::fake();


SendWelcomeEmail::dispatch($user);


Queue::assertPushed(
    SendWelcomeEmail::class
);
```

---

# 🔟 Event Mocking

```php
use Illuminate\Support\Facades\Event;


Event::fake();


event(new OrderPlaced($order));


Event::assertDispatched(
    OrderPlaced::class
);
```

---

# 1️⃣1️⃣ Database Testing

Laravel provides database testing helpers.

---

## Refresh Database

```php
use RefreshDatabase;


class UserTest extends TestCase
{

    use RefreshDatabase;

}
```

---

## Create Test Data

Using Factory:

```php
$user = User::factory()->create();
```

---

## Verify Database

```php
$this->assertDatabaseHas(
    'users',
    [
        'email'=>$user->email
    ]
);
```

---

# 📋 Testing Workflow

```
Developer Writes Code

        │

        ▼

Create Test Case

        │

        ▼

Execute Application Logic

        │

        ▼

Run Assertions

        │

        ▼

PASS / FAIL Result
```

---

# ⚖️ PHPUnit vs Pest

| Feature | PHPUnit | Pest |
|---------|---------|------|
| Laravel Default | ✅ | ✅ |
| Mature Framework | ✅ | ✅ |
| Cleaner Syntax | ❌ | ✅ |
| Beginner Friendly | Medium | Easy |
| Built on PHPUnit | ❌ | ✅ |

---

# 🔥 Real World Example

## E-Commerce Application Testing

Application Flow:

```
Customer Places Order

        │

        ▼

Create Order

        │

        ▼

Process Payment

        │

        ▼

Send Email

        │

        ▼

Update Inventory
```

Tests:

✅ Order Created  
✅ Payment Successful  
✅ Email Triggered  
✅ Inventory Updated  
✅ Queue Executed  

---

# 📦 Useful Laravel Commands

## Create Test

```bash
php artisan make:test UserTest
```

---

## Run All Tests

```bash
php artisan test
```

---

## Run Specific Test

```bash
php artisan test --filter UserTest
```

---

## Run PHPUnit Directly

```bash
vendor/bin/phpunit
```

---

## Clear Cache

```bash
php artisan optimize:clear
```

---

## Start Development Server

```bash
php artisan serve
```

---

# 🎯 Key Takeaway

Unit Testing makes Laravel applications:

✅ More reliable  
✅ Easier to maintain  
✅ Safer to modify  
✅ Easier to debug  
✅ Production ready  

PHPUnit provides the testing foundation.

Pest provides cleaner syntax.

Assertions verify expected results.

Mocking allows testing without depending on external services.

Together, these tools help build scalable and professional Laravel applications.

---

# 📄 License

This project is open-source and available under the **MIT License**.
