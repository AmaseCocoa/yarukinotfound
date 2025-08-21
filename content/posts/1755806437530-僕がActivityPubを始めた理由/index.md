---
title: "僕がActivityPubを始めた理由"
date: 2025-08-21
draft: false
description: "ActivityPubを知った経緯やapkit/Kaguraなどの話を。"
tags: ["activitypub", "fediverse", "apkit", "apsig", "apmodel", "apfetch"]
---

{{< alert >}}
**警告！** これはうろ覚えの記憶と少しの砂糖を混ぜて作った記事です。誤った情報が含まれているかもしれないので、注意して進んでください！
{{< /alert >}}

殆どapkitの話です。ごめんね

---

## なぜActivityPubに惹かれたのか？

### Misskeyとの出会い
僕が分散型SNSに興味を持ったのは、SNSで見かけた**Misskey.io**がきっかけでした。MisskeyにはAPIが公開されていたので、一時期はそれを使ってBotを開発していました。しかし、ネットワークの不安定さや、コードの管理ミスによる**ソースコードの消失**といった問題に直面し、Bot開発からは距離を置くようになりました。

そんな中、Misskeyが採用している**ActivityPub**という技術に強く惹かれ、その仕組みを本格的に調べるようになりました。

### ActivityPub実装への第一歩
「[Mastodonにアカウントとして認識されるActivityPubを実装してみる](https://qiita.com/wakin/items/94a0ff3f32f842b18a25)」というQiitaの記事を見つけたことが、自分でActivityPubを実装してみる大きなきっかけになりました。今思い返すと、この記事がなければこの挑戦は始まらなかったかもしれません。

#### 過去の挑戦：Hol0とGrapheneの挫折
実は、過去に**Hol0 (Graphene)**というActivityPub実装を自作しようと試みたことがあります。しかし、今から1年ほど前の当時はActivityPubについての知識がまだ未熟で、特に「署名」の実装でつまずき、プロジェクトを完成させることはできず、そのままフェードアウトしました。

---

## 現在のプロジェクト：Kaguraとライブラリ開発

過去の失敗を乗り越え、現在は**Kagura**というActivityPub実装を作成しています。このプロジェクトのために、いくつかのライブラリを開発しました。

### 開発の経緯
これらのライブラリは、当時のPythonにおけるActivityPub関連ライブラリの課題から生まれました。実用的なライブラリがほとんどなく、存在してもGPLやAGPLといった、比較的寛容ではないライセンスのものが多かったのです。

1.  **apsig**
    Kaguraで利用することを目的として、スタンドアロンの署名ライブラリである**apsig**を最初に開発しました。これは、Kagura以外でも手軽に署名を実装できるようにしたいという思いから生まれたものです。

2.  **apkit**
    次に、JavaScriptのActivityPubフレームワークである**Fedify**や、Python向けのFediverseユーティリティである**bovine**に大きな影響を受け、PythonにもFedifyのようなライブラリを作りたいという衝動から、**apkit**の開発を始めました。

3.  **apmodel**
    apkitの開発を進める中で、Activity Streams 2.0のパーサーに実用的なものがほとんどないことに気づきました。僕の性格上、既存のライブラリを修正するよりも、ゼロから新しく作成する方が効率的だと判断し、**apmodel**を開発しました。これが、現在のapkitの基盤の一つとなっています。

### apkitの思想と設計
apkitは、「ActivityPubの実装を、誰でも簡単に、シンプルかつ直感的に書けるようにすること」を最も重要な思想としています。

#### 1. 独立性と選択肢
apkitは、それ自体がなくてもActivityPub実装が可能なように設計されています。**apsig**や**apmodel**といった中核ライブラリはそれぞれ独立して利用できるため、apkitはこれらのライブラリを統合することで、より便利で効率的な開発体験を提供することを目指しています。開発者は、必要な機能だけを組み込むことも、apkit全体を利用して迅速に開発を進めることも自由に選択できます。

#### 2. 簡潔性
apkitのコードは、デコレータや型ヒントを積極的に採用することで、冗長な記述を避け、シンプルで読みやすいコードを実現しています。これにより、開発者はActivityPubの複雑な仕様に悩まされることなく、アプリケーションのロジックに集中できます。

### apkitのこれから
apkitはまだ未完成です。今後の主要な取り組みは以下の通りです。

* **全面的な書き直し:** **apmodel**は特に実装が乱雑であり、**apkit**と**apmodel**は全体的にコードを見直して書き直す予定です。
* **テストの拡充:** 現在、**apsig**のみにテストを実装していますが、今後は**apkit**や**apmodel**でもテストを拡充し、堅牢性を高めます。
* **同期サポートの強化:** **apkit**の非同期処理部分を分離し、同期処理のサポートも強化していく予定です。ただし、非同期処理のサポートは依然として継続し、同期処理のサポートはFlask等の非同期では無いフレームワーク向けに提供されます。
* **モジュールの分離:** **apkit**のActivityPubクライアントなど、主要なモジュールを分離し、独立して扱えるようにします。

これらの取り組みを通じて、apkitをより使いやすく、強力なツールへと進化させていきます。

## Links
{{< github repo="fedify-dev/fedify" showThumbnail=true >}}
<br>
{{< codeberg repo="bovine/bovine" >}}

---

{{< github repo="fedi-libs/apsig" showThumbnail=true >}}
<br>
{{< github repo="fedi-libs/apkit" showThumbnail=true >}}
<br>
{{< github repo="fedi-libs/apmodel" showThumbnail=true >}}

---

この記事は人間によって執筆され、`Gemini 2.5 Flash`による修正を含みます。