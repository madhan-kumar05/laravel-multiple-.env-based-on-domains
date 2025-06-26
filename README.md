# Laravel Multiple `.env` Files Based on Subdomain

This guide explains how to load different `.env` files in a Laravel application based on the subdomain the user is visiting. This is useful when you want different configurations for different environments or subdomains.

## Steps to Implement

### 1. Create Multiple `.env` Files

In the root of your Laravel project, create separate `.env` files for each subdomain. For example:

- `.env` (default environment)
- `.env.subdomain1`
- `.env.subdomain2`

You can create as many `.env.<subdomain>` files as you need, each containing different environment variables for the respective subdomain.

### 2. Create a Middleware to Load the `.env` File

Next, you'll create a middleware to check the subdomain of the incoming request and load the corresponding `.env` file.

Run the following Artisan command to create a new middleware:

```bash
php artisan make:middleware SubdomainEnvMiddleware
```
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Config;
use Dotenv\Dotenv;

class SubdomainEnvMiddleware
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        $subdomain = $this->getSubdomain($request);

        // Load .env file based on subdomain
        $envFile = base_path('.env.' . $subdomain);
        if (file_exists($envFile)) {
            $dotenv = Dotenv::createImmutable(base_path(), '.env.' . $subdomain);
            $dotenv->load();
        }

        return $next($request);
    }

    /**
     * Get the subdomain from the request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return string
     */
    private function getSubdomain($request)
    {
        $host = $request->getHost();
        $subdomain = explode('.', $host)[0];  // Getting the first part of the domain
        return $subdomain;
    }
}
```
