# Prepare

自分用の作業メモ

## NodeJS環境準備

### Voltaの用意

voltaは25年5月頃にインストールしていたが11月頃には更新が止まっている旨のissueが立っているので利用しない。

https://github.com/volta-cli/volta/issues/2080

    Important

    Volta is unmaintained. Everything that works today should continue to do so for the foreseeable future, so if it is working for you, there is no particular urgency to migrate to another tool, but we will not be able to address breakages from new OS releases or other changes in the ecosystem, so you should put it on your maintenance roadmap at some point. We recommend migrating to mise. See issue #2080.   

### miseの用意

上記で勧められているmiseを利用する。

https://mise.jdx.dev/getting-started.html

上記ページの手順に従いwingetでインストールする

```powershell
PS C:\Users\> winget install jdx.mise
既存のパッケージが既にインストールされています。インストールされているパッケージ...をアップグレードしようとしています
見つかりました mise-en-place [jdx.mise] バージョン 2026.6.10
このアプリケーションは所有者からライセンス供与されます。
Microsoft はサードパーティのパッケージに対して責任を負わず、ライセンスも付与しません。
このパッケージには次の依存関係が必要です:
  - パッケージ
      Microsoft.VCRedist.2015+.x64
ダウンロード中 https://github.com/jdx/mise/releases/download/v2026.6.10/mise-v2026.6.10-windows-x64.zip
  ██████████████████████████████  30.5 MB / 30.5 MB
インストーラーハッシュが正常に検証されました
アーカイブを展開しています...
アーカイブが正常に展開されました
パッケージのインストールを開始しています...
コマンド ライン エイリアスが追加されました: "mise-shim"
コマンド ライン エイリアスが追加されました: "mise"
インストールが完了しました
PS C:\Users\>
```

この後はターミナル・コマンドプロンプトを再起動させておく。  

### miseでNode.jsをインストール

以下で最新のnodejsをインストールしてグローバルのデフォルトに設定する

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise use --env local node@latest
node@26.3.0                                                                                                            ◜
mise D:\work\NodeJS\04_SafeExternalOpener\prj\mise.local.toml tools: node@26.3.0
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

以下でインストールした状態を確認できる。

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise list
Tool  Version  Source                                                    Requested
node  24.12.0
node  25.2.1
node  26.3.0   D:\work\NodeJS\04_SafeExternalOpener\prj\mise.local.toml  latest
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

### miseでpnpmをインストール

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise use --env local pnpm@latest
pnpm@11.6.0     extract pnpm-win32-x64.zip                                                                             ✔
mise D:\work\NodeJS\04_SafeExternalOpener\prj\mise.local.toml tools: pnpm@11.6.0
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

### 開発時のバージョン

以下で各バージョンを確認する

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise exec -- node -v
v26.3.0
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise exec -- npm -v
11.16.0
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise exec -- pnpm -v
11.6.0
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

### 開発用フォルダ作成

#### 初期化

以下コマンドによりプロジェクトを初期化する。

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise exec -- npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help init` for definitive documentation on these fields and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (prj) SafeExternalOpener
Sorry, name can no longer contain capital letters.
package name: (prj) safe-external-opener
version: (1.0.0) 0.0.1
description:
entry point: (index.js)
test command:
git repository: (https://github.com/in0ho1no/SafeExternalOpener.git)
keywords:
author:
license: (ISC) MIT
type: (commonjs)
About to write to D:\work\NodeJS\04_SafeExternalOpener\prj\package.json:

{
  "name": "safe-external-opener",
  "version": "0.0.1",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/in0ho1no/SafeExternalOpener.git"
  },
  "author": "",
  "license": "MIT",
  "type": "commonjs",
  "bugs": {
    "url": "https://github.com/in0ho1no/SafeExternalOpener/issues"
  },
  "homepage": "https://github.com/in0ho1no/SafeExternalOpener#readme"
}


Is this OK? (yes)
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

## VSCode拡張機能の開発環境

### プロジェクト構成方針

このリポジトリは「親=作業手順・git・インフラの入れ物 / 子=拡張本体」という入れ子構成にする。

- ルート `prj/` は Node プロジェクトにしない。
  `Docs/`、`.github/`、`docker/`、`git-setup/`、`*.code-workspace` 等の入れ物に徹する。
  ルートに `package.json` / `node_modules` / ロックファイルを置かない。
- 拡張本体 `prj/safeexternalopener/` を Node / pnpm プロジェクトとする。
- 公開ツール `@vscode/vsce` も拡張本体の `devDependencies` に入れ、
  `safeexternalopener/` の中で `vsce package` / `vsce publish` を実行する。

こうすると `node_modules`・ロックファイル・`pnpm-workspace.yaml` が拡張側の1セットに集約され、
パッケージマネージャの混在(npm と pnpm の併用)や設定場所のミスマッチが起きない。

> 手順の順番に注意:
> 先に雛形を生成(`yo code`)してから、生成された `safeexternalopener/` の中で
> 依存・公開ツールを入れる。`@vscode/vsce` を先にルートへ入れてはいけない。

### 拡張機能の雛形を生成する(開発環境として保持する必要のない1度のみ実行)

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise exec -- npx --package yo --package generator-code -- yo code

     _-----_     ╭──────────────────────────╮
    |       |    │   Welcome to the Visual  │
    |--(o)--|    │   Studio Code Extension  │
   `---------´   │        generator!        │
    ( _´U`_ )    ╰──────────────────────────╯
    /___A___\   /
     |  ~  |
   __'.___.'__
 ´   `  |° ´ Y `

`list` prompt is deprecated. Use `select` prompt instead.
✔ What type of extension do you want to create? New Extension (TypeScript)
✔ What's the name of your extension? SafeExternalOpener
✔ What's the identifier of your extension? safeexternalopener
✔ What's the description of your extension? Open Office, PDF, and Windows shortcut targets safely from VS Code Explorer with strict extension al
lowlisting.
✔ Initialize a git repository? Yes
`list` prompt is deprecated. Use `select` prompt instead.
✔ Which bundler to use? esbuild
`list` prompt is deprecated. Use `select` prompt instead.
✔ Which package manager to use? pnpm

Writing in D:\work\NodeJS\04_SafeExternalOpener\prj\safeexternalopener...
   create safeexternalopener\.vscode\extensions.json
   create safeexternalopener\.vscode\launch.json
   create safeexternalopener\.vscode\settings.json
   create safeexternalopener\.vscode\tasks.json
   create safeexternalopener\package.json
   create safeexternalopener\tsconfig.json
   create safeexternalopener\.vscodeignore
   create safeexternalopener\esbuild.js
   create safeexternalopener\vsc-extension-quickstart.md
   create safeexternalopener\.gitignore
   create safeexternalopener\README.md
   create safeexternalopener\CHANGELOG.md
   create safeexternalopener\src\extension.ts
   create safeexternalopener\src\test\extension.test.ts
   create safeexternalopener\.vscode-test.mjs
   create safeexternalopener\eslint.config.mjs
   create safeexternalopener\.npmrc

Changes to package.json were detected.

Running pnpm install for you to install the required dependencies.

   ╭───────────────────────────────────────────╮
   │                                           │
   │    Update available! 11.6.0 → 11.7.0.     │
   │    Changelog: https://pnpm.io/v/11.7.0    │
   │   To update, run: pnpm add -g @pnpm/exe   │
   │                                           │
   ╰───────────────────────────────────────────╯

[WARN] 1 deprecated subdependencies found: glob@10.5.0
Packages: +359
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Progress: resolved 385, reused 0, downloaded 359, added 359, done

devDependencies:
+ @types/mocha 10.0.10
+ @types/node 22.19.21 (25.9.3 is available)
+ @types/vscode 1.120.0
+ @vscode/test-cli 0.0.12
+ @vscode/test-electron 2.5.2 (3.0.0 is available)
+ esbuild 0.27.7 (0.28.1 is available)
+ eslint 9.39.4 (10.5.0 is available)
+ npm-run-all 4.1.5
+ typescript 5.9.3 (6.0.3 is available)
+ typescript-eslint 8.61.0

[ERR_PNPM_IGNORED_BUILDS] Ignored build scripts: esbuild@0.27.7

Run "pnpm approve-builds" to pick which dependencies should be allowed to run scripts.
Error code

Command failed with exit code 1: pnpm install
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

(末尾の `pnpm install` 失敗は後述の「生じるエラーへの対処」で解消する)

### 公開ツール `@vscode/vsce` を拡張本体に追加する

構成方針どおり、`@vscode/vsce` はルートではなく 拡張ディレクトリ
`safeexternalopener/` の `devDependencies`に入れる。pnpm で追加する。

```powershell
mise exec -- pnpm -C safeexternalopener add -D @vscode/vsce
```

`@vscode/vsce` は `@vscode/vsce-sign`(署名検証)と `keytar`(PAT保存)を連れてくるため、
この直後にも `[ERR_PNPM_IGNORED_BUILDS]` が出る。次節でまとめて対処する。

#### 生じるエラーへの対処

`[ERR_PNPM_IGNORED_BUILDS] Ignored build scripts: ...` というエラーが出る。
これは pnpm v10 以降のセキュリティ機能で、依存パッケージの install/postinstall
スクリプトをデフォルトで全てブロックするために起きる。
ネイティブバイナリを配置する必要のあるパッケージ(esbuild など)はビルドを許可する必要がある。

##### ポイント1: 設定は拡張プロジェクト1か所に集約する

構成方針どおり Node / pnpm プロジェクトは `safeexternalopener/` の1つだけなので、
ビルド許可の設定も `prj/safeexternalopener/pnpm-workspace.yaml` の1か所に書く。
esbuild(雛形由来)も `@vscode/vsce-sign` / `keytar`(vsce 由来)も、すべてここで扱う。

| 依存 | 出どころ | 扱い |
|------|----------|------|
| `esbuild` | 雛形(バンドラ) | `true`(バンドルに必須) |
| `@vscode/vsce-sign` | `@vscode/vsce` | `true`(公開時の事故防止に許可) |
| `keytar` | `@vscode/vsce` | `false`(非推奨かつ `vsce login` でしか使わないのでスキップ) |

##### ポイント2: pnpm v11 は `allowBuilds:` マップで指定する

`package.json` の `pnpm.onlyBuiltDependencies` は pnpm v11 では読まれない
(`[WARN] The "pnpm" field in package.json is no longer read by pnpm` が出る)。
`pnpm install` のたびに pnpm が `pnpm-workspace.yaml` に以下のような
プレースホルダを自動追記するので、`set this to true or false` を実際の
真偽値(`true`=ビルド許可 / `false`=意図的にスキップ)に **埋める**。

`prj/safeexternalopener/pnpm-workspace.yaml`:

```yaml
allowBuilds:
  esbuild: true               # バンドルに必須
  '@vscode/vsce-sign': true   # 公開時の事故防止に許可
  keytar: false               # 非推奨かつ vsce login でしか使わないのでスキップ
```

埋めたあと、拡張ディレクトリで以下を実行すると、許可したパッケージの
ビルドが走りエラーも消える。

```powershell
mise exec -- pnpm -C safeexternalopener install
```

##### 補足: keytar をスキップしてよい理由

`keytar` は `vsce login` で PAT(個人アクセストークン)を OS のキーチェーンに
保存するときだけ使われる。公開は環境変数 `VSCE_PAT` で渡せば keytar に触れない。
かつ keytar はメンテ終了済みなので、ビルドせずスキップ(`false`)でよい。

```powershell
$env:VSCE_PAT = "<Azure DevOps で発行したトークン>"
mise exec -- pnpm -C safeexternalopener exec vsce publish
```

#### 補足1

なぜ esbuild か
1. なぜ「そもそもバンドルが要るのか」
拡張は最終的に .vsix で配布します。unbundled だと node_modules を全部同梱することになり:

パッケージサイズが肥大(数百ファイル)
起動(activation)が遅くなる
今回の keytar のようなネイティブ依存・脆弱性も丸ごと同梱
バンドルすると 1ファイル(dist/extension.js)に圧縮され、これらが解消します。

2. esbuild vs webpack

どちらも「1ファイル化」は達成できる
違いは速度と設定量。esbuild は Go製でビルドが桁違いに速く、設定ファイルも数十行で済む
webpack が要るのは「Webview に複雑なフロントエンド資産をまとめる」等の特殊ケース。今回の "SafeExternalOpener"(外部を安全に開く拡張)のような規模では不要

#### 補足2

パッケージマネージャの選定 → pnpm を採用。

| 選択肢 | 評価 | コメント |
|--------|------|----------|
| pnpm | ✅ 採用 | 速い・ディスク効率良い・依存解決が厳密。雛形(`yo code`)も pnpm を選択でき、`pnpm-workspace.yaml` が生成される。mise で管理可能 |
| npm | ○ 無難な代替 | Node 同梱で追加導入ゼロ。`@vscode/vsce` も標準対応。こだわりが無ければこれでも可 |
| yarn | △ | 今あえて選ぶ理由は薄い。Classic は古く、Berry(PnP)は拡張開発で詰まりやすい |

判断理由

1. mise との統合
このリポジトリでは別途 pnpm も mise で管理している(「### miseでpnpmをインストール」参照)。
そのため pnpm を使っても導入・バージョン管理の手間は増えない。

2. vsce との相性
`@vscode/vsce` は元々 npm 前提で一番こなれており、pnpm はシンボリックリンク構造ゆえ
`vsce package` 時に依存が拾えず `--no-dependencies` 等の追加配慮が要る場合がある。
ただし本構成は バンドラに esbuild を使い、配布物を1ファイルに固める前提なので、
この問題は実用上ほぼ無視できる。

3. pnpm 採用時の注意
pnpm v10 以降はビルドスクリプトをデフォルトでブロックする。
本手順では `pnpm-workspace.yaml` の `allowBuilds:` で
esbuild / @vscode/vsce-sign を許可、keytar をスキップして対処する
(「#### 生じるエラーへの対処」参照)。

結論
- 本プロジェクトは pnpm を採用(雛形が pnpm 構成を生成し、mise でも管理済みのため)。
- 速度・厳密性にこだわらないなら npm でも可。yarn は積極的に選ぶ理由がない。
