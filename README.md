# devcontainers

プロジェクト種別ごとの devcontainer 雛形集。各フォルダを丸ごと対象プロジェクトの
ルートにコピーして使う（`.devcontainer/` 以下と、必要に応じてルートの設定ファイル）。

## 構成

| 種別 | 言語 | パス | 用途 |
| --- | --- | --- | --- |
| base | 言語非依存 | `base/` | 言語/プロジェクトに依存しない汎用ベース（共通ツールのみ） |
| web | TypeScript (full) | `web/typescript/` | Node ベースの汎用フロント/フルスタック（FW 非依存） |
| web | C# (backend) | `web/csharp/` | ASP.NET Core 等のバックエンド |
| web | Go (backend) | `web/go/` | Go 製バックエンド |
| web | Python (backend) | `web/python/` | FastAPI/Django 等のバックエンド |
| cli/library | C# | `cli/csharp/` | CLI / ライブラリ |
| cli/library | Go | `cli/go/` | CLI / ライブラリ |
| cli/library | Python | `cli/python/` | CLI / ライブラリ |

各雛形は次のファイルで構成される。

```
<template>/
├─ .devcontainer/
│  ├─ devcontainer.json   # VS Code 設定・拡張機能・feature
│  ├─ Dockerfile          # ベースイメージ + 共通ツール
│  └─ docker-compose.yml  # dev サービス定義（DB 等は含めない）
├─ biome.json             # TypeScript のみ
├─ ruff.toml              # Python のみ
└─ .editorconfig          # C# のみ
```

## ベースイメージ（devcontainer 公式イメージ）

バージョンは各 `Dockerfile` の `FROM` で変更できる。

| 言語 | イメージ | タグ |
| --- | --- | --- |
| 言語非依存 (base) | `mcr.microsoft.com/devcontainers/base` | `ubuntu`（最新 LTS） |
| TypeScript | `mcr.microsoft.com/devcontainers/typescript-node` | `4-24`（Node 24） |
| C# | `mcr.microsoft.com/devcontainers/dotnet` | `10.0`（.NET 10 LTS） |
| Go | `mcr.microsoft.com/devcontainers/go` | `1.25` |
| Python | `mcr.microsoft.com/devcontainers/python` | `3.13` |

## 共通ツール

- **gh**: devcontainer feature（`ghcr.io/devcontainers/features/github-cli:1`）で導入。
- **lazygit**: 公式 feature が存在しないため、`Dockerfile` 内で GitHub Release から
  CPU アーキテクチャ（x86_64 / arm64）を判定して導入。
- **Claude Code**: devcontainer feature は Node.js への依存が生じるため使わず、
  `devcontainer.json` の `postCreateCommand` でネイティブインストーラ
  （`curl -fsSL https://claude.ai/install.sh | bash`）を実行して導入する。
  インストール先 `~/.local/bin` を `remoteEnv` の `PATH` に追加している
  （ユーザーの home に合わせる: 多くは `/home/vscode`、TypeScript は `/home/node`）。

## 共通拡張機能

- `mhutchie.git-graph`（Git Graph）
- `usernamehw.errorlens`（Error Lens）

## 言語別の lint/format

- **TypeScript**: biome（`biome.json`、`biomejs.biome` 拡張をフォーマッタに設定）
- **Python**: ruff（`ruff.toml`、`charliermarsh.ruff` 拡張をフォーマッタに設定）
- **C#**: `.editorconfig`（`editorconfig.editorconfig` 拡張、保存時フォーマット）
- **Go**: gofmt/goimports（`golang.go` 拡張、保存時に import 整理）

## 使い方

1. 対象プロジェクトのルートに、使いたい雛形フォルダの中身をコピーする。
   例: `web/python/.devcontainer/` と `web/python/ruff.toml` をプロジェクト直下へ。
2. VS Code で「Reopen in Container」を実行（要 Dev Containers 拡張・Docker）。
3. 初回はイメージビルドが走る。完了後、コンテナ内で開発できる。

### 注意

- ワークスペースはコンテナ内 `/workspace` にマウントされる
  （`docker-compose.yml` の `../:/workspace:cached`）。
- web 系はポートを転送している（TS: 3000/5173、C#/Go: 8080、Python: 8000）。
  アプリの待受ポートに合わせて `docker-compose.yml` と `devcontainer.json` を調整する。
- DB 等の追加サービスが必要な場合は `docker-compose.yml` に service を追記する。
