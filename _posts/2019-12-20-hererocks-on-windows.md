---
layout: post
title: "Windows環境にluarocksをインストールするにはhererocksが便利"
---

最近luaとluarocksを使っています。
lua自体のビルドは5.4ならonelua.cを適当にCコンパイラに投げればよいので簡単です。
luarocksのインストールは自分には難しく、うまくいきませんでした...。

hererocksを使うとWindowsでも簡単にluaとluarocksをビルド、インストールすることができます。

hererocksは指定したローカルディレクトリにlua又はluajit/moonjitの特定のバージョンとluarocksをインストールすることができます。

例えば```hererocks .\my-lua53 --lua 5.3 --luarocks latest```とすれば.\my-lua53ディレクトリにlua 5.3.5がインストールされます。

luaとluarocksをPATHに一時的に通すためにはインストールしたディレクトリのbinサブディレクトリ内のactivateスクリプトを実行します。
コマンドプロンプト用にはactivate.bat。PowerShell向きにはactivate.ps1が用意されています。

hererocksはpythonで書かれているのでpythonが必要です。
現在、hererocksはluarocksのチームがメンテナンスしいるのでインストールに関する詳細はこちらのrepositoryを参照してください → [https://github.com/luarocks/hererocks](https://github.com/luarocks/hererocks)

私はBlender 3D内蔵のpythonを使ってhererocks.pyを直接実行しました。

hererocksはcl.exe(MSVC)がPATHにあればcl.exeをCコンパイラとして使い、無ければgccを代わりに使います(gccがPATHにあれば)。
```x64 Native Tools Command Prompt for VS 2019```から実行しましょう。
