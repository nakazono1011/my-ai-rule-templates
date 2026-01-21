# プラグインとマーケットプレイス

プラグインはClaude Codeに新しいツールや機能を追加します。このガイドではインストール方法のみを説明します。使用するタイミングや理由については[詳細記事](https://x.com/affaanmustafa/status/2012378465664745795)をご覧ください。

---

## マーケットプレイス

マーケットプレイスはインストール可能なプラグインのリポジトリです。

### マーケットプレイスの追加

```bash
# Add official Anthropic marketplace
claude plugin marketplace add https://github.com/anthropics/claude-plugins-official

# Add community marketplaces
claude plugin marketplace add https://github.com/mixedbread-ai/mgrep
```

### おすすめのマーケットプレイス

| マーケットプレイス | ソース |
|-------------|--------|
| claude-plugins-official | `anthropics/claude-plugins-official` |
| claude-code-plugins | `anthropics/claude-code` |
| Mixedbread-Grep | `mixedbread-ai/mgrep` |

---

## プラグインのインストール

```bash
# Open plugins browser
/plugins

# Or install directly
claude plugin install typescript-lsp@claude-plugins-official
```

### おすすめのプラグイン

**開発:**
- `typescript-lsp` - TypeScriptインテリジェンス
- `pyright-lsp` - Python型チェック
- `hookify` - 会話形式でフックを作成
- `code-simplifier` - コードのリファクタリング

**コード品質:**
- `code-review` - コードレビュー
- `pr-review-toolkit` - PR自動化
- `security-guidance` - セキュリティチェック

**検索:**
- `mgrep` - 強化された検索（ripgrepより優秀）
- `context7` - リアルタイムドキュメント検索

**ワークフロー:**
- `commit-commands` - Gitワークフロー
- `frontend-design` - UIパターン
- `feature-dev` - 機能開発

---

## クイックセットアップ

```bash
# Add marketplaces
claude plugin marketplace add https://github.com/anthropics/claude-plugins-official
claude plugin marketplace add https://github.com/mixedbread-ai/mgrep

# Open /plugins and install what you need
```

---

## プラグインファイルの場所

```
~/.claude/plugins/
|-- cache/                    # ダウンロードしたプラグイン
|-- installed_plugins.json    # インストール済みリスト
|-- known_marketplaces.json   # 追加したマーケットプレイス
|-- marketplaces/             # マーケットプレイスデータ
```
