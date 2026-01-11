# 推奨ディレクトリ設計

サブエージェントの参照範囲制限を効果的に機能させるための推奨ディレクトリ構造です。

> **注**: この構造は汎用的なテンプレートです。プロジェクトの技術スタック（言語、フレームワーク）に合わせて適宜調整してください。`[placeholder]` は具体的なディレクトリ名に置き換えてください。

## 基本構造

```
project/
├── CLAUDE.md                 # 共通憲法（最小限）
├── README.md                 # プロジェクト概要
│
├── .claude/                  # Claude設定
│   └── agents/               # サブエージェント定義
│       ├── req-analyzer.md
│       ├── spec-planner.md
│       ├── tech-leader.md
│       ├── frontend-designer.md
│       ├── backend-designer.md
│       ├── code-builder.md
│       ├── code-reviewer.md
│       ├── code-debugger.md
│       ├── code-guide.md
│       └── general-purpose.md
│
├── docs/                     # ドキュメント
│   ├── requirements/         # 要件（Planner用）
│   ├── api/                  # API仕様（Builder用）
│   └── architecture/         # アーキテクチャ（Designer用）
│
├── specs/                    # 仕様書（Planner/Designer出力）
│   ├── features/             # 機能仕様
│   └── designs/              # 設計書
│
├── src/                      # ソースコード
│   ├── [frontend]/           # フロントエンド関連（プロジェクトに応じた構成）
│   ├── [backend]/            # バックエンド関連（プロジェクトに応じた構成）
│   ├── api/                  # APIエンドポイント
│   ├── services/             # ビジネスロジック
│   ├── models/               # データモデル
│   └── [shared]/             # 共通モジュール
│
├── tests/                    # テストコード
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── database/                 # DB関連（backend）
│   └── migrations/
│
├── infra/                    # インフラ（参照制限）
│   └── [IaC tools]/          # 例: terraform/, k8s/, ansible/ 等
│
└── logs/                     # ログ（debugger用）
```

## 役割別の参照マトリクス

| ディレクトリ | Planner | Designer | Builder | Reviewer | Guide |
|-------------|---------|----------|---------|----------|-------|
| `.claude/` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `docs/` | ✅ | ✅ | 一部 | ✅ | ✅ |
| `specs/` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `src/` | ❌ | ✅ | ✅ | ✅ | ✅ |
| `tests/` | ❌ | ❌ | ✅ | ✅ | ✅ |
| `database/` | ❌ | backend | ✅ | ✅ | ✅ |
| `infra/` | ❌ | tech-leader | ❌ | ❌ | ❌ |
| `logs/` | ❌ | ❌ | ❌ | ✅ | ✅ |
| `.env*` | ❌ | ❌ | ❌ | ❌ | ❌ |

## 役割グループと担当エージェント

### Planner（計画・分析）
| エージェント | 主な出力先 |
|-------------|-----------|
| req-analyzer | `docs/requirements/` |
| spec-planner | `specs/features/` |

### Designer（設計）
| エージェント | 主な参照先 | 主な出力先 |
|-------------|-----------|-----------|
| tech-leader | `src/`（構造）, `infra/` | `docs/architecture/` |
| frontend-designer | `src/[frontend]/` | `specs/designs/frontend/` |
| backend-designer | `src/api/`, `src/services/` | `specs/designs/backend/` |

### Builder（実装）
| エージェント | 主な参照先 | 主な出力先 |
|-------------|-----------|-----------|
| code-builder | `specs/`, `docs/api/` | `src/`, `tests/` |

### Reviewer（レビュー・保守）
| エージェント | 主な参照先 | 主な出力先 |
|-------------|-----------|-----------|
| code-reviewer | `src/`, `tests/`, `specs/` | レビューコメント |
| code-debugger | `src/`, `tests/`, `logs/` | 修正案、バグレポート |

### Guide（案内）
| エージェント | 主な参照先 | 主な出力先 |
|-------------|-----------|-----------|
| code-guide | 全般（読み取りのみ） | 説明、案内 |
| general-purpose | 全般 | タスク依存 |

## CLAUDE.md の設計原則

### 悪い例（肥大化）

```markdown
# CLAUDE.md

## プロジェクト概要
このプロジェクトは...（長い説明）

## 技術スタック
- React 18
- TypeScript 5
- ...（詳細な技術リスト）

## コーディング規約
### 命名規則
- 変数名は camelCase
- クラス名は PascalCase
...（詳細なルール）

## API設計ガイドライン
...

## テスト方針
...

## デプロイ手順
...
```

### 良い例（最小限）

```markdown
# CLAUDE.md

## 共通原則
- 不明点は勝手に仮定せず、必ず確認する
- 役割外の判断・実装をしない
- 必要なファイルのみ読むこと

## 役割指定
- Planner / Designer / Builder / Reviewer / Guide のいずれかで動作
- 各役割の詳細は `.claude/agents/*.md` を参照

## 参照ルール
- 機密情報（.env*, credentials*, secrets*）は参照禁止
- 各役割の参照範囲を厳守

## 禁止事項
- 確認なしの破壊的変更
- 役割を超えた作業の実行
```

## ファイル配置のベストプラクティス

### 1. 機密情報の分離
```
# 絶対に参照させないファイル
.env
.env.local
.env.production
credentials.json
secrets/
```

### 2. 仕様書の構造化
```
specs/
├── features/           # 機能仕様（spec-planner出力）
│   ├── auth.md
│   └── payment.md
└── designs/            # 設計書
    ├── frontend/       # frontend-designer出力
    │   └── components.md
    └── backend/        # backend-designer出力
        ├── api.md
        └── database.md
```

### 3. ドキュメントの階層化
```
docs/
├── requirements/       # Planner向け
│   ├── functional.md
│   └── non-functional.md
├── architecture/       # Designer向け
│   └── overview.md
├── api/                # Builder向け
│   └── endpoints.md
└── operations/         # 運用チーム向け（エージェント参照外）
    └── runbook.md
```

## 参照制限の実装方法

各サブエージェントファイルの冒頭に以下を記載：

```markdown
## 参照範囲

### 参照してよい
- `docs/**`
- `specs/**`
- `.claude/**`

### 参照禁止
- `src/**`
- `infra/**`
- `.env*`
```

これにより、エージェントは自身の参照範囲を認識し、不要なファイルアクセスを避けます。
