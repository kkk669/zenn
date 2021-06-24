---
title: "play.js を使って iPad で Zenn CLI を使って Zenn の記事を書く"
emoji: "✏️"
type: "tech"
topics: ["ipad", "zenn"]
published: true
---

## はじめに

iPad には [play.js](https://playdotjs.com) という JavaScript IDE があり、Node.js, React Native, React, Vue.js, Next.js などの開発がネイティブ環境でできます。最近 CodeSandbox というオンライン IDE を作っている会社に[買収された](https://codesandbox.io/post/codesandbox-acquires-playjs)ので、個々の豊富なプロジェクトテンプレートが play.js から使えるようになったり、CodeSandbox 上で作成したプロジェクトを開けるようになったりとこちらとのインテグレーションも進んできています。

さらに、このアプリは (直接 CLI で叩けるわけではありませんが) ネイティブで npm を含む Node.js が実行できるため、「Zenn CLI が使えるのではないか？」と疑問を持ち実際に試してみたところ問題なく使えたので簡単ですが方法を記事にまとめました。

基本的には Zenn 公式の「[Zenn CLIをインストールする](https://zenn.dev/zenn/articles/install-zenn-cli)」の流れをそのまま play.js 上で追っていくだけなので、こちらの記事をまだ読んだことがない方はこちらもどうぞ。

## サンドボックス作成

まずは Zenn の記事を管理するためのサンドボックスを作成していきます。play.js を立ち上げると以下のような画面が表示されるので、"New Sandbox" を選択して新規サンドボックスを作成しましょう。

![](https://storage.googleapis.com/zenn-user-upload/20b899bee3d3be222dc00827.png)

するとテンプレートから作成するか、ローカルストレージ上のディレクトリを選択するか、Git からクローンするかなどが選択できます。

![](https://storage.googleapis.com/zenn-user-upload/0a272a9ce3f7ab191a95f8c3.png)

新規作成する場合は "New node.js application" を選択します。

既に Zenn と連携済みのリポジトリを持っている方はここで "Clone from Git repository" を選択するか、あるいは [Working Copy](https://workingcopy.app) などの Git クライアントアプリでクローン済みのディレクトリを "Open directory" で選択すると良いでしょう。play.js は Git クライアントとしての機能も持っているので新規作成したサンドボックスで各ファイルのコミットやリモートリポジトリ (GitHub 等) への push/pull も行えます。

## Zenn CLI のインストール

サンドボックスが開かれると以下のような画面になります。

![](https://storage.googleapis.com/zenn-user-upload/ef03ed88c4d45eb7b8aa6a30.png)

play.js で新規作成した場合は既に `package.json` ファイルが存在しますが、既にあるリポジトリをクローンした場合はもしこの段階で `package.json` ファイルがまだなければ以下のような単純な内容で良いので作成しておきましょう。

```json
{
  "name": "zenn"
}
```

次に以下の画面の左下にある "$ default: node index.js" と書かれたプルダウンメニューを選択します。

![](https://storage.googleapis.com/zenn-user-upload/e98838ace91d2411dac37714.png)

すると以下のようなスクリプト一覧画面が開くので、ここで "install" と書かれた項目を選択します。

![](https://storage.googleapis.com/zenn-user-upload/970244999a1f4c7380ffd721.png)

すると以下のような依存解決用の画面に遷移するので、下の方にある "Add module" セクションの "Name" に `zenn-cli` と入力し、"Development dependency" にチェックを入れて "Resolve module" ボタンを押してください。ここで Zenn CLI のインストールが始まります。

![](https://storage.googleapis.com/zenn-user-upload/5dbd6ed08389ef3f9a3c16a0.png)

インストールが終わったら、`package.json` に `devDependencies` のキーと値が追加され、`package-lock.json` ファイルと `node_modules` ディレクトリが増えていることが確認できると思います。

![](https://storage.googleapis.com/zenn-user-upload/5acda1f766765e1f5147a49d.png)

## Zenn のセットアップ

インストールが終わったのでここで `npx zenn init` といきたいところですが、あいにくと play.js には CLI がないので、代わりに `package.json` に `scripts` を書いて GUI 上で実行します。

まずは以下のような項目を `package.json` に追加してください。


```json
  "scripts": {
    "init": "zenn init",
    "preview": "zenn preview"
  },
```

すると例えば私の場合は以下のようになりました。

```json
{
  "name": "zenn",
  "scripts": {
    "init": "zenn init",
    "preview": "zenn preview"
  },
  "devDependencies": {
    "zenn-cli": "^0.1.89"
  }
}
```

この状態で先ほど同様画面左下からスクリプト一覧画面を開くと、今書いた "init" と "preview" の項目が追加されているのが確認できると思います。

![](https://storage.googleapis.com/zenn-user-upload/91372fb7fc5a8f015a4fa729.png)

ここで、"init" を選択しましょう。するとエディタ画面に戻るので、画面の左下にある実行ボタンを押します。

![](https://storage.googleapis.com/zenn-user-upload/d677c2474d868148f50ebccd.png)

すると以下のように Zenn CLI の出力メッセージが画面下部に表示されます。

![](https://storage.googleapis.com/zenn-user-upload/e0feed619139a87e471b653d.png)

このメッセージが正常に表示されて、`articles` ディレクトリや `books` ディレクトリ、`README.md`, `.gitignore` ファイルなどが生成されていれば成功です。

## 記事を書く

では実際に `articles` ディレクトリ以下にファイルを作成して記事を書いてみます。しばらくするとプレビューしたくなってくることでしょう。

そういう時は、先ほどスクリプト一覧画面で選択しなかった "preview" の方を選択して実行してみましょう。すると画面下部には

```
Preview: http://localhost:8000
```

とだけ表示されますが、画面右下にある🌐ボタンを押してください。

![](https://storage.googleapis.com/zenn-user-upload/ba519b200f465f5c876e915d.png)

すると、Split View でブラウザウィンドウが立ち上がります。このアドレスバーに上記の URL を打ち込むと Zenn のプレビュー画面が表示されます。もちろん左側のエディタで記事を編集するとリアルタイムにプレビューに反映されていきます。

![](https://storage.googleapis.com/zenn-user-upload/64d16f3a0869e955f7290fa3.png)

これで導入完了です。🎉

## 次のステップへ

Zenn CLI を使って記事を書く上での具体的な話は「[Zenn CLIで記事・本を管理する方法](https://zenn.dev/zenn/articles/zenn-cli-guide)」に書かれているので是非目を通してみてください。

もちろんこの記事も play.js で Zenn CLI を使って執筆しました。みなさんもどんどん Zenn に iPad の技術記事を iPad で書いていきましょう。それでは良い iPad ライフを。
