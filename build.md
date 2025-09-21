# ソースコードからのビルド方法

このセクションでは、Erlang/OTPをソースコードからビルドする手順を説明します。
このガイドは主にMacOSでの手順を対象としていますが、Linuxでも同様の手順でビルド可能です。

## 1. ソースコードの取得

githubからソースコードを取得します。

```bash
$ git clone https://github.com/erlang/otp.git
$ cd otp
```






## 2. 必要なパッケージのインストール

Erlangのビルドには以下のパッケージが必要です。

### MacOSの場合




インストール手順

```bash
$ brew install ...
```


## 3. ビルドとインストール





```sh
$ ./configure
$ make
$ sudo make install
```

## 4. インストール確認

```sh
erl
```

Erlangシェルが起動すれば成功です。

## 参考


