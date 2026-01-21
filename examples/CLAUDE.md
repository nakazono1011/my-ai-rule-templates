# プロジェクト CLAUDE.md のサンプル

これはプロジェクトレベルの CLAUDE.md ファイルのサンプルです。プロジェクトのルートディレクトリに配置してください。

## プロジェクト概要

[プロジェクトの簡単な説明 - 機能、技術スタック]

## 重要なルール

### 1. コード構成

- 少数の大きなファイルより、多数の小さなファイルを優先
- 高凝集、低結合
- 1ファイルあたり200-400行が標準、最大800行まで
- 種類別ではなく、機能/ドメイン別に整理

### 2. コードスタイル

- コード、コメント、ドキュメントに絵文字を使用しない
- 常にイミュータブル - オブジェクトや配列を変更しない
- 本番コードに console.log を含めない
- try/catch による適切なエラーハンドリング
- Zod などによる入力バリデーション

### 3. テスト

- TDD: テストを先に書く
- 最低80%のカバレッジ
- ユーティリティにはユニットテスト
- APIにはインテグレーションテスト
- 重要なフローにはE2Eテスト

### 4. セキュリティ

- シークレットをハードコードしない
- 機密データには環境変数を使用
- すべてのユーザー入力をバリデーション
- パラメータ化クエリのみ使用
- CSRF対策を有効化

## ファイル構造

```
src/
|-- app/              # Next.js app router
|-- components/       # Reusable UI components
|-- hooks/            # Custom React hooks
|-- lib/              # Utility libraries
|-- types/            # TypeScript definitions
```

## 主要パターン

### APIレスポンス形式

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
}
```

### エラーハンドリング

```typescript
try {
  const result = await operation()
  return { success: true, data: result }
} catch (error) {
  console.error('Operation failed:', error)
  return { success: false, error: 'User-friendly message' }
}
```

## 環境変数

```bash
# Required
DATABASE_URL=
API_KEY=

# Optional
DEBUG=false
```

## 利用可能なコマンド

- `/tdd` - テスト駆動開発ワークフロー
- `/plan` - 実装計画の作成
- `/code-review` - コード品質レビュー
- `/build-fix` - ビルドエラーの修正

## Gitワークフロー

- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`
- mainブランチに直接コミットしない
- PRにはレビューが必要
- マージ前にすべてのテストをパス
