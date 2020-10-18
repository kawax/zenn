---
title: "LINE SDK for Laravelを作った"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Laravel", "PHP", "LINE"]
published: true
---

https://github.com/kawax/laravel-line-sdk

Laravelユーザーなら分かるだろうけど最近各社が公式として出してる「PHP用SDK」が死ぬほど使いにくい。
LINEはまだマシというか単に公開時期相応の古さなだけ。
LINEみたいなパターンではなく2020年に新しく出したSDKなのに10年前レベルのコードな事例が多すぎる。
「公式」を名乗って世に出せるレベルじゃない。
ラッパーライブラリを作らないと使い物にならない。

PHPはこの10年でJavaScriptの次くらいに大きく変わったけどその変化について行けてない人が多い。
個別では好きなように使えばいいけど「公式SDK」なら現代のPHPに合わせてもらわないと困る。

LINE SDK for Laravelは自分で使うために作っただけなので日本語での使い方記事はそのうち。
Laravelのイベントシステムと連携したらあまりにも自然なLaravelらしい開発体験になったのでLaravelユーザーならすぐ使える。