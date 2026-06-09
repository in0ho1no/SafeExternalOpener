# Safe External Opener for VS Code - SPEC

## 目的

VS Code の Explorer 上で、VS Code 標準では扱いにくい Office 文書、PDF、Windows ショートカットを、安全性を優先して OS 既定アプリで開けるようにする。

本拡張機能は、汎用的な「任意ファイルを任意コマンドで開く」拡張ではない。攻撃面を極力増やさないため、対応拡張子を限定し、任意コマンドや任意引数の実行機能は提供しない。

---

## 対象プラットフォーム

### 必須対応

- Windows
- VS Code Desktop

### 非対象

- VS Code Web
- GitHub Codespaces 上の Web 版 VS Code
- Remote 環境での外部アプリ起動保証

Remote-SSH、WSL、Dev Containers では、`vscode.env.openExternal()` の挙動が環境依存になる可能性があるため、Windows ローカルワークスペースを主対象とする。

---

## 基本方針

### セキュリティ方針

- 安全性を最優先する。
- シェル経由の任意コマンド実行を行わない。
- `child_process.exec()` を使用しない。
- PowerShell、cmd.exe、bash などを経由してファイルを開かない。
- ファイルを開く処理には原則として `vscode.env.openExternal()` を使用する。
- 対応拡張子は allowlist 方式で限定する。
- 拡張機能設定から任意コマンドを指定できる機能は提供しない。
- `.lnk` の任意引数、作業ディレクトリ、アイコン情報などは使用しない。
- `.lnk` はショートカット自体を実行せず、リンク先の `TargetPath` のみを解決して開く。

---

## 対応ファイル種別

### Office 文書

以下の拡張子のみ対応する。

#### Excel

- `.xlsx`
- `.xlsm`
- `.xls`

#### Word

- `.docx`
- `.doc`

#### PowerPoint

- `.pptx`
- `.ppt`

### PDF

- `.pdf`

### Windows ショートカット

- `.lnk`

---

## 非対応ファイル種別

以下を対象外とする。

- `.csv`
- `.rtf`
- `.ppsx`
- `.vsdx`
- `.accdb`
- `.msg`
- `.url`
- `.bat`
- `.cmd`
- `.ps1`
- `.exe`
- `.msi`
- `.vbs`
- `.js`
- `.jar`
- その他 allowlist にないすべての拡張子

`.csv` は VS Code で直接開けるため対象外とする。

---

## 機能要件

### Explorer コンテキストメニュー

VS Code Explorer 上で対応ファイルを右クリックしたとき、以下のコマンドを表示する。

```text
Open Safely in External App
```

表示条件は以下とする。

- 対象がファイルであること
- 拡張子が allowlist に含まれること

フォルダには表示しない。

---

### Office / PDF ファイルを開く

ユーザーが Office 文書または PDF を選択してコマンドを実行した場合、以下の処理を行う。

1. 選択された URI を取得する。
2. `file` scheme であることを確認する。
3. 拡張子を小文字化して allowlist と照合する。
4. 対象が通常ファイルであることを確認する。
5. `vscode.env.openExternal(vscode.Uri.file(filePath))` で開く。

#### 期待動作

- `.xlsx` は Windows で関連付けされたアプリ、通常は Excel で開く。
- `.docx` は Word で開く。
- `.pptx` は PowerPoint で開く。
- `.pdf` は既定の PDF ビューアで開く。

本拡張機能は特定アプリケーションのパスを指定しない。

---

### Windows ショートカットを開く

ユーザーが `.lnk` を選択してコマンドを実行した場合、以下の処理を行う。

1. 選択された URI を取得する。
2. `file` scheme であることを確認する。
3. 拡張子が `.lnk` であることを確認する。
4. `.lnk` のリンク先 `TargetPath` のみを取得する。
5. `Arguments` は取得しても使用しない。
6. `WorkingDirectory` は使用しない。
7. `TargetPath` が空の場合はエラー表示する。
8. `TargetPath` が存在しない場合はエラー表示する。
9. `TargetPath` が許可されたファイル拡張子、またはフォルダである場合のみ開く。
10. 開く処理には `vscode.env.openExternal()` を使用する。

#### `.lnk` のリンク先として許可するもの

- フォルダ
- 以下のファイル
  - `.xlsx`
  - `.xlsm`
  - `.xls`
  - `.docx`
  - `.doc`
  - `.pptx`
  - `.ppt`
  - `.pdf`

#### `.lnk` のリンク先として拒否するもの

- 実行ファイル
- スクリプト
- URL
- allowlist にない拡張子のファイル
- 存在しないパス

拒否時は次のようなエラーメッセージを表示する。

```text
This shortcut target is not allowed by Safe Office/PDF Opener.
```

---

## `.lnk` 解決方法

### 推奨実装

Node.js で安全に `.lnk` の TargetPath を取得できるライブラリを利用する。

候補例:

- `windows-shortcuts`
- `@emmetio/windows-shortcuts`
- その他、`.lnk` の読み取りのみを行える軽量ライブラリ

採用前に以下を確認する。

- ライセンスが商用利用可能であること
- 依存関係が過度に大きくないこと
- メンテナンス状況が極端に悪くないこと
- `.lnk` の作成や実行ではなく、読み取りだけに使えること

### PowerShell / WScript.Shell の扱い

PowerShell または `WScript.Shell` COM 経由で `.lnk` を読む方法は存在するが、初期実装では原則避ける。

理由:

- 外部プロセス起動が必要になる。
- 実装を誤るとコマンド注入リスクが増える。
- セキュリティ方針として、シェル経由処理を避けたい。

どうしても採用する場合は、以下を必須条件とする。

- `exec()` は使わない。
- `execFile()` または `spawn()` を使う。
- ユーザー入力をコマンド文字列へ連結しない。
- `.lnk` パスは引数として渡す。
- 取得するのは `TargetPath` のみ。
- `Arguments` は実行しない。

---

## コマンド仕様

### コマンド ID

```text
safeOfficeOpener.openSafely
```

### コマンドタイトル

```text
Open Safely in External App
```

### `package.json` contribution 例

```json
{
  "contributes": {
    "commands": [
      {
        "command": "safeOfficeOpener.openSafely",
        "title": "Open Safely in External App"
      }
    ],
    "menus": {
      "explorer/context": [
        {
          "command": "safeOfficeOpener.openSafely",
          "when": "resourceScheme == file && resourceExtname =~ /\\.(xlsx|xlsm|xls|docx|doc|pptx|ppt|pdf|lnk)$/i",
          "group": "navigation"
        }
      ]
    }
  }
}
```

---

## 実装要件

### 言語・環境

- TypeScript
- Node.js
- VS Code Extension API

### 推奨構成

```text
safe-office-opener/
  package.json
  tsconfig.json
  src/
    extension.ts
    allowlist.ts
    openExternal.ts
    shortcutResolver.ts
    validators.ts
  test/
    extension.test.ts
    validators.test.ts
    shortcutResolver.test.ts
  README.md
  SPEC.md
```

---

## 主要モジュール責務

### `allowlist.ts`

- 許可拡張子を定義する。
- 通常ファイル用 allowlist と、ショートカットリンク先用 allowlist を分離する。

```ts
export const OPENABLE_EXTENSIONS = new Set([
  ".xlsx",
  ".xlsm",
  ".xls",
  ".docx",
  ".doc",
  ".pptx",
  ".ppt",
  ".pdf",
  ".lnk",
]);

export const SHORTCUT_TARGET_FILE_EXTENSIONS = new Set([
  ".xlsx",
  ".xlsm",
  ".xls",
  ".docx",
  ".doc",
  ".pptx",
  ".ppt",
  ".pdf",
]);
```

---

### `validators.ts`

以下を検証する。

- URI scheme が `file` であること
- ファイルが存在すること
- 通常ファイルまたはフォルダであること
- 拡張子が allowlist に含まれること
- `.lnk` のリンク先が許可対象であること

---

### `openExternal.ts`

`vscode.env.openExternal()` をラップする。

- コマンド実行は行わない。
- ファイルパスをシェル文字列に連結しない。
- `Uri.file()` で URI を作成する。

---

### `shortcutResolver.ts`

`.lnk` の TargetPath を解決する。

返却する情報は原則として以下のみ。

```ts
export type ShortcutTarget = {
  targetPath: string;
};
```

`arguments`、`workingDirectory`、`iconLocation` は返却しない、または返却しても呼び出し側で使用しない。

---

## エラー処理

以下の場合は `vscode.window.showErrorMessage()` で通知する。

| ケース | メッセージ例 |
|---|---|
| URI が file scheme ではない | Only local files are supported. |
| 拡張子が非対応 | This file type is not supported. |
| ファイルが存在しない | The selected file does not exist. |
| `.lnk` の TargetPath が空 | This shortcut has no target path. |
| `.lnk` の TargetPath が存在しない | This shortcut target does not exist. |
| `.lnk` の TargetPath が許可対象外 | This shortcut target is not allowed by Safe Office/PDF Opener. |
| `openExternal()` が失敗 | Failed to open the file in an external application. |

---

## セキュリティ要件

### 禁止事項

以下を実装してはならない。

- 任意コマンド実行
- 任意アプリケーションパス設定
- 任意引数設定
- `.lnk` の Arguments 実行
- `.lnk` 自体の実行
- `child_process.exec()` の使用
- `powershell -Command` にファイルパスを文字列連結して渡す処理
- `cmd /c start` にファイルパスを文字列連結して渡す処理
- URL ショートカット `.url` の対応
- `http:` / `https:` / `vscode-remote:` などの URI を開く処理

---

### 許可事項

以下は許可する。

- `vscode.env.openExternal(vscode.Uri.file(path))`
- Node.js の `fs.stat` / `fs.access` による存在確認
- `.lnk` の TargetPath 読み取り
- TargetPath の allowlist 検証

---

## 設定項目

初期版では設定項目を提供しない。

理由:

- 設定項目を増やすと攻撃面が増える。
- 任意コマンドや任意拡張子を許可すると、安全性が下がる。
- 本拡張機能の目的は、限定された安全な外部起動である。

将来的に設定を追加する場合も、任意コマンド指定は追加しない。

---

## テスト要件

### 単体テスト

以下をテストする。

- 許可拡張子が正しく判定されること
- 大文字拡張子も小文字化されて許可されること
- 非対応拡張子が拒否されること
- `.lnk` のリンク先が許可拡張子の場合に許可されること
- `.lnk` のリンク先が `.exe` の場合に拒否されること
- `.lnk` のリンク先が `.bat` の場合に拒否されること
- `.lnk` のリンク先が URL の場合に拒否されること
- 存在しない TargetPath が拒否されること

### 結合テスト

可能であれば VS Code Extension Test を用いて以下を確認する。

- Explorer コンテキストメニューに対象拡張子でコマンドが表示されること
- 非対象拡張子ではコマンドが表示されないこと
- コマンド実行時に `vscode.env.openExternal()` が呼ばれること

### 手動テスト

Windows 環境で以下を確認する。

| ファイル | 期待結果 |
|---|---|
| `.xlsx` | Excel または既定アプリで開く |
| `.xlsm` | Excel または既定アプリで開く |
| `.docx` | Word または既定アプリで開く |
| `.pptx` | PowerPoint または既定アプリで開く |
| `.pdf` | 既定 PDF ビューアで開く |
| `.lnk` → `.xlsx` | リンク先 Excel ファイルが開く |
| `.lnk` → フォルダ | リンク先フォルダが開く |
| `.lnk` → `.exe` | 拒否される |
| `.lnk` → `.bat` | 拒否される |
| `.lnk` → URL | 拒否される |

---

## README に記載する内容

README には以下を記載する。

- この拡張機能の目的
- 対応拡張子
- `.lnk` は TargetPath のみ開くこと
- `.lnk` の Arguments は実行しないこと
- 任意コマンド実行機能はないこと
- `vscode.env.openExternal()` を使うこと
- Windows ローカル環境を主対象とすること
- 既定アプリの関連付けに従って開くこと

---

## ライセンス

推奨ライセンス:

```text
MIT License
```

ただし、`.lnk` 解決ライブラリを採用する場合は、そのライブラリのライセンスと依存関係を確認すること。

---

## 完了条件

初期版の完了条件は以下とする。

- VS Code Explorer の右クリックメニューから対象ファイルを開ける。
- Office 文書、PDF を `vscode.env.openExternal()` で開ける。
- `.lnk` の TargetPath のみを解決して開ける。
- `.lnk` の Arguments は実行されない。
- `.lnk` のリンク先が `.exe`、`.bat`、`.cmd`、`.ps1`、URL の場合は拒否される。
- 任意コマンド実行機能が存在しない。
- allowlist 外の拡張子は開けない。
- README に安全性方針が明記されている。
- SPEC.md がリポジトリに含まれている。
