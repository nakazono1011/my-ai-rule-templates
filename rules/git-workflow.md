# Gitワークフロー

## コミットメッセージの形式

```
<type>: <description>

<optional body>
```

Types: feat, fix, refactor, docs, test, chore, perf, ci

注: Attribution（帰属表示）は `~/.claude/settings.json` によりグローバルに無効化されています。

## プルリクエストワークフロー

PR作成時:
1. 完全なコミット履歴を分析する（最新のコミットだけでなく）
2. `git diff [base-branch]...HEAD` を使用してすべての変更を確認する
3. 包括的なPRサマリを作成する
4. TODOを含むテスト計画を含める
5. 新しいブランチの場合は `-u` フラグを付けてプッシュする

## 機能実装ワークフロー

1. **まず計画する**
   - **planner** エージェントを使用して実装計画を作成する
   - 依存関係とリスクを特定する
   - フェーズに分割する

2. **TDDアプローチ**
   - **tdd-guide** エージェントを使用する
   - テストを先に書く (RED)
   - テストに合格するように実装する (GREEN)
   - リファクタリングする (IMPROVE)
   - 80%以上のカバレッジを確認する

3. **コードレビュー**
   - コードを書いた直後に **code-reviewer** エージェントを使用する
   - CRITICAL（重大）および HIGH（高）の問題に対処する
   - 可能な場合は MEDIUM（中）の問題を修正する

4. **コミット & プッシュ**
   - 詳細なコミットメッセージ
   - Conventional Commits 形式に従う
