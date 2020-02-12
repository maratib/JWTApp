# Setting JWT in Laravel 6
```bash
#install JWT authentication package
composer require tymon/jwt-auth:dev-develop --prefer-source

#publish the package configuration and choose
#Provider: Tymon\JWTAuth\Providers\LaravelServiceProvider
php artisan vendor:publish
#Copied File [\vendor\tymon\jwt-auth\config\config.php] To [\config\jwt.php]


#Generate JWT auth. keys
php artisan jwt:secret
#the above command generates
#jwt-auth secret [XyzdaDlOZ44RPAxWSl3ZVJuSYqZQkIAMBWhLRtaPftXrP5k2lHGq4QeiG11Q92S] set successfully.

```

## Register JWT Middleware
JWT comes with a pre built middleware for API routes.
Open `app/Http/Kernel.php`
```php
protected $routeMiddleware = [
    .
    .
'auth.jwt'  =>  \Tymon\JWTAuth\Http\Middleware\Authenticate::class, //JWT middleware   
]
```
## Setting up API routes
Open `routes/api.php`
```php
Route::post('login', 'ApiController@login');
Route::post('register', 'ApiController@register');

Route::group(['middleware' => 'auth.jwt'], function () {
Route::get('logout', 'ApiController@logout');

Route::get('tasks', 'TaskController@index');
Route::get('tasks/{id}', 'TaskController@show');
Route::post('tasks', 'TaskController@store');
Route::put('tasks/{id}', 'TaskController@update');
Route::delete('tasks/{id}', 'TaskController@destroy');
});
```

## Creating Registration Form Request
Now start implementing the logic
```bash
php artisan make:request RegistrationFormRequest

#Now open RegistrationFormRequest from app/Http/Requests folder and make the changes as follows:-
set authorize to return true
and rules to return 
'name' => 'required|string',
'email' => 'required|email|unique:users',
'password' => 'required|string|min:6|max:10'
```

## Creating API Controller for Login and Registration

```bash
php artisan make:controller APIController
```
