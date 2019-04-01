# Laravel Social lite Code
--------
#### Setp 1: -
```sh
composer require laravel/socialite
```

#### Setp 2: - 

 >inside  ->  config/services.php

 
```php
   'google' => [
        'client_id' => env('GOOGLE_CLIENT_ID'),
        'client_secret' => env('GOOGLE_CLIENT_SECRET'),
        'redirect' => 'http://localhost:8000/login/google/callback',
    ],

    'github' => [
        'client_id' => env('GITHUB_CLIENT_ID'),
        'client_secret' => env('GITHUB_CLIENT_SECRET'),
        'redirect' => 'http://localhost:8000/login/github/callback',
    ],
    'facebook' => [
        'client_id' => env('FACEBOOK_CLIENT_ID'),
        'client_secret' => env('FACEBOOK_CLIENT_SECRET'),
        'redirect' => 'http://localhost:8000/login/facebook/callback',
    ],
    'twitter' => [
        'client_id' => env('TWITTER_CLIENT_ID'),
        'client_secret' => env('TWITTER_CLIENT_SECRET'),
        'redirect' => 'http://localhost:8000/login/twitter/callback',
    ],
],
```

#### Setp 3: - 

 >inside  ->  web.php

 
```php
    Route::get('login/{provider}', 'Auth\LoginController@redirectToProvider');
    Route::get('login/{provider}/callback', 'Auth\LoginController@handleProviderCallback');
],
```
#### Setp 4: - 

 >inside  ->  Auth\LoginController.php

 
```php
<?php

namespace App\Http\Controllers\Auth;


use App\User;
use Socialite;
use App\Http\Controllers\Controller;
use Illuminate\Support\Facades\Auth;
use Illuminate\Foundation\Auth\AuthenticatesUsers;

class LoginController extends Controller
{
   

    use AuthenticatesUsers;
    

    public function redirectToProvider($provider)
    {
        return Socialite::driver($provider)->redirect();
    }

    public function handleProviderCallback($provider)
    {

       
        try{
        $userSocial  = Socialite::driver($provider)->user();
        $checkUser = User::where('email',$userSocial->email)->first();
        if($checkUser){
            if($userSocial->token){
                Auth::login($checkUser, true);
            }
        }else{
            $user = new User;
            $user->name = $userSocial->name;
            $user->email = $userSocial->email;
            $user->save();
            Auth::login($user, true);
        }      
           return redirect()->route('home');
        }
        catch(Exception $e ){
            return $e;
        }
      
    }
}

],
```

## Usage

```php
<a href="{{url('login/{provider}'}}"> Provider </a>
```
>Note : provider means all the deriver which you specifies in config/services.php
