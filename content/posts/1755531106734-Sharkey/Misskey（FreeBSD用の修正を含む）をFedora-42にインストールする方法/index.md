---
title: "SharkeyやMisskeyをFedora 42にインストールするときに発生する問題に対処する"
date: 2025-08-18
draft: false
description: "a description"
tags: ["example", "tag"]
---
{{< alert >}}
これは[Hacker's Pubから](https://hackers.pub/@cocoa/2025/how-to-install-sharkey-misskey-with-fixes-for-freebsd-for-fedora-42/ja)の本人による~~無断~~転載です
{{< /alert >}}

FreeBSDで起動できるように修正されたMisskeyおよびSharkey（変更がすでに適用済み）をFedora 42環境にインストールする際、以下のエラーが発生することがあります。

```
error: ‘uint8_t’ was not declared in this scope
error: ‘state’ was not declared in this scope
```

これらの問題は、使用しているGCCのバージョンに起因しているようです（[参照](https://github.com/misskey-dev/misskey/issues/16098#issuecomment-2910448414)）。以下に、Fedora 42でこれらの問題を解決する方法を示します。

## ステップ1: 依存関係のインストール

まず、[Wiki](https://github.com/Automattic/node-canvas/wiki/Installation:-Fedora-and-other-RPM-based-distributions)に記載されているように、必要な依存関係をインストールします。

```sh
sudo dnf install cairo-devel libjpeg-turbo-devel pango-devel giflib-devel pixman-devel
```

## ステップ2: GCC/G++のコンパイル

FedoraにバンドルされているデフォルトのGCCを使用すると、`pnpm install`を実行した際にインストールが失敗する可能性があります（2025年5月27日現在）。この問題を避けるために、別のGCC/G++のバージョンをコンパイルして使用する必要があります。

まず、wgetを使用してGCCのソースコードをダウンロードし、解凍した後、ソースディレクトリに移動します。

```sh
wget https://ftp.tsukuba.wide.ad.jp/software/gcc/releases/gcc-13.3.0/gcc-13.3.0.tar.gz
tar xzf gcc-13.3.0.tar.gz
cd gcc-13.3.0
mkdir build
cd build
```

次に、GCC/G++をビルドするために必要な依存関係をインストールします。

```sh
sudo dnf group install development-tools
sudo dnf install mpfr-devel gmp-devel libmpc-devel zlib-devel glibc-devel.i686 glibc-devel isl-devel libgphobos-static
```

次に、ビルドを設定します（フラグは必要に応じて変更してください）。

```sh
../configure --disable-bootstrap --prefix=/usr --program-suffix=-13.3 --mandir=/usr/share/man --enable-languages=c,c++
```

設定が完了したら、以下のコマンドでGCCをコンパイルします。

```sh
make
```

より速いビルドのために複数のコアを利用するには、`-j`フラグを使用します。

```sh
make -j6
```

コンパイルが完了したら、新しいGCCバージョンをインストールします。

```sh
sudo make install
```

コンパイルしたGCCのインストールを確認するには、以下のコマンドを使用します。

```sh
gcc-13.3 -v
```

## ステップ3: Misskey/Sharkeyのインストールコマンドを修正

最後に、SharkeyとMisskeyを正常にインストールするために、インストールコマンドを以下のように修正します。

```sh
CXX=/usr/sbin/g++-13.3 CC=/usr/sbin/gcc-13.3 pnpm install --frozen-lockfile
```

これらの調整を行うことで、MisskeyとSharkeyを問題なくインストールできるはずです。Fediverseを楽しんでください！

*テキストを自然にするためにLLMをある程度使用しました。投稿前に確認しましたが、不自然な部分があればお知らせください。

## 参考文献
- [Fedora 41/40でGCC 14を使用してGCC 13.3をビルドする方法](https://www.if-not-true-then-false.com/2023/fedora-build-gcc/#9-check-gcc-13-installation)