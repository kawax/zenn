---
title: "サーバーサイドWebフレームワークでのフロントの扱いを調査した"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Laravel"]
published: true
---

普段Laravel(PHP)しか使わないのでどれもこんなものだろうと思ってたけど他の言語も含めていろいろなWebアプリケーションフレームワークを調べたらLaravelユーザーから見ると信じられない世界が広がっていた…。

## 前提
フレームワークでの標準的な使い方の話。
独自に導入する方法は関係ない。

## Ruby on Rails
SprocketsからWebpackerに変わったもののRubyに寄せる方針は変わってないように見える。
フロントが得意な人ならWebpackerも外して使っている。

## Symfony / PHP
LaravelやRailsのように独自のwebpackラッパーを用意。
https://symfony.com/doc/current/frontend.html

> Encore is inspired by Webpacker and Mix

## CakePHP
モダンフロントの話はない。

## Django / Python
ドキュメントにモダンフロントの話はない。
jsやcssはstaticに置いて利用する。
https://docs.djangoproject.com/en/3.1/howto/static-files/

実際npm使わずにjquery.min.jsやvue.min.jsを直接プロジェクト内に置いてるDjangoプロジェクトは存在した。
初心者が練習用に作ったプロジェクトではなく有名企業がOSSとして誇らしげに公開してるプロジェクトなのでこれがDjangoでの標準なんだろう。

## Spring / Java
なし。
AngularでもjQueryでもpublic以下に書いてる。
https://spring.io/guides/gs/consuming-rest-angularjs/
https://spring.io/guides/gs/consuming-rest-jquery/

## Play / Scala
アセットコンパイラがある。でも微妙な古さ。
https://www.playframework.com/documentation/2.8.x/Assets

## RevelやBeegoやGin / Go
なし。

PHP以外はstaticで静的ファイルの配信機能みたいな方向しかない。
WebサーバーとセットなPHPとその他言語の基本的な方針の違いみたいなものが見える。
フロントのことはWebサーバー側でやってくれ。
https://revel.github.io/modules/static.html

## Mojolicious他 / Perl
なし。

## AdonisJs / node.js
自分でwebpackをセットアップしようって方針。
https://adonisjs.com/recipes/4.1/frontend-assets

## Sails / node.js
アセットパイプライン。
https://sailsjs.com/documentation/concepts/assets

npmはサーバーサイドで使うからフロントには使わない？

## NestJS / node.js
静的に提供
https://docs.nestjs.com/recipes/serve-static
> In order to serve static content like a Single Page Application (SPA)

新しめのフレーワークだとさすがにnode.js使ってる人なら分かってる想定でのなし。
CORSの対応もあるし普通は分離。

## 終わり
これ以上探してもなさそうなので調査終了。

「サーバーサイドフレームワークなのでフロントのことなんか知らねーよ」って結論しかなかった…。
ここにモダンフロントを導入しようとするとDjangoみたいな例か、独自な方法か、完全に分離か、となるのも仕方ない。
分かってる人なら正しく導入できるけど現実的にはそんな人ばかりじゃないことは分かっている。
標準的な導入方法がないから変な使い方している。
ベテランでもフロントの知識が完全に抜けてる人は多いけどサーバーサイドだけずっとやってた結果か。
今の「Web」に関わるならサーバーサイドに加えて最低限のフロントの知識は必須。

（本来はサーバー／クライアント、バックエンド／フロントエンドなのにサーバーサイド／フロントエンドの組み合わせが普及してるのも両方詳しい人が少ないせい？）