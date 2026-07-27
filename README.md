# ⚡Testing-and-Quality-Assurance-Guide-Part-1

This project demonstrates the fundamentals of **Testing & Quality Assurance in Laravel** using **PHPUnit, Assertions, Pest, and Mocking**.

Automated testing helps ensure that application features work correctly, prevents future bugs, and improves code reliability during development.

Instead of manually testing every feature after code changes, Laravel allows developers to write automated tests that verify expected behavior.

---

# ❓ Why Use Testing & Quality Assurance?

Testing is useful when:

- You want to prevent bugs before deployment.
- You want confidence while modifying existing code.
- You want to verify business logic automatically.
- You want reliable and maintainable applications.
- You want safer refactoring.

Common examples:

- User Authentication Testing
- API Endpoint Testing
- Database Testing
- Email Testing
- Payment Flow Testing
- File Upload Testing
- Notification Testing

Without automated testing:

```
Code Change
     │
     ▼
Manual Testing
     │
     ▼
Possible Human Error
```

With automated testing:

```
Code Change
     │
     ▼
Run Tests
     │
     ▼
Automatic Verification
```

---

# 🧩 Testing Concepts Covered

✅ PHPUnit  
✅ Unit Testing  
✅ Assertions  
✅ Pest Testing Framework  
✅ Mocking & Laravel Fakes  
✅ Database Testing  

---

# 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Laravel 10 | PHP Framework |
| PHPUnit | Testing Framework |
| Pest | Modern Testing Framework |
| Eloquent ORM | Database Testing |
| MySQL | Database |

---

# 1️⃣ Laravel Unit Testing

## What is Unit Testing?

Unit Testing verifies that a small piece of code works correctly.

A unit can be:

- A Function
- A Method
- A Class

Example:

A calculator method:

```php
public function add($a, $b)
{
    return $a + $b;
}
```

Expected:

```
2 + 3 = 5
```

The test checks whether the method returns the correct result.

---

# Create a Test

Generate a test:

```bash
php artisan make:test CalculatorTest
```

Creates:

```
tests/Feature/CalculatorTest.php
```

---

# Example Unit Test

```php
namespace Tests\Feature;

use Tests\TestCase;

class CalculatorTest extends TestCase
{

    public function test_addition_returns_correct_result()
    {
        $result = 2 + 3;

        $this->assertEquals(5, $result);
    }

}
```

Run test:

```bash
php artisan test
```

Output:

```
PASS CalculatorTest
```

---

# 2️⃣ PHPUnit

## What is PHPUnit?

PHPUnit is the default testing framework used by Laravel.

It provides:

- Test execution
- Assertions
- Mocking support
- Test reports

Run PHPUnit:

```bash
vendor/bin/phpunit
```

or:

```bash
php artisan test
```

---

# PHPUnit Test Structure

```php
class UserTest extends TestCase
{

    public function test_user_exists()
    {

        $user = User::factory()->create();

        $this->assertNotNull($user);

    }

}
```

---

# 3️⃣ Assertions

## What are Assertions?

Assertions verify that the actual result matches the expected result.

Think:

```
Expected Result
       |
       |
       ▼
Assertion Check
       |
       |
       ▼
PASS / FAIL
```

---

# Common Assertions

## Check Equality

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
$this->assertDatabaseHas('users', [

    'email' => 'test@example.com'

]);
```

---

## Check API Response

```php
$response = $this->get('/dashboard');

$response->assertStatus(200);
```

---

## Check Redirect

```php
$response->assertRedirect('/login');
```

---

# 4️⃣ Feature Testing Example

Testing user registration:

```php
public function test_user_can_register()
{

    $response = $this->post('/register',[

        'name'=>'John',

        'email'=>'john@test.com',

        'password'=>'password',

        'password_confirmation'=>'password'

    ]);


    $response->assertRedirect('/dashboard');


    $this->assertDatabaseHas('users',[

        'email'=>'john@test.com'

    ]);

}
```

---

# 5️⃣ Pest Testing

## What is Pest?

Pest is a modern testing framework built on top of PHPUnit.

It provides cleaner and simpler syntax.

---

# Install Pest

```bash
composer require pestphp/pest --dev
```

Install Laravel Pest Plugin:

```bash
composer require pestphp/pest-plugin-laravel --dev
```

---

# PHPUnit Style

```php
public function test_user_is_created()
{

    $user = User::factory()->create();

    $this->assertNotNull($user);

}
```

---

# Pest Style

```php
test('user is created', function () {

    $user = User::factory()->create();


    expect($user)
        ->not()
        ->toBeNull();

});
```

---

# Pest Assertions

Example:

```php
test('user has correct email', function(){

    $user = User::factory()->create([
        'email'=>'test@test.com'
    ]);


    expect($user->email)
        ->toBe('test@test.com');

});
```

---

# 6️⃣ Mocking

## What is Mocking?

Mocking means creating a fake version of an external dependency.

Used when testing:

- Emails
- APIs
- Payments
- Notifications
- Queues

Instead of performing the real action:

```
Send Real Email ❌

Fake Email Service ✅
```

---

# Example Without Mocking

```php
Mail::to($user)
    ->send(new WelcomeMail());
```

Problem:

- Sends real email
- Slow tests
- Requires mail server

---

# Mock Email Sending

Laravel provides:

```php
Mail::fake();
```

Example:

```php
use Illuminate\Support\Facades\Mail;


public function test_email_is_sent()
{

    Mail::fake();


    Mail::to('test@test.com')
        ->send(new WelcomeMail());


    Mail::assertSent(
        WelcomeMail::class
    );

}
```

---

# 7️⃣ Laravel Fake Testing

Laravel provides built-in fakes.

---

## Notification Fake

```php
Notification::fake();


$user->notify(
    new OrderNotification()
);


Notification::assertSentTo(
    $user,
    OrderNotification::class
);
```

---

## Queue Fake

```php
Queue::fake();


SendReport::dispatch();


Queue::assertPushed(
    SendReport::class
);
```

---

## Event Fake

```php
Event::fake();


event(new OrderCreated());


Event::assertDispatched(
    OrderCreated::class
);
```

---

## Storage Fake

Testing file uploads:

```php
Storage::fake('public');


$file = UploadedFile::fake()
    ->image('photo.jpg');


Storage::disk('public')
    ->assertExists(
        'photo.jpg'
    );
```

---

# 📋 Testing Flow

```
Write Code
     │
     ▼
Create Test Case
     │
     ▼
Run Test
     │
     ▼
Assertions Check Result
     │
     ▼
PASS / FAIL
```
