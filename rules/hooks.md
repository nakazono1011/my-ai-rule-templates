# フックシステム

## フックの種類

- **PreToolUse**: ツール実行前 (検証、パラメータ修正)
- **PostToolUse**: ツール実行後 (自動フォーマット、チェック)
- **Stop**: セッション終了時 (最終検証)

## 現在のフック (`~/.claude/settings.json` 内)

### PreToolUse
- **tmux reminder**: 長時間実行コマンド（npm, pnpm, yarn, cargo 等）に対してtmuxを提案
- **git push review**: プッシュ前にレビューのためにZedを開く
- **doc blocker**: 不要な .md/.txt ファイルの作成をブロック

### PostToolUse
- **PR creation**: PRのURLとGitHub Actionsのステータスをログ出力
- **Prettier**: 編集後のJS/TSファイルを自動フォーマット
- **TypeScript check**: .ts/.tsx ファイル編集後にtscを実行
- **console.log warning**: 編集されたファイルの console.log について警告

### Stop
- **console.log audit**: セッション終了前に変更されたすべてのファイルの console.log をチェック

## 権限の自動承認

注意して使用してください:
- 信頼できる明確な計画に対して有効化
- 探索的な作業では無効化
- dangerously-skip-permissions フラグは絶対に使用しない
- 代わりに `~/.claude.json` で `allowedTools` を設定する

## TodoWrite ベストプラクティス

TodoWriteツールを使用して:
- 複数ステップのタスクの進捗を追跡
- 指示の理解を確認
- リアルタイムの方向修正を可能にする
- 詳細な実装ステップを表示

Todoリストが明らかにするもの:
- 順序が正しくないステップ
- 欠落している項目
- 余分で不要な項目
- 間違った粒度
- 要件の誤解釈
