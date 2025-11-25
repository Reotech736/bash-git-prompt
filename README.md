# bash-git-prompt

[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/magicmonty/bash-git-prompt?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

Bashのプロンプトに現在のgitリポジトリの情報を表示するツールです。ブランチ名、リモートとの差分、ステージされたファイル数、変更されたファイル数などを表示できます。

このリポジトリは [magicmonty/bash-git-prompt](https://github.com/magicmonty/bash-git-prompt) をフォークしたものです。

## インストール方法

### Git cloneによるインストール

リポジトリをホームディレクトリにクローンします。

```bash
git clone --depth 1 https://github.com/Reotech736/bash-git-prompt.git ~/.bash-git-prompt
```

以下を `~/.bashrc` に追加します。

```bash
# bash-git-prompt setting
GIT_PROMPT_ONLY_IN_REPO=1                       # Git管理下のディレクトリでのみ表示
export GIT_PROMPT_THEME=Minimal_Reo_Cool        # テーマ設定
source ~/.bash-git-prompt/gitprompt.sh
```

設定を反映します。

```bash
source ~/.bashrc
```

## プロンプトの表示例

プロンプトは以下のように表示されます:

![Example prompt](gitprompt.png)

* `(master↑3|✚1)`: `master` ブランチにいて、リモートより3コミット先行、1ファイル変更(未ステージ)
* `(status|●2)`: `status` ブランチにいて、2ファイルがステージ済み
* `(master|✚7…)`: `master` ブランチにいて、7ファイル変更、未追跡ファイルあり
* `(master|✖2✚3)`: `master` ブランチにいて、2つのコンフリクト、3ファイル変更
* `(master|⚑2)`: `master` ブランチにいて、2つのstashエントリ
* `(experimental↓2↑3|✔)`: `experimental` ブランチで分岐中(リモート2コミット、ローカル3コミット)、リポジトリはクリーン
* `(:70c2952|✔)`: ブランチではなくコミットハッシュ `70c2952` を参照中、リポジトリはクリーン

## プロンプトの構造

デフォルトでは、プロンプトは以下の構造になっています:

```plaintext
(<ブランチ> <上流ブランチ> <ブランチ追跡>|<ローカルステータス>)
```

### 記号の意味

**ローカルステータスの記号:**
- `✔`: リポジトリがクリーン
- `●n`: ステージされたファイルが `n` 個
- `✖n`: マージコンフリクトが `n` 個
- `✖-n`: 削除待ちのステージ済みファイルが `n` 個
- `✚n`: 変更されているが未ステージのファイルが `n` 個
- `…n`: 未追跡ファイルが `n` 個
- `⚑n`: stashエントリが `n` 個

**上流ブランチ:**
- リモート追跡ブランチを表示
- デフォルトでは無効
- `GIT_PROMPT_SHOW_UPSTREAM=1` で有効化

**ブランチ追跡の記号:**
- `↑n`: リモートより `n` コミット先行
- `↓n`: リモートより `n` コミット遅れ
- `↓m↑n`: ブランチが分岐(リモート `m` コミット、ローカル `n` コミット)
- `L`: ローカルブランチ(リモート追跡なし)

**ブランチ記号:**
- ブランチ名がコロン `:` で始まる場合、それはブランチではなくハッシュを意味します

## 詳細な設定

### .bashrcの全設定例

```bash
# 最初に設定変数を定義
GIT_PROMPT_ONLY_IN_REPO=1

# GIT_PROMPT_FETCH_REMOTE_STATUS=0   # リモートステータスの取得を無効化
# GIT_PROMPT_IGNORE_SUBMODULES=1 # サブモジュールの変更検索を無効化
# GIT_PROMPT_WITH_VIRTUAL_ENV=0 # 仮想環境情報の表示を無効化
# GIT_PROMPT_VIRTUAL_ENV_AFTER_PROMPT=1 # 仮想環境情報をプロンプトとgitステータスの間に配置

# GIT_PROMPT_SHOW_UPSTREAM=1 # 上流追跡ブランチを表示
# GIT_PROMPT_SHOW_UNTRACKED_FILES=normal # 未追跡ファイルのカウント方法(no, normal, all)

# GIT_PROMPT_SHOW_CHANGED_FILES_COUNT=0 # 変更ファイル数の表示を無効化

# GIT_PROMPT_STATUS_COMMAND=gitstatus_pre-1.7.10.sh # Git 1.7.10より古いバージョンのサポート

# GIT_PROMPT_START=...    # カスタムプロンプト開始シーケンス
# GIT_PROMPT_END=...      # カスタムプロンプト終了シーケンス

# 最後にgitpromptスクリプトを読み込む
# GIT_PROMPT_THEME=Custom # カスタムテーマを使用(GIT_PROMPT_THEME_FILEで指定)
# GIT_PROMPT_THEME_FILE=~/.git-prompt-colors.sh
# GIT_PROMPT_THEME=Solarized # Solarizedカラースキーム用のテーマ
source ~/.bash-git-prompt/gitprompt.sh
```

### テーマ

設定の多くはテーマファイルに保存されています。テーマを選択するには、`<INSTALLDIR>/themes` にあるテーマ名から拡張子 `.bgptheme` を除いた名前を `GIT_PROMPT_THEME` 変数に設定します:

```bash
GIT_PROMPT_THEME=Solarized
```

`GIT_PROMPT_THEME` を `Custom` に設定すると、ホームディレクトリの `.git-prompt-colors.sh` が使用されます。

利用可能なテーマのリストは `git_prompt_list_themes` で表示できます(現在のテーマはハイライト表示されます)。

### パフォーマンスの最適化

未追跡ファイルが多数ある場合、`GIT_PROMPT_SHOW_UNTRACKED_FILES` 変数を `no` または `normal` に設定すると高速化できます:

```bash
GIT_PROMPT_SHOW_UNTRACKED_FILES=no
```

### リポジトリごとの設定

リポジトリのルートに `.bash-git-rc` ファイルを作成して、リポジトリごとに設定をカスタマイズできます:

```bash
# リモートステータスの取得を無効化
FETCH_REMOTE_STATUS=0

# このリポジトリを完全に無視
GIT_PROMPT_IGNORE=1

# 未追跡ファイルの表示を無効化
GIT_PROMPT_SHOW_UNTRACKED_FILES=no

# サブモジュールの変更検索を無効化
GIT_PROMPT_IGNORE_SUBMODULES=1
```

### 便利なコマンド

- `git_prompt_help`: ヘルプを表示
- `git_prompt_examples`: 使用例を表示
- `git_prompt_color_samples`: 利用可能な色のサンプルを表示
- `git_prompt_list_themes`: テーマ一覧を表示
- `git_prompt_toggle`: gitpromptの有効/無効を切り替え
- `git_prompt_reset`: 設定ファイルを再読み込み

## ライセンス

このコードは [BSD 2条項(NetBSD)ライセンス][license] の下で提供されています。

## クレジット

オリジナルの bash-git-prompt のメンテナは [Martin Gondermann][magicmonty] です。

このリポジトリは [magicmonty/bash-git-prompt](https://github.com/magicmonty/bash-git-prompt) をフォークしたものです。

## 類似プロジェクト

- [oh-my-bash](https://github.com/ohmybash/oh-my-bash)

[magicmonty]: http://blog.pagansoft.de/pages/about.html
[license]: https://github.com/magicmonty/bash-git-prompt/tree/master/LICENSE.txt
[homebrew]: http://brew.sh/
