# 貢献ガイドライン

ありがとうございます！改善提案・誤植修正・追加ネタなど歓迎です。

## 進め方
1. Issue で提案/相談（任意）
2. フォークしてブランチ作成（例: `feat/add-restore-examples`）
3. 変更をコミット
4. PR を作成（スクリーンショットや理由があると助かります）

## スタイル
- 例は **新コマンド（`switch` / `restore`）優先**、旧コマンドは補足扱い
- コードブロックは `bash` フェンス
- ファイルパスやブランチ名は `<placeholder>` で表記
- なるべく短く・実務で再利用しやすい形で

## コミットメッセージ
- 可能なら [Conventional Commits](https://www.conventionalcommits.org/) 風に
  - 例: `feat: add alias examples for switch/restore`
  - 例: `fix: correct example for git log -p -- <path>`

## チェック（任意）
- Markdown Lint を通す（GitHub Actions でも自動チェック）
