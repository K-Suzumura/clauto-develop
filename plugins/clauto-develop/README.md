# clauto-develop Plugin

Claude Code を活用した AI 駆動開発フレームワークのプラグインパッケージです。

## 含まれるコンポーネント

### サブエージェント（10種類）

| エージェント | 役割 |
|-------------|------|
| req-analyzer | 要求分析者 - ユーザーの曖昧な要求を具体的な要件に変換 |
| spec-planner | 仕様策定者 - 要件を実装可能な技術仕様に変換 |
| tech-leader | テックリード - 技術選定とアーキテクチャの方針決定 |
| backend-designer | バックエンド設計者 - API設計、DB設計 |
| frontend-designer | フロントエンド設計者 - UI/UX設計、コンポーネント設計 |
| code-builder | コードビルダー - 設計仕様に基づいた実装 |
| code-reviewer | コードレビューワー - 品質、セキュリティ、パフォーマンス評価 |
| code-debugger | コードデバッガー - バグの特定と根本原因分析 |
| code-guide | コードガイド - コードベースの案内と実装箇所の特定 |
| general-purpose | 汎用エージェント - 複雑なマルチステップタスクに対応 |

### スキル（9種類）

| スキル | 説明 |
|-------|------|
| architecture-reviewer | アーキテクチャレビュー |
| coding-standards | コーディング規約 |
| debug-triage | デバッグトリアージ |
| dependency-change-reviewer | 依存パッケージ変更レビュー |
| pr-reviewer | PRレビュー |
| release-notes-writer | リリースノート作成 |
| security-baseline | セキュリティベースライン |
| spec-reviewer | 仕様書レビュー |
| test-author | テスト作成 |

### コマンド（13種類）

| コマンド | 説明 |
|---------|------|
| /spec:init | 仕様書テンプレート生成 |
| /spec:review | 仕様書レビュー |
| /plan:make | 実装計画作成 |
| /impl:run | タスク実装実行 |
| /qa:full | フルテスト実行 |
| /git:commit | 規約準拠コミット |
| /git:pr | PR作成 |
| /git:branch | ブランチ作成 |
| /gh:commit-push-pr | commit→push→PR統合ワークフロー |
| /fixup-from-pr-comments | PRコメント対応 |
| /refactor:cleanup | リファクタリング |
| /session:compact-smart | セッションコンパクト |
| /ultrathink | 深い思考モード |

## ライセンス

Apache License 2.0
