---
title: Laravel 使用 Socialite Providers 实现第三方授权登录
date: 2019-10-30 16:33:37
categories: Laravel
---

> [Socialite Providers](https://socialiteproviders.netlify.com/)、[OAuth 2.0 - 阮一峰](http://www.ruanyifeng.com/blog/2019/04/oauth_design.html)

# 微信

>[socialiteproviders/weixin](https://socialiteproviders.netlify.com/providers/weixin.html)、[微信官方文档](https://developers.weixin.qq.com/doc/oplatform/Website_App/WeChat_Login/Wechat_Login.html)

## 安装配置

```bash
$ composer require socialiteproviders/weixin
```

*app/Providers/EventServiceProvider.php*

```php
protected $listen = [
    \SocialiteProviders\Manager\SocialiteWasCalled::class => [
        'SocialiteProviders\\Weixin\\WeixinExtendSocialite@handle',
    ],
];
```

*.env*

```env
WEIXIN_KEY=
WEIXIN_SECRET=
```

*config/services.php*

```php
'weixin' => [
    'client_id' => env('WEIXIN_KEY'),
    'client_secret' => env('WEIXIN_SECRET'),
    'redirect' => env('WEIXIN_REDIRECT_URI')
],
```

## 功能开发

**添加 `weixin_openid` 与 `weixin_unionid` 字段**

```bash
$ php artisan make:migration add_weixin_openid_to_users_table --table=users
```

*databases/migrations/add_weixin_openid_to_users_table.php*

```php
public function up()
{
    Schema::table('users', function (Blueprint $table) {
        $table->string('weixin_openid')->unique()->nullable()->after('password');
        $table->string('weixin_unionid')->unique()->nullable()->after('weixin_unionid');
        $table->string('password')->nullable()->change();
    });
}

public function down()
{
    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('weixin_openid');
        $table->dropColumn('weixin_unionid');
        $table->string('password')->nullable(false)->change();
    });
}
```

```bash
$ php artisan migrate
```

*app\Models\User*

```php
protected $fillable = [
    'weixin_openid', 'weixin_unionid' // 新增
];
```

**修改路由**

*routes/api.php*

```php
Route::prefix('v1')->namespace('Api')->name('api.v1.')->group(function () {
    Route::middleware('throttle:' . config('api.rate_limits.sign'))
        ->group(function() {
            // 第三方授权登录
            Route::post('socials/{social_type}/authorizations', 'AuthorizationController@socialStore')
                // 如果增加了其他第三方的登录 ->where('social_type', 'weixin|weibo')
                ->where('social_type', 'weixin')
                ->name('socials.authorizations.store');
        });
});
```

**业务控制器**

```bash
$ php artisan make:controller Api/AuthorizationController
```

*app/Http/Controllers/Api/AuthorizationController.php*

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Http\Requests\Api\SocialAuthorizationRequest;
use App\Models\User;
use Illuminate\Auth\AuthenticationException;

class AuthorizationController extends Controller
{

    public function socialStore($socialType, SocialAuthorizationRequest $request)
    {
        // 设置驱动
        $driver = \Socialite::driver($socialType);

        try {
            // 推荐：客户端只获取授权码，获取到授权码后就提交给服务器，服务器完成换取 access_token 及换取用户信息的流程。
            if ($code = $request->code) {
                // 服务器通过 code 换取用户数据 且如果是微信则会自动设置 openid
                $response = $driver->getAccessTokenResponse($code);
                $token = $response['access_token'];
            } else {
            // 客户端通过 access_token 换取用户数据
                $token = $request->access_token;

                // 如果是微信则会需要设置 openid
                if ($socialType === 'weixin') {
                    $driver->setOpenId($request->openid);
                }
            }

            // 通过 access_token 换取用户数据
            $oauthUser = $driver->userFromToken($token);
        } catch (\Exception $e) {
            throw new AuthenticationException('参数错误，未获取用户信息');
        }

        // 已成功拉取用户信息，处理登录及新用户注册相关
        switch ($socialType) {
            case 'weixin':

                // 判断拉取的数据是否存在 unionid
                // 只有在用户将公众号绑定到微信开放平台帐号后，才会出现 unionid 字段
                // 但是由于微信开放平台只有通过认证才能绑定公众号，代码做了兼容处理
                $unionid = $oauthUser->offsetExists('unionid') ? $oauthUser->offsetGet('unionid') : null;

                if ($unionid) {
                    // 存在 unionid 就通过这个获取
                    $user = User::where('weixin_unionid', $unionid)->first();
                } else {
                    // 不存在 unionid 则通过 openid 获取
                    $user = User::where('weixin_openid', $oauthUser->getId())->first();
                }

                // 如果用户不存在 则新创键一个
                if (!$user) {
                    $user = User::create([
                        'name' => $oauthUser->getNickname(),
                        'avatar' => $oauthUser->getAvatar(),
                        'weixin_openid' => $oauthUser->getid(),
                        'weixin_unionid' => $unionid
                    ]);
                }
                break;
        }

        return response()->json(['token' => $user->id]);
    }
}
```

**表单验证**

```bash
$ php artisan make:request Api/SocialAuthorizationRequest
```

*app/Http/Requests/Api/SocialAuthorizationRequest.php*

```php
<?php

namespace App\Http\Requests\Api;


class SocialAuthorizationRequest extends FormRequest
{
    public function rules()
    {
        $rules = [
            // code 存在：通过 appID 、 appsecret 、code 换取 access_token
            'code' => 'required_without:access_token|string',
            // access_token 存在：通过 access_token 换取用户数据
            'access_token' => 'required_without:code|string',
        ];

        // 如果使用的是微信授权，则需要添加 openid 字段
        if ($this->social_type === 'weixin' && !$this->code) {
            $rules['openid'] = 'required|string';
        }

        return $rules;
    }
}
```



