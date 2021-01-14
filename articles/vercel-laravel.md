---
title: "VercelでLaravelを動かす"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Laravel", "Vercel"]
published: true
---

https://vercel.com/

## 最初にまとめ
「データベースを使わない規模のちょっとしたLaravelアプリを無料で動かして公開するならVercelはちょうどいい」
なんだかんだで動かせるのがPHPのメリット。

本格的に使うなら「Laravel公式のサーバーレス」Vaporを使ったほうがいい。
https://vapor.laravel.com/


残りはここに書いてることとほとんど同じ。  
https://github.com/invokable/laravel-vercel


## 動かし方
必要なファイルは`vercel.json`と`api/index.php`

これを参考にすればいいけど`vercel.json`が少し違う。  
https://github.com/juicyfx/vercel-examples/tree/master/php-laravel

動くVIEW_COMPILED_PATHはこれ。
```
"VIEW_COMPILED_PATH": "/tmp"
```

東京リージョンで使うには
```
"regions": ["hnd1"],
```

## Databases
https://vercel.com/docs/solutions/databases

AWSのRDSなどが使える。

## Authentication
https://vercel.com/docs/solutions/authentication

DBが使えるならLaravelの認証が使えるはず。

## Realtime
https://vercel.com/docs/solutions/realtime

Pusherが使えそうだけどVercelで動かすLaravelではやらないことかも。

## Cron
https://vercel.com/docs/solutions/cron-jobs

VercelのドキュメントではGitHub Actionsで定期的にリクエストを送るとか書いてるけど
GitHub Actions使うならGitHub Actionsでartisanコマンドを実行すればいいだけなのでLaravelでは関係ない話。

## Email
https://vercel.com/docs/solutions/email
SESなどが使える。

## File Storage & Uploads
https://vercel.com/docs/solutions/file-storage
S3などが使えるはず。

Vercelで難しそうなのはキューワーカーやHorizonをずっと動かし続ける部分？  
個人的にはそこが一番重要。

## デプロイ時のartisanコマンド
composer scriptsに`vercel`を作ればデプロイ時に実行されるのでキャッシュやマイグレーションはこれでできそう。

```
    "scripts": {
        "vercel": [
            "@php artisan config:cache",
            "@php artisan route:cache",
            "@php artisan view:cache"
        ]
    }
```

## セッション
databaseかcookieを使う。

Livewireを使うならセッションが必須。DBなしならcookieを使えばいい。