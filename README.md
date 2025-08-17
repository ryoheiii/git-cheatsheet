# Gitコマンド・チートシート（日本語）

## 目次

- [Gitコマンド・チートシート（日本語）](#gitコマンドチートシート日本語)
  - [目次](#目次)
  - [初期セットアップ \& リモート](#初期セットアップ--リモート)
  - [最初のPush（追跡設定）](#最初のpush追跡設定)
  - [変更の確認](#変更の確認)
  - [ステージングと取り消し（新/旧）](#ステージングと取り消し新旧)
  - [ファイルの追加・削除](#ファイルの追加削除)
  - [コミット](#コミット)
  - [取得・同期（fetch / pull）](#取得同期fetch--pull)
  - [ブランチ操作](#ブランチ操作)
  - [マージとリベース](#マージとリベース)
  - [コミット履歴の整理（インタラクティブrebase）](#コミット履歴の整理インタラクティブrebase)
  - [タグ](#タグ)
  - [スタッシュ（作業の一時避難）](#スタッシュ作業の一時避難)
  - [よく使うログの書式](#よく使うログの書式)
  - [便利エイリアス例](#便利エイリアス例)
  - [新旧コマンド 対応表](#新旧コマンド-対応表)
  - [注意点](#注意点)

---

## 初期セットアップ & リモート

```bash
git remote add origin <URL>   # リモート追加（例：GitHub）
git remote -v                 # 登録リモート確認
git remote rename <old> <new> # リモート名の変更
git remote show origin        # 追跡ブランチ等の詳細
```

- リモートは複数登録可（例：`origin` と `upstream`）。

---

## 最初のPush（追跡設定）

```bash
git push -u origin <branch>   # 例: git push -u origin main
git push                      # 2回目以降は短く
```

> 旧 `git push -u origin master` は、`main` 利用プロジェクトでは `git push -u origin main` へ。

---

## 変更の確認

```bash
git status
git diff                      # 未ステージの差分
git diff --staged             # ステージ済みの差分
git log --oneline             # 1行ログ
git log -p -- <path>          # ファイルの差分付きログ（-- を忘れない）
git log -n <count>            # 最新N件
git log --oneline --decorate --graph --all  # 流れを可視化
```

---

## ステージングと取り消し（新/旧）

**ワークツリーの変更取り消し**

- 旧: `git checkout -- <path>` / `git checkout -- .`
- 新(推奨): `git restore <path>` / `git restore .`

**ステージの変更取り消し**

- 旧: `git reset HEAD <path>`
- 新(推奨): `git restore --staged <path>` / `git restore --staged .`

---

## ファイルの追加・削除

```bash
git add <path>                # 追加
git rm <path>                 # 削除してステージ
git rm -r <dir>               # ディレクトリ再帰削除
git rm --cached <path>        # 追跡のみ外す（ファイルは残す）
```

---

## コミット

```bash
git commit -m "message"
git commit -v                 # 差分を見ながらメッセージ作成
git commit --amend            # 直前コミットの修正（共有後は原則NG）
```

---

## 取得・同期（fetch / pull）

```bash
git fetch origin              # 取得のみ（安全）
git branch -a                 # 全ブランチ表示（ローカル/リモート）

# pull（上流ブランチ設定済み前提）
git pull                      # 既定はmerge（設定でrebaseにも）
git pull --rebase             # 明示的にrebase

# 既定をrebaseに
git config --global pull.rebase true
git config branch.main.rebase true
```

> `git pull origin master` のように「名前直指定pull」は混乱の元。
> 原則：**上流設定して `git pull`** か **`fetch` の後に `merge/rebase`**。

---

## ブランチ操作

```bash
git branch <name>             # 作成だけ

# 作成して切替（旧→新）
git checkout -b <name>        # 旧
git switch -c <name>          # 新（推奨）

# 既存へ切替（旧→新）
git checkout <name>           # 旧
git switch <name>             # 新（推奨）
git switch -                  # 直前ブランチへ戻る

# リネーム/削除
git branch -m <new_name>
git branch -d <name>          # マージ済み前提
git branch -D <name>          # 強制削除

# リモートへ公開
git push -u origin <name>     # 初回は -u で追跡設定
```

**用語**

- `HEAD` … 今見ているコミット（通常は現在ブランチ先頭）
- ブランチ名は**最新コミットへのポインタ**

---

## マージとリベース

```bash
git merge <branch>            # 現在ブランチへ取り込む
git merge origin/main         # 追跡ブランチを取り込む

git rebase <upstream>         # カレントのコミットを付け替え
git pull --rebase             # 取得と同時に付け替え
```

- **公開（push）前のローカル整理**は rebase 推奨。
- **共有済み履歴への rebase は原則NG**。
- コンフリクトはマージでもリベースでも発生し得る。

---

## コミット履歴の整理（インタラクティブrebase）

```bash
git rebase -i HEAD~N
# エディタ内で:
#  - squash: まとめる
#  - fixup : メッセージ不要でまとめる
#  - edit  : そのコミットで止めて修正
```

---

## タグ

```bash
git tag -a <tag> -m "message" # 注釈付きタグ（推奨）
git show <tag>                # 表示
git push origin <tag>         # 個別送信
git push origin --tags        # 一括送信
git tag -d <tag>              # ローカル削除
git push origin :refs/tags/<tag>  # リモート削除
```

---

## スタッシュ（作業の一時避難）

```bash
git stash                     # 変更を隠す
git stash -u                  # 未追跡も含める
git stash push -m "msg"       # メッセージ付き
git stash list                # 一覧
git stash show -p stash@{1}   # 中身を確認
git stash apply [stash@{N}]   # 適用（stashは残る）
git stash apply --index       # ステージ状態も復元
git stash pop                 # 適用＋削除
git stash drop                # 1件削除
git stash clear               # 全削除（注意）
```

---

## よく使うログの書式

```bash
git log --oneline --decorate
git log --oneline --decorate --graph --all
```

---

## 便利エイリアス例

```bash
git config --global alias.ci "commit"
git config --global alias.st "status"
git config --global alias.br "branch"
git config --global alias.co "checkout"    # 旧コマンド系
git config --global alias.sw "switch"      # 新しい切替
git config --global alias.rs "restore"     # 新しい取り消し
git config --global alias.lg "log --oneline --decorate --graph --all"
```

---

## 新旧コマンド 対応表

| 目的                 | 旧                                 | 新（推奨）                                |
| -------------------- | ---------------------------------- | ----------------------------------------- |
| ブランチ作成+切替    | `git checkout -b <br>`           | `git switch -c <br>`                    |
| ブランチ切替         | `git checkout <br>`              | `git switch <br>`                       |
| ワークツリー変更取消 | `git checkout -- <path>` / `.` | `git restore <path>` / `.`            |
| ステージ取消         | `git reset HEAD <path>`          | `git restore --staged <path>`           |
| 初回push+追跡        | `git push -u origin master`      | `git push -u origin main`               |
| pull時rebase         | `git pull --rebase`              | `git config pull.rebase true`（既定化） |

---

## 注意点

- `git log -p index.html` は `git log -p -- index.html` として `--` を挟む（曖昧さ回避）。
- 共有済みコミットへの `--amend` / `rebase -i` は原則禁止。必要ならチーム合意の上で。
- 新規作成で main を既定にしたい場合：

  ```bash
  git config --global init.defaultBranch main
  ```

