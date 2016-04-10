---
layout: post
title: "Ubuntu 13.10にfcitx-kkcをインストール"
date: 2013-11-09 20:29
permalink: /blog/fcitx-kkc-on-ubuntu-13-dot-10
---

octopressでブログ書こうと思い、
WindowsよりUnix風OSの方がなにかと楽だろうとVirtualBox 4.3.2にUbuntu入れた。

Ubuntuのインストールかなりお手軽だった。
（世間的にはそんなのあたりまえなんだろうけど）。

Linuxを使う時はDEではなくWMはdwmをつかう程度なので
GNOMEとかUnityの環境のことはよくわかってないのだけど、
標準の日本語入力関連というかIBus 1.5が不評なのは
知っていたので最初からibusはつかわずFcitxとlibkkcを入れた。
なので、ibusとくらべてどうこう言うわけではない。

今のところUbuntuの公式リポジトリにfcitx-kkcはないようなので、
手動インストールの手順を書いてみる。

## fcitx-kkcインストール手順

> こういった記事はすぐに陳腐化してしまうので「この記事、数カ月前のものだな」
とおもったら、fcitx-kkcがあるか
`apt-cache search fcitx-kkc`で確認してください。
存在すれば以下の説明は不要です。
単に`apt-get install fcitx-kkc`してください。

わかりやすいように順を追って依存パッケージなどを解決します。
(ubuntu自体とubuntuのパッケージマネージャになれていないので、
すでに入っているパッケージがあったりするかもしれません)

MARISAのpythonバインディングやlibkkc-dataのビルドにpythonを
つかいますが、3系だとlibkkc-dataのビルドができないかもなので注意してください。
Ubutu 13.10のデフォルトのpythonは2系なので問題ないはずですが念のため。

まず、fcitx-team/nightlyのPPAを追加します。

（公式のリポジトリにfcitxあるの知らずに
インストールしたのでPPA追加は不要かもしれません。
それに、あとで気づいたんですがPPA追加するなら日本語チームのを
追加した方がいいかもしれません）

``` bash
sudo add-apt-repository ppa:fcitx-team/nightly

sudo apt-get update
```


fcitxをインストールします。

``` bash
sudo apt-get install fcitx
```

libkkc-dataのためにmarisa-trieをビルドする必要があります。
次のパッケージをapt-getで入れてください。

* g++

[MARISA](http://code.google.com/p/marisa-trie)をダウンロードし、展開します。
（私がビルドしたmarisaのバージョンは0.2.4です）

MARISAのビルドは一般的なソフトウェアと同じように
MARISAのディレクトリ内でconfigureしてmakeするだけです。

``` bash
./configure --enable-popcnt --enable-sse4 --enable-sse4a
make
sudo make install
```

MARISAのpythonバインディングをビルドするために次のパッケージをいれます。

* python-dev

MARISA内のbindings/pythonに移動しビルドします。

``` bash
sudo python setup.py install
```

次に[libkkc](https://bitbucket.org/libkkc/libkkc/wiki/Home)をダウンロードして、展開します。
（私がビルドしたバージョンは0.3.1です）

libkkcをビルドするために次のパッケージをインストールします。

* gnome-common
* gobject-instrospection
* libgirepository1.0-dev
* libgee-dev
* libjson-glib-dev

configureじゃなくautogen.shします。

``` bash
./autogen.sh
make
sudo make install
```

libkkc-dataをダウンロード、展開、ビルドします。
（私がビルドしたバージョンは0.2.7です）

``` bash
./configure
make
sudo make install
```

最後にfcitx-kkcをビルドするために次のパッケージをいれます。

* skkdic
* fcitx-libs-dev
* libqt4-dev
* cmake
* git

gitでfcitx-kkcをcloneします。

``` bash
git clone git://github.com/fcitx/fcitx-kkc
```

fcitx-kkcはビルドシステムにCMakeをつかっているので、
CMakeの慣習にのっとりビルドディレクトリをfcitx-kkc内につくります。

``` bash
mkdir build
cd build

cmake -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release ..

make
sudo make install
```

これでインストールはおわったので、あとはIMをIBusからFcitxに切り替えます。
私はim-configコマンドで切り替えましたがUbuntuの設定でも変えられるのかもしれません。この辺よくわからない。

以上。
