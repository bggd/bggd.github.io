---
layout: post
title: "mruby-minigameの紹介"
date: 2013-12-05 10:35
permalink: /blog/introduction-to-mruby-minigame/
---

# 注意！

# 2016年現在、この記事の内容は古く、間違いが含まれます。

- 最新のmruby-minigameのAPIと違う。
- Image#hold_drawingの説明が間違っている。
いわゆる、スプライトバッチの機能なので「描画順が担保されない」は間違い。
- mrubyのVisual Studioツールチェインのデフォルトはデバッグビルドではなくなっている。
- 現在のAllegro5のWindowsバイナリの入手方法はNuGet経由になっている。


----


> この投稿は [mruby Advent Calendar 2013](http://qiita.com/advent-calendar/2013/mruby) 5日目の記事です。

[mruby-minigame](https://github.com/bggd/mruby-minigame)は2Dゲームプログラミングのためのmruby拡張モジュールです。

アイデアが思い浮んだ時に
パッと動くところまで実装できるようなフレームワーク/ライブラリを目指しています。

この記事ではmruby-minigameのビルドと簡単に使い方の説明をします。

## Allegro 5のインストール

mruby-minigameは[allegro 5](http://alleg.sourceforge.net)というゲームライブラリをつかっているので
まず先にallegro 5をインストールする必要があります。

**Ubuntu**の場合は```sudo apt-get install liballegro5.0 liballegro5-dev```
でOKです。

**Windows**の場合はhttps://www.allegro.cc/files か
http://targonski.nazwa.pl/thedmd/allegro から
使用するコンパイラ(mingwかMSVC)向けのバイナリをダウンロードしてください。

vs2013を使っている場合は
http://targonski.nazwa.pl/thedmd/allegro の5.0.10にMSVC12向けのがあります。

**Mac OS X**は実行環境がないので何とも言えないのですが、
Homebrewの[homebrew-versions](https://github.com/Homebrew/homebrew-versions)というリポジトリにallegro 5があります。
headオプションでgit版も選べるようですが、現時点のデフォルトはなぜか
ひとつ古いバージョン（5.0.9）のようです。

iOSやAndroid、Raspberry Piで使う場合は開発版をソースからビルドする必要があります。

より一般的なビルド情報はallegro 5の各プラットフォームのREADMEと[Allegro 5のWiki](http://wiki.allegro.cc/index.php?title=Allegro_5)を参照してください。

## ビルド

mrubyのbuild_config.rbのビルド設定ブロックに
mruby-minigameのgithubリポジトリを追加してください。

```ruby
  conf.gem :github => 'bggd/mruby-minigame'
```

**Windows**ではさらにダウンロードしたallegro 5の
includeとlibフォルダへのパスを設定してください。

```ruby
  conf.cc.include_paths << 'allegro 5 のincludeフォルダ'
  conf.linker.library_paths << 'allegro 5 のlibフォルダ'
```

Linux以外のOSの場合はリンカにライブラリを設定してください。
（次のサンプルはLinux向けのものです）

```ruby
  conf.linker.libraries << %w(allegro allegro_main allegro_color allegro_image allegro_primitives allegro_font allegro_ttf allegro_audio allegro_acodec)
```

OS XならもしかしたらこれでOKかもしれませんがWindowsでは
例えば、allegro_color-5.0.10-mdのように
ライブラリのバージョンやビルド構成を元にポストフィックスを
指定しなればなりません。
書くのが面倒な時は、allegro 5の各モジュールとアドオンが
ひとつになったmonolithを指定するのがおすすめです。

```ruby
  conf.linker.libraries << %w(allegro-5.0.10-monolith-md)
```

Windowsの場合はmrubyのbinフォルダにallegro 5のDLLを置くのを忘れずに。

mrubyのVisual Studioでのビルドはデフォルトでデバッグビルド（/Od + /RTC1）になっているので
遅いです。最適化などを有効化しましょう。

## mruby-minigameの機能と使い方

今のところ、このようなモジュール構成です。
詳細は[WikiのAPIの項目](https://github.com/bggd/mruby-minigame/wiki)
を参照してください。

* Minigame::Color - 色の指定。RGBとHSVの表色系に対応
* Minigame::Display - 画面の生成、背面描画・更新
* Minigame::Image - 画像の読み込み、描画
* Minigame::Graphics - 図形の描画。線と矩形、円に対応
* Minigame::Font - TTFフォントの読み込み、描画
* Minigame::Gameloop - ゲームループ関連
* Minigame::Mouse - マウスの座標とボタンの状態の取得
* Minigame::Key - キーボードの状態取得
* Minigame::Audio
* Minigame::Music - 音声のストリーム再生
* Minigame::Sound - 効果音の再生

* Minigame::Event - Gameloopの実装につかっているので基本的には非推奨

今回はゲームループとキーボードとマウスの入力、Imageクラスの扱い方等を
かいつまんで紹介します。

### ゲームループ

ビデオゲームはプレイヤーからの入力や時間変化とともに
ゲームの世界をどんどん変化させる必要があり、
その一連のサイクルをゲームループと呼びます。

とりあえず雰囲気をわかってもらうために
以下のサンプルコードをみてください。

```ruby
include Minigame

Display.create(640, 480)

Gameloop.draw do
  Gameloop.quit if Key.down(Key::ESCAPE)

  Display.clear
end

Gameloop.run
```

Display.createで640x480の画面を作り、
Gameloop.drawへ渡したブロックがGameloop.runから毎フレーム呼び出されます。
ブロック内はエスケープキーが押されたらゲームループを終了し、
そうでないなら画面をクリアし続けるという内容です。

Gameloop.drawはゲームループ中の描画のタイミングで呼び出されますが
他にもプレイヤーからの入力があった時に呼ばれるkey_pressedやmouse_pressed
などがあります。

注意点としては、ブロックを渡しているのでreturnしたい場面ではnextを使います。

### キーボードとマウス

キーボードとマウスの状態はGameloopモジュールと
Key、Mouseモジュールから取得できます。
まずGameloopモジュールのほうから説明します。

Gameloopにはキーが押された時に呼ばれるkey_pressedと
キーが離された時のkey_releasedがあります。

ブロックの引数にKeyの定数が渡されます。[定数一覧](https://github.com/bggd/mruby-minigame/wiki/Minigame%3A%3AKey)

```ruby
Gameloop.key_pressed do |key|
  if key == Key::ESCAPE
    Gameloop.quit
  end
end
```

mouse_pressedとmouse_releasedではマウスのボタンが下がった時と
上がった時にそれぞれ呼び出されます。

ブロックの引数にはマウスの座標と、状態に変化のあったボタンが渡されます。
ボタンは:left, :middle, :rightのいずれかのシンボルです。

```ruby
Gameloop.mouse_pressed do |x, y, button|
  if button == :left
    p [x, y]
  end
end
```

次にKey、Mouseモジュールを説明します。

Keyにはキーが押されているか確認するKey.down。
そのフレーム時に押されたか確認するKey.pressed、離されたか確認するKey.released
があります。

Mouseには現在の座標を得るMouse.xとMouse.yのモジュール変数があり、
Keyと同様にMouse.downとMouse.pressed、Mouse.releasedがあります。


### Image

画像の読み込みや表示をするのがImageクラスです。

さっそくサンプルコードをみてください。

```ruby
include Minigame

Display.create(640, 480)

img = Image.load("hero.png")

Gameloop.draw do
  Display.clear

  img.draw(Mouse.x, Mouse.y)
end

Gameloop.run
```
Image.loadで指定した画像を読み込み、
毎フレームImage#drawでマウスの座標に画像を表示します。

Image.loadは画像を読み込めなかった時はnilを返します。
mruby-minigameではFontやMusicクラスも同様に読み込めなかった時はnilを返す方針です（例外の方がいいかな？）。

Image#drawはオプションで以下のキーワード引数を受け付けます。

* angle - 回転を0〜360度で指定 (デフォルトは0)
* color - 色合いをColorオブジェクトで指定 (Color.rgb())
* scale - X軸とY軸の拡大率を配列で指定 ([1.0, 1.0])
* anchor - 各軸のアンカーを指定 ([0.0, 0.0])
* pivot - 各軸のピボットを指定 ([0.5, 0.5])
* flip - 各軸の反転の有無を指定 ([false, false])

アンカーは画像をどの位置を起点に表示するかを示します。
Image#draw(x, y)では画像の左端を起点に描画しますが
Image#draw(x, y, anchor:[0.5, 0.5])では画像の中央を起点に表示します。

ピボットはどの位置を起点に回転をするか示します。
Flash向けのゲームエンジン、Starling Frameworkの
[Wiki](http://wiki.starling-framework.org/manual/pivot_points)の
画像がわかりやすいので引用します。

![alt text](http://wiki.starling-framework.org/_media/manual/pivot_point.png)

アンカーとピボットは[0.0, 0.0]が左上[0.5, 0.5]が真ん中、[1.0, 1.0]が右下になります。

Image.newで新規に画像を生成することも可能です。

例えば、白い32x32サイズの画像を生成するには：

```ruby
img = Image.new(32, 32)
```

赤い画像を生成するには：

```ruby
img = Image.new(32, 32, Color.rgb(255, 0, 0))
```

これをそのまま矩形の描画として使うこともできますが、
Image.targetに画像を渡すとそのうえに描画をすることもできます。

```ruby
img = Image.new(32, 32)

Image.target(img) do
  # img画像の上に描画をできる
  Graphics.circle(img.w/2, img.h/2, 16, color:Color.rgb(0, 0, 255))
end
```

Image#sub_imageは元の画像のメモリを共有したまま、その画像全体または
一部を表示するオブジェクトを生成します。

```ruby
img = Image.load("tile_sheet.png")
block_tile = img.sub_image(64, 64, 32, 32)
```

元の画像がGCされると二度と表示できないので注意してください。

Image.hold_drawingで同じ画像を効率良く描画することができます。

```ruby
include Minigame

Display.create(640, 480)

img = Image.load("hero.png")
positions = Array.new(100) { [rand(Display.w), rand(Display.h)] }

Gameloop.draw do
  Display.clear

  Image.hold_drawing(true)

  positions.each { |pos|
    img.draw(pos[0], pos[1])
  }

  Image.hold_drawing(false)
end

Gameloop.run
```

hold_drawingで囲っている間は描画順などが担保されないので注意が必要です。

これはallegro 5の機能をそのまま使っているのですが、
同じ画像の座標や色、回転等の情報をキャッシュして内部的な（OpenGL/Direct3Dの）DrawCallを減らすものです。たぶん。

### 音量

mruby-minigameの音声モジュールは主にBGM向けのMusicクラスと
効果音向けのSoundクラスにわけられます。

Audio.volumeをマスターボリュームとしてMusic.volume, Sound.volumeと
その各インスタンスが音量の影響を受けます。

<pre>
               - Music.volume - Music#volume
              /               - Music#volume
Audio.volume -
              \
               - Sound.volume - Sound#volume
                              - Sound#volume
</pre>

音量は0から100で表します。
Audio.volumeを100から50にした場合、全体の音量が下がります。
BGMの音量だけ下げたい時はMusic.volumeの値を下げます。

### 経過時間と休止

Minigame.get_timeで経過時間を取得します。

Minigame.restに指定した秒数のあいだ休止します。

1秒は1.0です。

## 終わりに

ざっと紹介しましたが、よりくわしい情報はWikiを参照してください。
exampleも増やしていく予定です。

コアな部分はバックエンドにつかっている
allegro 5の薄いラッパー程度で、より高機能な部分は
mruby-minigame-extとして追加できたらいいなと思います。

あったらいいなリスト：

* 素材の非同期読み込み
* 衝突判定
* Pygame風のSpriteとGroup（継承によるゲームオブジェクトの表現とその管理）
* IMGUI
* シーン管理

やる気駆動開発なのでいつ実装できるかまったくわからないのと
技術力的にきびしい部分（特に衝突判定）もあるので
mruby-minigameと連携できそうなライブラリ作ったぜという方は
ぜひissue等で報告してもらえればWikiにリンクを追加したいです。
