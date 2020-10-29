---
title: "LaravelでLINEボットを作る クイックスタート版"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Laravel", "LINE"]
published: true
---

## 環境
- Laravel 8.x
- LINE SDK for Laravel 1.x
- PHP 7.4

いつもの：Laravelはバージョンアップで容赦なく変わるのでバージョンが違う情報を信用してはいけない。

## 作るボット
ユーザーからのメッセージをそのまま返すだけのボット。

## 準備
LINE Developers consoleで`Messaging API`のチャンネルを作る。
https://developers.line.biz/
`Channel access token (long-lived)`と`Channel secret`とQRコードを取得。

 LINEからのWebhookを受信するためのWebサーバー。普通のローカルサーバーでは不可能なので外部からアクセスできる実際のサーバーを用意。httpsも必須。

 ## Laravelプロジェクト作成
ここではインストーラーではなくcreate-projectでLaravel8を指定。
```
composer create-project --prefer-dist laravel/laravel line-bot "8.*"
cd ./line-bot
composer require revolution/laravel-line-sdk
```

`.env`を編集して`Channel access token (long-lived)`と`Channel secret`を書く。

```
LINE_BOT_CHANNEL_TOKEN=
LINE_BOT_CHANNEL_SECRET=
```

`app/Providers/EventServiceProvider`でイベントディスカバリを有効化。
このままコピペすればいい。
忘れそうな重要な設定はこれ。

```php
/**
 * Determine if events and listeners should be automatically discovered.
 *
 * @return bool
 */
public function shouldDiscoverEvents()
{
    return true;
}
```

デフォルトのイベントリスナーを公開。メッセージのみの需要が高そうなので用意している。
```
php artisan vendor:publish --tag=line-listeners-message
```
全部入りならall
```
php artisan vendor:publish --tag=line-listeners-all
```
プロジェクト側の`app/Listeners`に公開される。
app側に公開されたファイルは編集もリネームも移動も削除も自由。

メッセージを返すだけのボットは`TextMessageListener`で実装済なのでもう終了。

## サーバーにデプロイ
ローカルでは動かせないのでサーバーにデプロイ。Laravelユーザーならさくっとデプロイできるサーバーくらい持ってるだろう。

LINE Developers consoleで`Webhook URL`を設定。
各自のURLに合わせて`https://example.com/line/webhook`のようなURL。
Webhookの検証もできる。これでエラーならサーバー側のどこかを間違えている。

### ローカルサーバーを使う方法も一応ある
ngrokやexposeを使えばローカルでWebhookの受信が可能。
https://beyondco.de/docs/expose/introduction

## ボットを追加
QRコードを使って手動で追加。

## テストメッセージを送信
ボットからはそのまま返ってくる。

クイックスタート版ではここまで。

## 発展1
現実的にはこれだけのボットに意味はないのでユーザーからのメッセージを元にいろいろ処理したり返信メッセージを作ったりもっと複雑になる。

Laravelのイベントシステムを使っているのでLaravelユーザーならいつも通りの普通の使い方ができる。
Webhookを受信してイベントの発行まではライブラリ内で済んでいるのユーザー側は
TextMessageイベントを受信したらTextMessageListenerが実行されることだけ分かっていればいい。

```php
<?php

namespace App\Listeners\Message;

use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use LINE\LINEBot\Event\MessageEvent\TextMessage;
use Revolution\Line\Facades\Bot;

class TextMessageListener
{
    /**
     * Create the event listener.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Handle the event.
     *
     * @param  TextMessage  $event
     * @return void
     */
    public function handle(TextMessage $event)
    {
        $response = Bot::replyText($event->getReplyToken(), $event->getText());

        if (! $response->isSucceeded()) {
            logger()->error(static::class.$response->getHTTPStatus(), $response->getJSONDecodedBody());
        }
    }
}
```

複雑な返信メッセージはMessageBuilderを使って作る。
公式SDKの使い方の範囲なのでここでは書かない。
```php
    public function handle(StickerMessage $event)
    {
        $packageId = $event->getPackageId();
        $stickerId = $event->getStickerId();

        Bot::replyMessage($event->getReplyToken(), new StickerMessageBuilder($packageId, $stickerId));
    }
```

## 発展2
LINE Login時にボットを追加するためにSocialiteドライバーも用意している。

## 発展3
MessagingのPushMessageは制限があるのでLINE Notify x Laravel Notificationsも用意。
既存プロジェクトへはこのLINE Notifyで通知がたぶん一番導入しやすい。

自分宛に通知ならPersonal access token使うだけ。

## 終わり
後はサンプルプロジェクトとかを参考に。
- https://github.com/kawax/laravel-line-sdk
- https://github.com/kawax/laravel-line-project

Laravelの技を駆使していくらでも拡張できるようにしているので好きなように使える。
