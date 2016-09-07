---
layout: post
title: "gamepad2key"
---

ゲームパッド/ジョイスティックに対応していないゲーム用に、
それらの入力をキーボードやマウスの入力に変換する
ソフトウェアがありますが（joytokeyとかxpadder）
これらの実装ってどうなってんの？ ......という興味から適当に実装してみました。

[https://github.com/bggd/gamepad2key](https://github.com/bggd/gamepad2key)


[SDL2](https://www.libsdl.org)から受けたゲームパッドの入力を特定のキー入力としてSendInput(Win32 API)するだけ...という単純なものです。

試しにblizzardのblackthorneをプレイしたところ、
こんなのでも個人的には実用十分でした。

バイナリ用意してませんが一応説明↓<br />
使い方は
iniファイルにパッドとキーの対応を書いてコマンドプロンプトから
```gamepad2.exe your.ini```←みたいに実行します。

もう少し実用性のある物にするために、
最低でもSDL2のゲームパッドのマッピングのファイル読み込みに対応して
[SDL_GameControllerDB](https://github.com/gabomdq/SDL_GameControllerDB)
とか[SDL2 Gamepad Tool](http://www.generalarcade.com/gamepadtool/)
を生かせるようにしたいですね。
（自分はXbox Oneのパッドを使っているのと
リマッピングしたいわけでもないので必要性が今のとこ無いので実装しないかも）
