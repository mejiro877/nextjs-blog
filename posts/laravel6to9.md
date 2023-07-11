---
title: "Laravel 6から Laravel 9にアップデートする際にやったこと"
date: "2023-04-24"
thumbnail: "/images/thumbnail03.jpeg"
---

個人開発でちょっとした Laravel の Web アプリを作ったことがあって、それをリニューアルしたいと思ってバージョンをあげました。Laravel 6 は既にサポートが終了していたので、選択肢としては 9 と 10 があったのですが、アップデート手順の紹介がされているサイトの多くが 6 から 9 へのアップデートに合わせました。
※おそらく同じような手順で 10 へのアップデートもできると思います。

## まずは PHP バージョンのアップデート

自分の PC は Windows で手早く修正したかったので、PHP は XAMP で用意しました。
前に入っていた XAMP が古かったので、最新版をダウンロードしてインストールを実施しました。
XAMP は Apache も含んでいるため、htdoc 配下にプロジェクトファイルを置いて動作確認しました。

## Composer のアップデート

Laravel をアップデートする前にまずはパッケージマネージャを新しくする必要があるので、
Composer をアンイストールし、最新版の Composer をインストールしました。

## composer.json の書き換え

Laravel のバージョン変更によって、依存するライブラリ群のバージョンも合わせる必要があるので、
自分の場合は、下記のように composer.json を書き換えました。
※ここは人によって入っているライブラリが異なるのでここにないものが入っている方は各自で調べてください。

変更前

```json
    "require": {
        "php": "^7.2",
        "barryvdh/laravel-debugbar": "^3.6",
        "fideloper/proxy": "^4.0",
        "laravel/framework": "^6.0",
        "laravel/tinker": "^1.0"
    },
    "require-dev": {
        "facade/ignition": "^1.4",
        "fzaninotto/faker": "^1.4",
        "laravel/ui": "1.0",
        "mockery/mockery": "^1.0",
        "nunomaduro/collision": "^3.0",
        "phpunit/phpunit": "^8.0"
    },
```

変更後

```json
    "require": {
        "php": "^8.2",
        "barryvdh/laravel-debugbar": "^3.6",
        "fideloper/proxy": "^4.0",
        "laravel/framework": "^9.0",
        "laravel/tinker": "^2.7",
        "laravel/ui": "^4.0"
    },
    "require-dev": {
        "spatie/laravel-ignition": "^1.0",
        "fakerphp/faker": "^1.9.1",
        "mockery/mockery": "^1.4.4",
        "nunomaduro/collision": "^6.1",
        "phpunit/phpunit": "^9.5.10"
    },
```

## ソースの修正

使っていたライブラリや書き方が変わってしまったところがあるので、エラーが出ているソースを順次直していきました。

自分の例をあげておきます。

### Exceptions\Handler.php

・use Exception;　を　 use Throwable;　に変えて、Exception と記述のあるところを Throwable に変更
・use Illuminate\Validation\ValidationException;　の use 文を追加

### Controller.php

・use Throwables;が必要なところを追加

### Middleware\TrustProxies.php

・headers の変数定義を次のように変更

変更前

```
    protected $headers = Request::HEADER_X_FORWARDED_ALL;
```

変更後

```
    protected $headers =
        Request::HEADER_X_FORWARDED_FOR |
        Request::HEADER_X_FORWARDED_HOST |
        Request::HEADER_X_FORWARDED_PORT |
        Request::HEADER_X_FORWARDED_PROTO |
        Request::HEADER_X_FORWARDED_AWS_ELB;
```

## まとめ

Laravel のアップデートは使ってるライブラリによって、修正手順が大きく異なるので、一概にこの方法で上手くいくとは言えません。ただやり方の流れ自体は変わらないと思います。

1. PHP や Composer のアップデート
1. composer.json の依存関係を考慮したライブラリのアップデート
1. 各ソースでエラーが出ている箇所をエラー名で検索して解決策を探す

モノによっては解決策が海外のサイトにしか載ってないってことがザラにあるので、
英語だからといって敬遠せずにちゃんと内容を見て該当するか確認しましょう。

※最近のブラウザの翻訳機能は精度が良いので翻訳すればだいたいわかると思います。
　あとは ChatGPT で聞いてみるというのも一つの方法かもしれません。
