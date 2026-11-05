# devcontainers の方針と構成

このリポジトリが何を管理し、base の devcontainer が何を導入するかを説明する。コピーして使う手順は [devcontainers の README](../README.md) に従う。

## 方針

- devcontainer は言語非依存の `base` 1 つのみを管理する。言語別・アプリ形式別の devcontainer は管理が煩雑になるため置かない。言語固有のツールや設定は、コピー後に各プロジェクトの `Dockerfile` や `devcontainer.json` に追記する。
- `.editorconfig`、`.gitignore`、lint/format 設定は言語ごとに管理する。アプリ形式(web / cli 等)では分けない。

## base の devcontainer

ベースイメージは `mcr.microsoft.com/devcontainers/base:ubuntu`(最新 LTS)。タグは `base/.devcontainer/Dockerfile` の `FROM` で変更できる。

共通ツールを次の方法で導入する。

- **gh**: devcontainer feature(`ghcr.io/devcontainers/features/github-cli:1`)で導入する。
- **lazygit**: 公式 feature が存在しないため、`Dockerfile` 内で GitHub Release から CPU アーキテクチャ(x86_64 / arm64)を判定して導入する。
- **Claude Code**: devcontainer feature は Node.js への依存が生じるため使わず、`devcontainer.json` の `postCreateCommand` でネイティブインストーラ(`curl -fsSL https://claude.ai/install.sh | bash`)を実行して導入する。インストール先 `~/.local/bin` を `remoteEnv` の `PATH` に追加している。

共通拡張機能として次を設定している。

- `editorconfig.editorconfig`(EditorConfig)
- `mhutchie.git-graph`(Git Graph)
- `usernamehw.errorlens`(Error Lens)

## 言語別の lint と format

- **TypeScript**: biome(`typescript/biome.json`)
- **Python**: ruff(`python/ruff.toml`)
- **C#**: `.editorconfig`(`csharp/.editorconfig` にコードスタイル・命名規則を定義)
- **Go**: gofmt / goimports(設定ファイル不要。`go/.editorconfig` でタブインデントを定義)

lint/format 用の VS Code 拡張機能(`biomejs.biome`、`charliermarsh.ruff`、`golang.go` 等)は、各プロジェクトの `devcontainer.json` に追記する。

## コピーした後の変更

- 言語のツールチェーンは、`Dockerfile` か `devcontainer.json` の `features` に追記する。
- ワークスペースはコンテナ内 `/workspace` にマウントされる(`docker-compose.yml` の `../:/workspace:cached`)。
- web アプリ等でポート転送が必要な場合は、`docker-compose.yml` の `ports` と `devcontainer.json` の `forwardPorts` に追記する。
- DB 等の追加サービスが必要な場合は `docker-compose.yml` に service を追記する。
