---
layout: post
title: "Chrome Remote Desktopで、こたつでノートPCがはかどる"
---

冬場は寒いので、こたつでchromebookを使っている。
コードを書く場合はPCを起動して、それにSSHで接続する。
これにはchrome拡張のSecure Shellを使っている。

これだけでも便利だが普段PCのブラウザから電子書籍の漫画を読むのに使っていたサービスは
なぜかchromebookからはブラウザ視聴できない。
いつからかchromebookはPlayストアに対応したので視聴用のAndroidアプリを入れるという手もある。
しかしどうせならPCに入っているソフトウェアを操作できるリモートデスクトップを使ってみる事にした。

chromeリモートデスクトップはLinuxをホスト（サーバー？）にもできるのでUbuntu 16.04 LTSに入れた。
これを試す前にxrdpとMS謹製のRDPアプリを試したが自分のスキル不足でうまくいかなかったが、
chromeリモートデスクトップはすんなり動いてくれたのでありがたい。

ネットワークに詳しくないので間違っている可能性もあるが、
[https://support.google.com/chrome/a/answer/2799701?hl=ja](https://support.google.com/chrome/a/answer/2799701?hl=ja)
←を読む限りRemoteAccessHostFirewallTraversalを無効にすると家庭内で閉じた構成にできそうなので、
[https://www.chromium.org/administrators/linux-quick-start](https://www.chromium.org/administrators/linux-quick-start)
←このへんを参考にポリシーを変更した。

```json
{
  "RemoteAccessHostFirewallTraversal" : false
}
```

上記のJSONファイルを適切な場所に保存することでLinux上のchromeのポリシーとやらを変更できるようだ。
設定が反映されているかはchrome://policyから確認できる。

ついでにリモートデスクトップ経由でBlender 3Dも使ってみたが、
ズーム時に多少の遅延はあるような気もするが問題なく動作している。
Blenderは画面の更新頻度がたしかデフォルトは20FPSなのでリモートデスクトップの負荷的には軽い部類だとは思う。

blenderを使う上で問題になったのが多ボタンマウスのクリックが、標準の左、中、右クリックしかリモートデスクトップからは扱えなかったことだ。
これはchromeリモートデスクトップの制限なのか知らないが困った。もちろん私のマウスの問題かもしれないが。

blenderでは視点の操作に中クリック（MMB）が標準で割り当てられているが、私は中クリックの、つまりスクロールホイールを押した時のペコペコした感触が嫌いで
多ボタンマウスのサイドにあるボタン（ブラウザの戻るや進むに一般的に割り当てられるアレ）を視点操作に割り当てている。
リモートデスクトップ経由ではこの割当が実現できなかったので、blenderの入力の設定のemulate 3 button mouseを有効にしてAlt+LMBで代用した。
blenderはキーボードを多用するのでAlt+LMBでもそこまで不便ではないかもしれない（と言い聞かせているのかもしれない）。

こうなってくるとPixelbookが欲しくなってしまう。
今使っているchromebookの解像度は1366x768でPixelbookは2400x1600だ。
リモートデスクトップ的には転送量が増えると遅延も増えるだろうから、このへんは不安材料ではある。
そもそもPixelbookの価格帯だとsurface proのような2in1のWindowsラップトップやタブレットが射程に入ってしまうのも悩みどころだ。
というか、その金で良いCPUを買ってPC側の性能を上げるほうが、基本的にはいいだろうか...。

リモートデスクトップとは関係ないが、ちょうどautodesk sketchbookのandroid版が更新されてPixelbookでも遅延が少なくなり実用的になっていると聞く（redditあたりからの伝聞でしかないが）のでお絵かきタブレットとしても魅力がある。
[https://www.sketchbook.com/blog/sketchbook-for-android-4-0-a-monster-upgrade/](https://www.sketchbook.com/blog/sketchbook-for-android-4-0-a-monster-upgrade/)
