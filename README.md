Erlang Hacking Guide
====================

本文章について
--------------

本文章は[Rubyソースコード完全解説(RHG)](http://i.loveruby.net/ja/rhg/book/)にインスパイアされて作成しました。

[Erlang](http://www.erlang.org/)は並行処理指向の関数型言語です。

- 分散環境
- 障害耐性(フォルトトレラント)
- 無停止稼動

といった仕組み・機能を効率良く使う事ができます。

本文章のテーマは

- ErlangVM(BEAM)の構造を知る
- Erlangについての勉強のアウトプット
- 日本語の適当な文章が無かったので書く事にした
- etc

です。

本文章の構成について
--------------------

本文章は以下の章から構成されます。

### Erlangのビルド

[Ch00.事前準備](./ch00.md)

[Ch01.Erlangのフォルダ構成](./ch01.md)

[Ch02.Erlangビルド時に何が起こっているか](./ch02.md)

[makefile](./makefile.md)

[entry](./entry.md)

[macro](./macro.md)

[data](./data.md)
