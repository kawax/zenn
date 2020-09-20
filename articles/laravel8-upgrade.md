---
title: "Laravel8へアップグレード"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Laravel"]
published: true
---

## はじめに
Laravel8は「基本的な使い方」部分であちこち変わってるので見た目以上に変化が大きい。
旧バージョンの情報が使えなくなってるのでこれから始める初心者は大変だけど、ベテランも古い常識は忘れて新しい常識を覚え直す必要がある。
その新しい常識がまだ定まってないのでしばらく待つしかないんだけど。

## いつもの読むところ
- https://laravel.com/docs/8.x/releases
- https://laravel.com/docs/8.x/upgrade
- https://readouble.com/laravel/8.x/ja/releases.html
- https://readouble.com/laravel/8.x/ja/upgrade.html
- https://github.com/laravel/laravel/compare/7.x...master

その他のドキュメント全部やJetstreamも。
https://jetstream.laravel.com/

プロジェクト側の修正のためにzipでダウンロードしておく。
https://github.com/laravel/laravel

## Jetstream
https://github.com/laravel/jetstream

Laravel8の一番大きい新機能。5.8までの`make:auth`、6以降の`laravel/ui`を置き換える新しいデフォルトプロジェクト。
今後は認証機能を含む新規プロジェクトを作る時はJetstream付きで作ることになる。
新規用なので旧バージョンからアップグレードしたプロジェクトでは関係ない。以前の認証機能をそのまま使っていい。

Jetstreamの前にLivewireやInertiaをよく調べたほうがいい。
https://laravel-livewire.com/
https://inertiajs.com/

Laravel8+Jetstreamのプロジェクト作成方法。
まずlaravel/installer 4.0以上に更新。

```
composer global require laravel/installer
```

`--jet`オプションでJetstreamを含むプロジェクトが作られる。

```
laravel new my-project --jet
```

LivewireやInertiaの使用は必須ではない。
なるべく今まで通りのBladeがいいならLivewire版で作ってLivewireの機能を無視すればいい。

それでもJetstreamは若干過剰なのでSocialiteでのログインしか使わないような場合はJetstreamなしでもいいかもしれない。
Jetstreamの使い方もそのうち落ち着くだろう。まだ待ち。

https://kawax.biz/laravel8-jetstream/

## ルーティング
影響の大きい変更1。

Laravel8新規なら最初から新しいルーティングの書き方する、アップグレードしたプロジェクトではRouteServiceProviderを変更しなければ何も変わらない、という想定で影響を甘く見てそうだけど旧バージョン用の情報見ながらLaravel8でそのまま真似しても動かないので初心者ほど混乱している。ルーティングなんて一番基本部分が変わったら影響は大きい。

https://kawax.biz/laravel8-routing/

## モデルファクトリー
影響の大きい変更2。

`factory()`が削除されて`User::factory()`のような使い方に変更。
地道に全部書き換えるしかない。
面倒なら`laravel/legacy-factories`をインストールすれば変更なし。
https://github.com/laravel/legacy-factories

## app/Models

- M : Eloquentモデルの置き場所として`app/Models`が復活。
- V : LivewireやInertiaは使わなくてもいいけどLaravel7で作り直されたBladeコンポーネントの理解はほぼ必須。
- C : コントローラーに繋がるルーティングが変更。

MVC全部変わってるので厄介。

## laravel/ui 3.0
Laravel8以降の新規はJetstreamを使うけど、laravel/uiで作った既存プロジェクトは今後もuiを使い続ける。
Laravel8用にui3.0へ更新。
uiはcomposer.jsonから消しても良さそうだけど`Auth::routes()`のためにuiが必須なので消せない。
つまりLaravel9以降もuiの対応が必要。

ややこしいけど
- Laravel6とui1.x : `Auth::routes()`はフレームワーク側で定義されてるのでcomposer.jsonにuiがなくてもいい。
- Laravel7とui2.x : composer.jsonにuiが必要。

Laravel7の頃に「uiはrequire-devでいいのでは？」と思ってたけど違った。requireで本番でのインストールも必須。
Laravel6ならrequire-devでもいい。実際6のリリースノートには`--dev`でインストールするように書いてある。

最近はほとんどSocialiteだったり、LTSの6で止めてたのでこの変更に気付いてなかった。
6から7への更新時にuiを追加しないと`Auth::routes()`でエラー。

## ページネーション
デフォルトがTailwindに変わったのでBootstrapを使いたいならAppServiceProviderで指定。

```php
use Illuminate\Pagination\Paginator;

public function boot()
{
    Paginator::useBootstrap();
}
```

これページネーションだけの話ではなく。
JetstreamはTailwindなので慣れ親しんだBootstrapはLaravelからほぼ消えた。
自分でインストールしないと使えない。
CSSが変わったのも使い方が大きく変わったように感じる原因。

## プロジェクト側
こっちも地味に面倒。zipでダウンロードして準備しておいたほうが早い。

### public/index.php
ファイルごと入れ替え。

### app/Exceptions/Handler.php
元から変更してないならこれも入れ替え。

### いや面倒すぎるので省略
差分見ながら書き換えるしかない。

## 影響のなかった変更
`illuminate/collections`と`illuminate/macroable`に分離。
分離しただけで名前空間は変更なしなので影響はほとんどない。

開発途中では名前空間も変わっていたのでこれで戻されなければ大惨事になっていた…。
https://github.com/laravel/framework/pull/33108

### パッケージ開発者向け
`illuminate/support`にcollectionsもmacroableも含まれてるので今まで通り`"illuminate/support": "^6.0||^7.0||^8.0",`でいい。

## 終わり
とにかくJetstreamでなにもかも変わってしまったので「Laravelの普通の使い方」をまた模索していくことに。
