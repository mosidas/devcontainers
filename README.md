# devcontainers

devcontainer と設定ファイルの雛形を集め、対象のプロジェクトへコピーして使うリポジトリ。

- `base/`: 言語に依存しない devcontainer と、`.editorconfig`・`.gitignore`
- `csharp/`・`go/`・`python/`・`typescript/`: 言語ごとの `.editorconfig`・`.gitignore`・lint の設定

## 使い方

1. Docker と、VS Code の Dev Containers 拡張をインストールする。
2. `base/.devcontainer/` を対象のプロジェクトのルートにコピーする。
3. 言語のディレクトリの設定ファイルを、プロジェクトのルートにコピーする。言語に依存しないプロジェクトでは、`base/` の `.editorconfig` と `.gitignore` をコピーする。
4. 言語のツールチェーンを、コピーした `Dockerfile` に追記する。
5. VS Code で「Reopen in Container」を実行する。初回はイメージのビルドが実行される。

ポートの転送やデータベースを追加するときは、devcontainers の方針と構成の [コピーした後の変更](docs/design.md#コピーした後の変更) に従って設定を追記する。
