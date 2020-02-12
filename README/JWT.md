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

// Route::get('tasks', 'TaskController@index');
// Route::get('tasks/{id}', 'TaskController@show');
// Route::post('tasks', 'TaskController@store');
// Route::put('tasks/{id}', 'TaskController@update');
// Route::delete('tasks/{id}', 'TaskController@destroy');
});
```
## Update User Model 

```php
// Add imports and implement the interface
use Tymon\JWTAuth\Contracts\JWTSubject;

class User extends Authenticatable implements JWTSubject


// Add the following methods at the bottom of app/User.php

/**
 * Get the identifier that will be stored in the subject claim of the JWT.
 *
 * @return mixed
 */
public function getJWTIdentifier()
{
    return $this->getKey();
}

/**
 * Return a key value array, containing any custom claims to be added to the JWT.
 *
 * @return array
 */
public function getJWTCustomClaims()
{
    return [];
}
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

```php
namespace App\Http\Controllers;

use JWTAuth;
use App\User;
use Illuminate\Http\Request;
use Tymon\JWTAuth\Exceptions\JWTException;
use App\Http\Requests\RegistrationFormRequest;
class APIController extends Controller
{
    /**
     * @var bool
     */
    public $loginAfterSignUp = true;

    /**
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     */
public function login(Request $request)
    {
        $input = $request->only('email', 'password');
        $token = null;

        if (!$token = JWTAuth::attempt($input)) {
            return response()->json([
                'success' => false,
                'message' => 'Invalid Email or Password',
            ], 401);
        }

        return response()->json([
            'success' => true,
            'token' => $token,
        ]);
    }

    /**
     * @param Request $request
     * @return \Illuminate\Http\JsonResponse
     * @throws \Illuminate\Validation\ValidationException
     */
    public function logout(Request $request)
    {
        $this->validate($request, [
            'token' => 'required'
        ]);

        try {
            JWTAuth::invalidate($request->token);

            return response()->json([
                'success' => true,
                'message' => 'User logged out successfully'
            ]);
        } catch (JWTException $exception) {
            return response()->json([
                'success' => false,
                'message' => 'Sorry, the user cannot be logged out'
            ], 500);
        }
    }

    /**
     * @param RegistrationFormRequest $request
     * @return \Illuminate\Http\JsonResponse
     */
    public function register(RegistrationFormRequest $request)
    {
        $user = new User();
        $user->name = $request->name;
        $user->email = $request->email;
        $user->password = bcrypt($request->password);
        $user->save();

        if ($this->loginAfterSignUp) {
            return $this->login($request);
        }

        return response()->json([
            'success'   =>  true,
            'data'      =>  $user
        ], 200);
    }
}
```

## Authentication Quick start
```bash
composer require laravel/ui --dev
```
