---
title: "Laravelでセルフオーダーシステムを作るためのスターターキットを作った"
emoji: "😷"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["Laravel"]
published: true
---

セルフオーダー=飲食店などでユーザーが自分のスマホで注文する仕組み。

セルフオーダーの普及が目的なので自分が使う以上に他の人が使ってセルフオーダー導入をビジネスにしていって欲しい。ってところを目指してる。

## composerパッケージ
https://github.com/kawax/self-ordering

## Laravelプロジェクトテンプレート
https://github.com/kawax/self-ordering-starter

## 実際に動いてるデモ
- QRコード表示 https://self-ordering-starter.vercel.app/
- メニュー選択 https://self-ordering-starter.vercel.app/order
- 管理画面(パスワードはsecret) https://self-ordering-starter.vercel.app/login

注文してもどこにも何も送信されないので自由に試して大丈夫。

PayPayはテストモードなので支払いは実行されない。
PCのブラウザならPayPayのページにリダイレクトするまで。
スマホならPayPayアプリが起動してエラー。
開発用アカウントでしかログインできない。

## 開発で必要なこと
「メニューデータの管理方法」
「注文情報の送信先」
「決済機能を使うかどうか」

店舗ごとに決めてカスタマイズする部分。
