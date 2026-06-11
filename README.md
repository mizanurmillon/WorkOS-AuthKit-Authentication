# WorkOS AuthKit Setup Guide for Laravel

## Introduction

This guide explains how to integrate WorkOS AuthKit into a Laravel application. WorkOS AuthKit provides modern authentication features including:

* Email & Password Authentication
* Social Login (Google, GitHub, Microsoft, Apple)
* Magic Link Authentication
* Multi-Factor Authentication (MFA)
* Enterprise Single Sign-On (SSO)
* User Management

---

## Prerequisites

Before starting, make sure you have:

* PHP 8.2+ (Laravel 12) or PHP 8.3+ (Laravel 13)
* Composer installed
* Laravel project created
* WorkOS account

---

## Step 1: Create a WorkOS Account

1. Visit https://workos.com
2. Create an account.
3. Create a new project.
4. Open the project dashboard.
5. Copy the following credentials:

   * Client ID
   * API Key

Keep these credentials safe.

---

## Step 2: Install WorkOS Laravel SDK

Run:

```bash
composer require workos/workos-php
```

---

## Step 3: Add Environment Variables

Open your `.env` file and add:

```env
WORKOS_CLIENT_ID=your_client_id
WORKOS_API_KEY=your_api_key
WORKOS_REDIRECT_URI=https://your-domain.com/callback
```

Example:

```env
WORKOS_CLIENT_ID=client_123456
WORKOS_API_KEY=sk_test_abcdef
WORKOS_REDIRECT_URI=http://localhost:8000/callback
```

---

## Step 4: Create WorkOS Configuration

Create:

```php
config/workos.php
```

```php
<?php

return [
    'client_id' => env('WORKOS_CLIENT_ID'),
    'api_key' => env('WORKOS_API_KEY'),
    'redirect_uri' => env('WORKOS_REDIRECT_URI'),
];
```

---

## Step 5: Create Authentication Controller

Create:

```bash
php artisan make:controller Auth/WorkOSController
```

Example:

```php
<?php

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

class WorkOSController extends Controller
{
    public function login()
    {
        return redirect('https://api.workos.com/user_management/authorize');
    }

    public function callback(Request $request)
    {
        // Handle authentication callback

        $user = [
            'email' => 'user@example.com',
            'name' => 'Demo User'
        ];

        return redirect('/dashboard');
    }
}
```

---

## Step 6: Register Routes

Open:

```php
routes/web.php
```

Add:

```php
use App\Http\Controllers\Auth\WorkOSController;

Route::get('/login', [WorkOSController::class, 'login'])
    ->name('login');

Route::get('/callback', [WorkOSController::class, 'callback']);
```

---

## Step 7: Create Login Button

Example Blade view:

```html
<a href="{{ route('login') }}">
    Continue with WorkOS
</a>
```

---

## Step 8: Configure Social Providers

In the WorkOS Dashboard:

1. Navigate to Authentication.
2. Open Social Connections.
3. Enable:

   * Google
   * GitHub
   * Microsoft
   * Apple

Follow provider-specific instructions.

---

## Step 9: Enable Multi-Factor Authentication

Dashboard:

Authentication → Security → MFA

Enable:

* SMS Verification
* TOTP Applications
* Backup Codes

Save changes.

---

## Step 10: Enable Enterprise SSO

Dashboard:

Authentication → Enterprise Connections

Create:

* SAML Connection
* OIDC Connection

Provide metadata from your enterprise customer.

---

## Testing Authentication

Start Laravel:

```bash
php artisan serve
```

Visit:

```text
http://localhost:8000/login
```

Complete authentication and verify redirection.

---

## User Flow

1. User clicks Login.
2. User is redirected to WorkOS.
3. User authenticates.
4. WorkOS returns to callback URL.
5. Laravel creates or updates user.
6. User is logged in.
7. User is redirected to Dashboard.

---

## Recommended Production Settings

### Enable HTTPS

```env
APP_URL=https://your-domain.com
```

### Secure Cookies

```env
SESSION_SECURE_COOKIE=true
```

### Queue Emails

```bash
php artisan queue:work
```

### Enable MFA

Always enable MFA for admin accounts.

---

## Troubleshooting

### Invalid Client ID

Verify:

```env
WORKOS_CLIENT_ID=
```

matches your WorkOS dashboard.

### Invalid Redirect URI

Verify the callback URL exactly matches the URL configured in WorkOS.

### Social Login Not Working

Check:

* Provider credentials
* Redirect URLs
* WorkOS connection status

---

## Useful Commands

```bash
php artisan optimize:clear
php artisan config:clear
php artisan route:clear
php artisan cache:clear
php artisan serve
```

---

## Conclusion

WorkOS AuthKit provides enterprise-grade authentication with minimal development effort. It is ideal for SaaS, B2B, and multi-tenant Laravel applications that require social login, MFA, and enterprise SSO support.
