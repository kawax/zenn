---
title: "LaravelプロジェクトのVue.jsを2から3へバージョンアップ"
emoji: "😷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Laravel", "Vue"]
published: true
---

## 対象
Laravel7以前に`make:auth`や`laravel/ui`で作ったプロジェクト。  
`resources/js/app.js`はこうなっている。  
https://github.com/laravel/ui/blob/2.x/src/Presets/vue-stubs/app.js  
Vue2とbootstrap4とjQuery。Vueだけ3に上げる。

Laravel8以降の新規プロジェクトでuiを使うことはないので対象外。

Laravelは8にしたけどVueのバージョンアップができてないプロジェクトが対象。かなり多いはず。

## 事前準備
Vue3のドキュメントを読む。
https://v3.ja.vuejs.org/guide/introduction.html

Vue3に対応してないnpmパッケージに依存した機能を諦めて切り捨てる覚悟を決める。  
おそらく対応を待ってもどうしようもないので削除か、代わりのパッケージを探すか、簡単なら機能ならLivewireで作り直す。

UIコンポーネントみたいな全面的に依存していて代わりがない場合はVue3へのバージョンアップはまだ無理と諦める。

## package.json
Laravel mix 6やVue3などとりあえずpackage.jsonだけ変えて`npm update`, `npm run dev`してみる。  
この段階ではビルドは失敗するはず。updateさえ失敗するならバージョンが合ってないのでupdateできるまで合わせる（記事を書いた日から時間が経てば当然変わる）

```json
{
  "private": true,
  "scripts": {
    "dev": "npm run development",
    "development": "mix",
    "watch": "mix watch",
    "watch-poll": "mix watch -- --watch-options-poll=1000",
    "hot": "mix watch --hot",
    "prod": "npm run production",
    "production": "mix --production"
  },
  "devDependencies": {
    "@vue/compiler-sfc": "^3.0.7",
    "axios": "^0.21.1",
    "bootstrap": "^4.1.0",
    "cross-env": "^7.0",
    "jquery": "^3.4.0",
    "laravel-mix": "^6.0.0",
    "lodash": "^4.17.4",
    "popper.js": "^1.14",
    "resolve-url-loader": "^3.1.2",
    "sass": "^1.15.2",
    "sass-loader": "^8.0",
    "vue": "^3.0",
    "vue-loader": "^16.0.0",
    "vue-template-compiler": "^2.6.12"
  }
}
```

## resources/js/app.js
Vue3対応に一番重要なのはapp.jsだけ。

元と同じapp.jsのVue3版。
```js
require("./bootstrap");

import { createApp } from 'vue'
import ExampleComponent from './components/ExampleComponent'

createApp({
    components: {
        'example-component': ExampleComponent,
    },
}).mount('#app')
```
ここにVueコンポーネントを追加すればいい。

## webpack.mix.js
Laravel mix 6から`vue()`が必要になっている。

```js
mix.js('resources/js/app.js', 'public/js').
    vue().
    sass('resources/sass/app.scss', 'public/css').
    extract().
    version()
```

## 各Vueコンポーネント
完全にVue側の話なのでLaravelとは関係ない。  
Vue 2からのマイグレーションを読んで必要なら変更。  
https://v3.ja.vuejs.org/guide/migration/introduction.html
普通に使っていれば変更箇所はほとんどないはず。  
Composition APIとかは別に無視していい。