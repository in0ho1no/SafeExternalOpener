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
PS C:\Users\seigy> winget install jdx.mise
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
PS C:\Users\seigy>
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

### VSCode拡張機能開発に必要なパッケージのインストール

以下コマンドにより必要なパッケージをインストールする

```powershell
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj> mise exec -- npm install --save-dev @vscode/vsce

added 114 packages, removed 521 packages, and audited 290 packages in 15s

85 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
npm warn allow-scripts 2 packages have install scripts not yet covered by allowScripts:
npm warn allow-scripts   keytar@7.9.0 (install: node-gyp rebuild)
npm warn allow-scripts   @vscode/vsce-sign@2.0.9 (postinstall: node ./src/postinstall.js)
npm warn allow-scripts
npm warn allow-scripts Run `npm approve-scripts --allow-scripts-pending` to review, or `npm approve-scripts <pkg>` to allow.
[in0ho1no] PS D:\work\NodeJS\04_SafeExternalOpener\prj>
```

### 開発環境として保持する必要のない1度のみ実行する

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

#### 生じるエラーへの対処

safeexternalopener/package.json に以下を追記:

```json
"pnpm": {
  "onlyBuiltDependencies": ["esbuild", "@vscode/vsce-sign"]
}
```

その後:

mise exec -- pnpm install


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

選択肢	評価	コメント
npm	✅ 推奨(無難)	既に使用中。mise/Node 26 に同梱、追加導入ゼロ。@vscode/vsce も標準対応
pnpm	○ 有力な次点	速い・ディスク効率良い・依存解決が厳密。こだわるなら可
yarn	△	今あえて選ぶ理由は薄い。Classic は古く、Berry(PnP)は拡張開発で詰まりやすい
判断理由
1. 個人プロジェクトかつ規模が小さい
"SafeExternalOpener" は単一拡張です。pnpm/yarn の主な利点(モノレポ、大量依存、CI高速化)が効きにくく、npm で困りません。

2. vsce との相性
@vscode/vsce は npm を前提に作られていて一番こなれています。pnpm でも動きますが、シンボリックリンク構造ゆえ vsce package 時に依存が拾えず --no-dependencies 等の追加配慮が必要になる場合があります。バンドラに esbuild を使う前提なら、依存は1ファイルに固められるのでこの問題はほぼ無視できます(=pnpm も実用上問題なし)。

3. mise との統合
mise は node と一緒に npm を面倒見てくれます。pnpm を使うなら mise use npm:pnpm 等で別途管理する手間が増えます。

推奨
特にこだわりがなければ npm:既存の mise exec -- npm ... フローをそのまま継続
速度・厳密性を重視するなら pnpm:ただし esbuild バンドル前提とし、.vsix 作成時の挙動を一度確認しておく
yarn は今回は積極的に選ぶ理由がない、という整理です。
