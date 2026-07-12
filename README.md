# devcontainers

devcontainer 雛形と言語別設定ファイルの集約リポジトリ。必要なファイルを対象プロジェクトのルートにコピーして使う。

## 1. 方針

- devcontainer は言語非依存の `base` 1 つのみを管理する。言語別・アプリ形式別の devcontainer は管理が煩雑になるため置かない。言語固有のツールや設定は、コピー後に各プロジェクトの `Dockerfile` や `devcontainer.json` に追記する。
- `.editorconfig`、`.gitignore`、lint/format 設定は言語ごとに管理する。アプリ形式(web / cli 等)では分けない。

## 2. 構成

```
devcontainers/
├─ base/
│  ├─ .devcontainer/
│  │  ├─ devcontainer.json   # VS Code 設定・拡張機能・feature
│  │  ├─ Dockerfile          # ベースイメージ + 共通ツール
│  │  └─ docker-compose.yml  # dev サービス定義(DB 等は含めない)
│  ├─ .editorconfig          # 言語非依存の汎用ベース
│  └─ .gitignore             # 言語非依存の汎用ベース
├─ csharp/
│  ├─ .editorconfig          # C# のコードスタイル・命名規則を含む
│  └─ .gitignore
├─ go/
│  ├─ .editorconfig          # Go のタブインデント設定を含む
│  └─ .gitignore
├─ python/
│  ├─ .editorconfig
│  ├─ .gitignore
│  └─ ruff.toml              # ruff の lint/format 設定
└─ typescript/
   ├─ .editorconfig
   ├─ .gitignore
   └─ biome.json             # biome の lint/format 設定
```

## 3. base devcontainer

ベースイメージは `mcr.microsoft.com/devcontainers/base:ubuntu`(最新 LTS)。タグは `base/.devcontainer/Dockerfile` の `FROM` で変更できる。

共通ツールを次の方法で導入する。

- **gh**: devcontainer feature(`ghcr.io/devcontainers/features/github-cli:1`)で導入する。
- **lazygit**: 公式 feature が存在しないため、`Dockerfile` 内で GitHub Release から CPU アーキテクチャ(x86_64 / arm64)を判定して導入する。
- **Claude Code**: devcontainer feature は Node.js への依存が生じるため使わず、`devcontainer.json` の `postCreateCommand` でネイティブインストーラ(`curl -fsSL https://claude.ai/install.sh | bash`)を実行して導入する。インストール先 `~/.local/bin` を `remoteEnv` の `PATH` に追加している。

共通拡張機能として次を設定している。

- `editorconfig.editorconfig`(EditorConfig)
- `mhutchie.git-graph`(Git Graph)
- `usernamehw.errorlens`(Error Lens)

## 4. 言語別の lint/format

- **TypeScript**: biome(`typescript/biome.json`)
- **Python**: ruff(`python/ruff.toml`)
- **C#**: `.editorconfig`(`csharp/.editorconfig` にコードスタイル・命名規則を定義)
- **Go**: gofmt / goimports(設定ファイル不要。`go/.editorconfig` でタブインデントを定義)

lint/format 用の VS Code 拡張機能(`biomejs.biome`、`charliermarsh.ruff`、`golang.go` 等)は、各プロジェクトの `devcontainer.json` に追記する。

## 5. 使い方

1. `base/.devcontainer/` を対象プロジェクトのルートにコピーする。
2. 言語別ディレクトリの設定ファイル(`.editorconfig`、`.gitignore`、lint 設定)をプロジェクトのルートにコピーする。言語非依存のプロジェクトは `base/` の `.editorconfig` と `.gitignore` を使う。
3. 言語のツールチェーンを `Dockerfile` または `devcontainer.json` の `features` に追記する。
4. VS Code で「Reopen in Container」を実行する(要 Dev Containers 拡張・Docker)。初回はイメージビルドが走る。

## 6. 注意事項

- ワークスペースはコンテナ内 `/workspace` にマウントされる(`docker-compose.yml` の `../:/workspace:cached`)。
- web アプリ等でポート転送が必要な場合は、`docker-compose.yml` の `ports` と `devcontainer.json` の `forwardPorts` に追記する。
- DB 等の追加サービスが必要な場合は `docker-compose.yml` に service を追記する。
