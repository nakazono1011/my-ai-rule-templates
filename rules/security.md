# セキュリティガイドライン

## 必須のセキュリティチェック

コミット前（ANY commit）に:
- [ ] ハードコードされたシークレット（APIキー、パスワード、トークン）がない
- [ ] すべてのユーザー入力が検証されている
- [ ] SQLインジェクション防止（パラメータ化クエリ）
- [ ] XSS防止（サニタイズされたHTML）
- [ ] CSRF保護が有効
- [ ] 認証/認可が検証されている
- [ ] すべてのエンドポイントでのレート制限
- [ ] エラーメッセージが機密データを漏洩していない

## シークレット管理

```typescript
// NEVER: Hardcoded secrets
const apiKey = "sk-proj-xxxxx"

// ALWAYS: Environment variables
const apiKey = process.env.OPENAI_API_KEY

if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
```

## セキュリティ対応プロトコル

セキュリティ問題が見つかった場合:
1. 即座に停止する
2. **security-reviewer** エージェントを使用する
3. 続行する前にCRITICAL（重大）な問題を修正する
4. 露出したシークレットをローテーションする
5. コードベース全体で同様の問題をレビューする
