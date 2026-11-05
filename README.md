# devcontainers

devcontainer の雛形と、言語ごとの `.editorconfig`・`.gitignore`・lint の設定を集め、対象のプロジェクトへコピーして使うリポジトリ。

## 動作環境

- Docker
- VS Code と Dev Containers 拡張

## 使い方

1. `base/.devcontainer/` を対象のプロジェクトのルートにコピーする。
2. 言語のディレクトリ(`csharp`・`go`・`python`・`typescript`)の設定ファイルを、プロジェクトのルートにコピーする。言語に依存しないプロジェクトでは、`base/` の `.editorconfig` と `.gitignore` をコピーする。
3. 言語のツールチェーンを、コピーした `Dockerfile` に追記する。
4. VS Code で「Reopen in Container」を実行する。初回はイメージのビルドが実行される。

ポートの転送やデータベースを追加するときは、[devcontainers の方針と構成](docs/design.md) の「コピーした後の変更」に従って設定を追記する。
