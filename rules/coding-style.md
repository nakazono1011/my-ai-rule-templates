# コーディングスタイル

## 不変性 (重要)

常に新しいオブジェクトを作成し、決して変更（ミューテート）しないでください:

```javascript
// WRONG: Mutation
function updateUser(user, name) {
  user.name = name  // MUTATION!
  return user
}

// CORRECT: Immutability
function updateUser(user, name) {
  return {
    ...user,
    name
  }
}
```

## ファイル構成

多数の小さなファイル > 少数の大きなファイル:
- 高い凝集度、低い結合度
- 200-400行が標準、最大800行
- 大きなコンポーネントからユーティリティを抽出する
- 型ではなく機能/ドメインごとに整理する

## エラーハンドリング

常にエラーを包括的に処理してください:

```typescript
try {
  const result = await riskyOperation()
  return result
} catch (error) {
  console.error('Operation failed:', error)
  throw new Error('Detailed user-friendly message')
}
```

## 入力バリデーション

常にユーザー入力を検証してください:

```typescript
import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  age: z.number().int().min(0).max(150)
})

const validated = schema.parse(input)
```

## コード品質チェックリスト

作業完了とする前に:
- [ ] コードが読みやすく、適切な名前が付けられている
- [ ] 関数が小さい (<50行)
- [ ] ファイルが焦点を絞っている (<800行)
- [ ] 深いネストがない (>4レベル)
- [ ] 適切なエラーハンドリング
- [ ] console.log ステートメントがない
- [ ] ハードコードされた値がない
- [ ] ミューテーションがない（不変パターンを使用）
