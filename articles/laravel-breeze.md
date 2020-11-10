---
title: "Laravel Breeze 新たなスターターキットが登場"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Laravel"]
published: true
---

これは初心者が混乱する…。

Laravel8.x時点の情報を再度まとめる。もちろん将来のバージョンではまた変わっている可能性が高い。

## 先に大事なこと
これらは新規プロジェクト作成時の話。既存プロジェクトでは関係ない。

## Jetstream（松）
Livewire / Inertiaサポートや二要素認証を含む全部入り。
https://github.com/laravel/jetstream
初心者がいきなり使うには過剰すぎる。

## Breeze（竹）
Jetstreamまではいらないという意見が多かったのか今日新しくBreezeが公開された。
こっちはシンプルなログイン機能のみ。必要なファイルは全部プロジェクト側にコピーされるので好きなようにカスタマイズできる。
https://github.com/laravel/breeze
普及するかどうかは分からないけどこれで十分な人が多そう。

```
laravel new my-app

cd my-app

composer require laravel/breeze --dev

php artisan breeze:install
```
全部コピーするので`--dev`でいい。インストール後はcomposer.jsonから削除してもいい。ui:1.xの頃と同じ。

## ログイン機能なし（梅）
素のLaravelプロジェクトで作ってログイン機能を追加しない。
Socialiteでのログインだけ使うなら不要。
勧めないけど自分でログイン機能を作る場合も不要。

覚えるのはここまででいい。

## fortify
Breezeが公開されたのでfortify+カスタムviewでの使い方は忘れて良さそう。
viewの変更はBreezeでもできる。

## laravel/ui
uiも使う理由はなくなったけど…Jetstreamで一番壁を感じたのはTailwindなのでbootstrapやVue.jsのセットアップまでできてる状態がいい人もいるかもしれない。
最初だけ必要な作業なので諦めてBreezeで作った後bootstrapやVue.jsを使うように自分でセットアップ。

## uiからbreezeへの移行
「uiからfortify」よりは簡単なはずなので既存プロジェクトでuiを使い続けたくない場合は移行もあり。

## おわり
選択肢が複数あると「これが標準」と言えなくなるので困る。
