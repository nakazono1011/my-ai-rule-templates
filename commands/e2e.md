---
description: Playwrightを使用してE2Eテストを生成・実行します。テストジャーニーの作成、テストの実行、スクリーンショット/ビデオ/トレースのキャプチャ、アーティファクトのアップロードを行います。
---

# E2Eコマンド

このコマンドは**e2e-runner**エージェントを呼び出し、Playwrightを使用してエンドツーエンドテストを生成、保守、実行します。

## このコマンドの機能

1. **テストジャーニーの生成** - ユーザーフローのPlaywrightテストを作成
2. **E2Eテストの実行** - 複数のブラウザでテストを実行
3. **アーティファクトのキャプチャ** - 失敗時のスクリーンショット、ビデオ、トレース
4. **結果のアップロード** - HTMLレポートとJUnit XML
5. **不安定なテストの特定** - 不安定なテストを隔離

## 使用するタイミング

`/e2e`を使用する場合：
- 重要なユーザージャーニーのテスト（ログイン、取引、支払い）
- マルチステップフローがエンドツーエンドで機能することの検証
- UIインタラクションとナビゲーションのテスト
- フロントエンドとバックエンド間の統合検証
- 本番デプロイの準備

## 動作の仕組み

e2e-runnerエージェントは以下を行います：

1. **ユーザーフローを分析**し、テストシナリオを特定
2. **Playwrightテストを生成**（Page Object Modelパターンを使用）
3. **複数のブラウザでテストを実行**（Chrome、Firefox、Safari）
4. **失敗をキャプチャ**（スクリーンショット、ビデオ、トレース）
5. **レポートを生成**（結果とアーティファクト）
6. **不安定なテストを特定**し、修正を推奨

## 使用例

```
User: /e2e Test the market search and view flow

Agent (e2e-runner):
# E2E Test Generation: Market Search and View Flow

## Test Scenario Identified

**User Journey:** Market Search → View Results → Click Market → View Details

**Test Coverage:**
1. Navigate to markets page
2. Perform semantic search
3. Verify search results
4. Click on first result
5. Verify market details page loads
6. Verify chart renders

## Generated Test Code

```typescript
// tests/e2e/markets/search-and-view.spec.ts
import { test, expect } from '@playwright/test'
import { MarketsPage } from '../../pages/MarketsPage'
import { MarketDetailsPage } from '../../pages/MarketDetailsPage'

test.describe('Market Search and View Flow', () => {
  test('user can search markets and view details', async ({ page }) => {
    // 1. Navigate to markets page
    const marketsPage = new MarketsPage(page)
    await marketsPage.goto()

    // Verify page loaded
    await expect(page).toHaveTitle(/Markets/)
    await expect(page.locator('h1')).toContainText('Markets')

    // 2. Perform semantic search
    await marketsPage.searchMarkets('election')

    // Wait for API response
    await page.waitForResponse(resp =>
      resp.url().includes('/api/markets/search') && resp.status() === 200
    )

    // 3. Verify search results
    const marketCards = marketsPage.marketCards
    await expect(marketCards.first()).toBeVisible()
    const resultCount = await marketCards.count()
    expect(resultCount).toBeGreaterThan(0)

    // Take screenshot of search results
    await page.screenshot({ path: 'artifacts/search-results.png' })

    // 4. Click on first result
    const firstMarketTitle = await marketCards.first().textContent()
    await marketCards.first().click()

    // 5. Verify market details page loads
    await expect(page).toHaveURL(/\/markets\/[a-z0-9-]+/)

    const detailsPage = new MarketDetailsPage(page)
    await expect(detailsPage.marketName).toBeVisible()
    await expect(detailsPage.marketDescription).toBeVisible()

    // 6. Verify chart renders
    await expect(detailsPage.priceChart).toBeVisible()

    // Verify market name matches
    const detailsTitle = await detailsPage.marketName.textContent()
    expect(detailsTitle?.toLowerCase()).toContain(
      firstMarketTitle?.toLowerCase().substring(0, 20) || ''
    )

    // Take screenshot of market details
    await page.screenshot({ path: 'artifacts/market-details.png' })
  })

  test('search with no results shows empty state', async ({ page }) => {
    const marketsPage = new MarketsPage(page)
    await marketsPage.goto()

    // Search for non-existent market
    await marketsPage.searchMarkets('xyznonexistentmarket123456')

    // Verify empty state
    await expect(page.locator('[data-testid="no-results"]')).toBeVisible()
    await expect(page.locator('[data-testid="no-results"]')).toContainText(
      /no.*results|no.*markets/i
    )

    const marketCount = await marketsPage.marketCards.count()
    expect(marketCount).toBe(0)
  })

  test('can clear search and see all markets again', async ({ page }) => {
    const marketsPage = new MarketsPage(page)
    await marketsPage.goto()

    // Initial market count
    const initialCount = await marketsPage.marketCards.count()

    // Perform search
    await marketsPage.searchMarkets('trump')
    await page.waitForLoadState('networkidle')

    // Verify filtered results
    const filteredCount = await marketsPage.marketCards.count()
    expect(filteredCount).toBeLessThan(initialCount)

    // Clear search
    await marketsPage.searchInput.clear()
    await page.waitForLoadState('networkidle')

    // Verify all markets shown again
    const finalCount = await marketsPage.marketCards.count()
    expect(finalCount).toBe(initialCount)
  })
})
```

## テストの実行

```bash
# Run the generated test
npx playwright test tests/e2e/markets/search-and-view.spec.ts

Running 3 tests using 3 workers

  ✓  [chromium] › search-and-view.spec.ts:5:3 › user can search markets and view details (4.2s)
  ✓  [chromium] › search-and-view.spec.ts:52:3 › search with no results shows empty state (1.8s)
  ✓  [chromium] › search-and-view.spec.ts:67:3 › can clear search and see all markets again (2.9s)

  3 passed (9.1s)

Artifacts generated:
- artifacts/search-results.png
- artifacts/market-details.png
- playwright-report/index.html
```

## テストレポート

```
╔══════════════════════════════════════════════════════════════╗
║                    E2E Test Results                          ║
╠══════════════════════════════════════════════════════════════╣
║ Status:     ✅ ALL TESTS PASSED                              ║
║ Total:      3 tests                                          ║
║ Passed:     3 (100%)                                         ║
║ Failed:     0                                                ║
║ Flaky:      0                                                ║
║ Duration:   9.1s                                             ║
╚══════════════════════════════════════════════════════════════╝

Artifacts:
📸 Screenshots: 2 files
📹 Videos: 0 files (only on failure)
🔍 Traces: 0 files (only on failure)
📊 HTML Report: playwright-report/index.html

View report: npx playwright show-report
```

✅ E2Eテストスイートは CI/CD 統合の準備ができました！
```

## テストアーティファクト

テスト実行時に以下のアーティファクトがキャプチャされます：

**すべてのテストで：**
- タイムラインと結果を含むHTMLレポート
- CI統合用のJUnit XML

**失敗時のみ：**
- 失敗状態のスクリーンショット
- テストのビデオ録画
- デバッグ用トレースファイル（ステップバイステップの再生）
- ネットワークログ
- コンソールログ

## アーティファクトの表示

```bash
# View HTML report in browser
npx playwright show-report

# View specific trace file
npx playwright show-trace artifacts/trace-abc123.zip

# Screenshots are saved in artifacts/ directory
open artifacts/search-results.png
```

## 不安定なテストの検出

テストが断続的に失敗する場合：

```
⚠️  FLAKY TEST DETECTED: tests/e2e/markets/trade.spec.ts

Test passed 7/10 runs (70% pass rate)

Common failure:
"Timeout waiting for element '[data-testid="confirm-btn"]'"

Recommended fixes:
1. Add explicit wait: await page.waitForSelector('[data-testid="confirm-btn"]')
2. Increase timeout: { timeout: 10000 }
3. Check for race conditions in component
4. Verify element is not hidden by animation

Quarantine recommendation: Mark as test.fixme() until fixed
```

## ブラウザ設定

デフォルトでテストは複数のブラウザで実行されます：
- ✅ Chromium（デスクトップChrome）
- ✅ Firefox（デスクトップ）
- ✅ WebKit（デスクトップSafari）
- ✅ モバイルChrome（オプション）

ブラウザを調整するには`playwright.config.ts`で設定してください。

## CI/CD統合

CIパイプラインに追加：

```yaml
# .github/workflows/e2e.yml
- name: Install Playwright
  run: npx playwright install --with-deps

- name: Run E2E tests
  run: npx playwright test

- name: Upload artifacts
  if: always()
  uses: actions/upload-artifact@v3
  with:
    name: playwright-report
    path: playwright-report/
```

## PMX固有の重要フロー

PMXでは、これらのE2Eテストを優先してください：

**🔴 致命的（常にパスする必要あり）：**
1. ユーザーがウォレットを接続できる
2. ユーザーがマーケットを閲覧できる
3. ユーザーがマーケットを検索できる（セマンティック検索）
4. ユーザーがマーケット詳細を表示できる
5. ユーザーが取引を行える（テスト資金で）
6. マーケットが正しく解決される
7. ユーザーが資金を引き出せる

**🟡 重要：**
1. マーケット作成フロー
2. ユーザープロフィールの更新
3. リアルタイム価格更新
4. チャートのレンダリング
5. マーケットのフィルターとソート
6. モバイルレスポンシブレイアウト

## ベストプラクティス

**すべきこと：**
- ✅ 保守性のためにPage Object Modelを使用する
- ✅ セレクターにdata-testid属性を使用する
- ✅ 任意のタイムアウトではなくAPIレスポンスを待機する
- ✅ 重要なユーザージャーニーをエンドツーエンドでテストする
- ✅ mainにマージする前にテストを実行する
- ✅ テスト失敗時にアーティファクトを確認する

**すべきでないこと：**
- ❌ 脆弱なセレクターを使用する（CSSクラスは変更される可能性がある）
- ❌ 実装の詳細をテストする
- ❌ 本番環境に対してテストを実行する
- ❌ 不安定なテストを無視する
- ❌ 失敗時のアーティファクト確認をスキップする
- ❌ すべてのエッジケースをE2Eでテストする（ユニットテストを使用する）

## 重要な注意事項

**PMXにおける致命的事項：**
- 実際のお金を含むE2Eテストは、テストネット/ステージングのみで実行する必要があります
- 本番環境に対して取引テストを実行しないでください
- 金融テストには`test.skip(process.env.NODE_ENV === 'production')`を設定してください
- 少額のテスト資金のみを持つテストウォレットを使用してください

## 他のコマンドとの連携

- テストする重要なジャーニーを特定するには`/plan`を使用
- ユニットテスト（より高速で詳細）には`/tdd`を使用
- 統合テストとユーザージャーニーテストには`/e2e`を使用
- テストの品質を確認するには`/code-review`を使用

## 関連エージェント

このコマンドは以下にある`e2e-runner`エージェントを呼び出します：
`~/.claude/agents/e2e-runner.md`

## クイックコマンド

```bash
# Run all E2E tests
npx playwright test

# Run specific test file
npx playwright test tests/e2e/markets/search.spec.ts

# Run in headed mode (see browser)
npx playwright test --headed

# Debug test
npx playwright test --debug

# Generate test code
npx playwright codegen http://localhost:3000

# View report
npx playwright show-report
```
