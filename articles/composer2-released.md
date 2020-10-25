---
title: "Composer 2.0がリリース"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["PHP", "composer"]
published: true
---

Composer 2.0 is now available!
https://blog.packagist.com/composer-2-0-is-now-available/

## インストール方法
通常のインストール方法はすでに2。
https://getcomposer.org/download/
brewでも2。
https://formulae.brew.sh/formula/composer

インストール済composerを更新するには`--2`オプションを付ける。
```
composer self-update --2
```

Docker内では
```
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN composer self-update --2
```
もしくは2を指定。反映が遅いのでどっちにしろ更新はしたほうが良さそう。
```
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

RUN composer self-update --2
```

その他。
GitHub Actionsの`shivammathur/setup-php@v2`は2。
Travisは何もしなければまだ1。

## 速い
日本からcomposerを使うと遅すぎて使い物にならないのでプラグインやpackagistミラーで高速化してたけど2は十分速いので全部外して良さそう。
自分で作ってたミラーも終了させた。

互換も問題なさそうなのですぐに全部2に上げた。

## packagistミラー
もう不要だけどpackagistミラーは一度自分で作ってみると普段できないことができて楽しい。
今のパッケージ数は30万くらい。
https://packagist.org/statistics
パッケージ一つに対してjsonファイルが一つ。
ミラーではこれを全部ダウンロードすることになる。
許可された形でこれだけ大量のダウンロードは今の時代ではもう中々できない。

一度ダウンロードすれば後は差分だけなのでそんなに負荷は高くない。
最初はAWS S3使ってたのでS3との同期が一番負荷が高かった。
AWSのcliツールはPython製。複数プロセス使ったり普通なら問題ないけど2,30万ファイルの同期となるともっと強いサーバーが必要そうだった。
Go製のcliツールは見つけたけど非公式なので試さないままS3使うほうを止めた。
