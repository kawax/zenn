---
title: "Laravel8 laravel/uiからlaravel/fortifyに移行は可能"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Laravel"]
published: true
---

2020-09-22

旧バージョンからLaravel8にアップグレードしたプロジェクトが想定。
fortifyに移行できれば非推奨になったuiを削除できる。

## Fortify
フロントエンドにとらわれないバックエンド認証機能。
https://github.com/laravel/fortify
uiのauth-backendやstubs/Auth辺りに相当。
https://github.com/laravel/ui

フロントは自分で実装する必要がある。
Livewire or Inertiaで実装してるのがJetstream。

この記事でやろうとしてることは、uiのviewをフロントとして使う。

## プロジェクトの準備
Laravel8で一から作る。

```
laravel new laravel8-ui-to-fortify
cd laravel8-ui-to-fortify
composer require laravel/ui
php artisan ui vue --auth
npm i
npm run prod
php artisan migrate
```

DBはSQLiteを使うとか細かい所は省略。

これでLaravel8のwelcomeページとuiのログイン画面を持ったプロジェクトができあがる。
`login`や`register`ルートは存在するのでLaravel8のwelcomeページでも問題なく動く。
ユーザー登録しておく。登録後はログアウト。

## laravel/ui アンインストール
routes/web.phpの`Auth::routes();`とcomposer.jsonからlaravel/uiを削除。

## laravel/fortify インストール

```
composer require laravel/fortify
php artisan vendor:publish --provider="Laravel\Fortify\FortifyServiceProvider"
php artisan migrate
```

ここまででログインしようとしても`Target [Laravel\Fortify\Contracts\LoginViewResponse] is not instantiable.`のエラー。
解決方法は
`config/app.php`に`App\Providers\FortifyServiceProvider::class,`追加。
そのFortifyServiceProviderに`Fortify::viewPrefix('auth.');`追加。
Jetstreamでインストールすると`Fortify::viewPrefix()`はJetstream内で実行されてるのでなかなか原因が分からなかった。
https://github.com/laravel/jetstream/blob/3ac0a23814c643b1e4de8168423e5be784081261/src/JetstreamServiceProvider.php#L69

Fortifyにviewは含まないのでどういうviewを使うかは利用する側で指定。これの理解が一番重要だった。

この時点でユーザー登録とログインとhome表示とログアウトは可能。意外とスムーズ。

## Fortify::viewPrefix('auth.')の処理を追う
https://github.com/laravel/fortify/blob/96e6463117a4b11ce49baba11088ece6ffd96d13/src/Fortify.php#L85
`loginView('auth.login')`へ。
```php
    public static function viewPrefix(string $prefix)
    {
        static::loginView($prefix.'login');
        static::twoFactorChallengeView($prefix.'two-factor-challenge');
        static::registerView($prefix.'register');
        static::requestPasswordResetLinkView($prefix.'forgot-password');
        static::resetPasswordView($prefix.'reset-password');
        static::verifyEmailView($prefix.'verify-email');
        static::confirmPasswordView($prefix.'confirm-password');
    }
```
https://github.com/laravel/fortify/blob/96e6463117a4b11ce49baba11088ece6ffd96d13/src/Fortify.php#L102
サービスコンテナへ登録。LoginViewResponseだったらSimpleViewResponseを返す。
```php
    public static function loginView($view)
    {
        app()->singleton(LoginViewResponse::class, function () use ($view) {
            return new SimpleViewResponse($view);
        });
    }
```
`SimpleViewResponse`はここ。
https://github.com/laravel/fortify/blob/96e6463117a4b11ce49baba11088ece6ffd96d13/src/Http/Responses/SimpleViewResponse.php

```php
    public function toResponse($request)
    {
        return is_callable($this->view) && ! is_string($this->view)
                    ? call_user_func($this->view, $request)
                    : view($this->view, ['request' => $request]);
    }
```
最終的には見慣れた`view()`に辿り着く。
`view('auth.login')`を表示してるだけなのでuiと同じと分かる。
https://github.com/laravel/ui/blob/29335934ee4dee248ec128d938682a14d6ee3dc4/auth-backend/AuthenticatesUsers.php#L21
```php
    public function showLoginForm()
    {
        return view('auth.login');
    }
```

こんな回りくどいことをしてるのはカスタマイズできるように。
ログインは動くけどパスワードリセットは動かないのでFortifyServiceProvider@bootでviewの指定。
```php
        Fortify::viewPrefix('auth.');
        Fortify::requestPasswordResetLinkView('auth.passwords.email');
        Fortify::resetPasswordView('auth.passwords.reset');
        Fortify::confirmPasswordView('auth.passwords.confirm');
        Fortify::verifyEmailView('auth.verify');
```
逆にviewのファイル名を変更してもいい。

これでlaravel/uiの機能をlaravel/fortifyに置き換えられた。

今回はuiのviewを使ったけど完全に独自のviewを使う場合も同じ。
Fortify単体+独自viewでいろいろ作って欲しいのがLaravel開発者の想定？
JetstreamはLaravel公式が用意したview。
という所まで分かってくるとJetstreamの立ち位置が分かる。
Fortifyのためのviewを提供するcomposerパッケージも作れるな。

## 不要な機能をオフ
`config/fortify.php`で使わない機能をオフ。

```php
    'features' => [
        Features::registration(),
        Features::resetPasswords(),
        // Features::emailVerification(),
        //Features::updateProfileInformation(),
        //Features::updatePasswords(),
        //Features::twoFactorAuthentication([
        //    'confirmPassword' => true,
        //]),
    ],
```

uiにはなかったのでオフにしてるけどviewさえ用意すれば使える。

## reset.balde.php
細かい修正。
```
<input type="hidden" name="token" value="{{ $token }}">
```
↓
```
<input type="hidden" name="token" value="{{ $request->route('token') }}">
```

```
{{ $email ?? old('email') }}
```
↓
```
{{ old('email', $request->email) }}
```

他にもあるかも。

## Actions
ユーザー登録時の処理を変更したいとか細かいことは`app/Actions/Fortify/`のファイルで。

`app/Http/Controllers/Auth/`は削除していい。

## URL
パスワードリセットなどのURLが変わってるけど影響はないはず。ルートは変わってないので。

register,loginが変わってなければ問題ない。

## 終わり
uiからfortifyに移行はできる。uiを使い続けなくていいと分かって安心。

https://github.com/kawax/laravel8-ui-to-fortify