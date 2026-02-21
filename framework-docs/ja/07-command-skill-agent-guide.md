# Skill・Agent 使い分けガイド

玄人開発フレームワークでは、2種類の自動化機構を提供しています。本ガイドでは、それぞれの役割と適切な使い分けを解説します。

> **注**: v0.4.0 以降、カスタムコマンドはスキルに統合されました。スラッシュコマンドとして起動するワークフローは「ワークフロースキル」（`user-invocable: true`）として定義されます。

---

## 目次

1. [2つの機構の概要](#1-2つの機構の概要)
2. [レイヤー構造と役割分担](#2-レイヤー構造と役割分担)
3. [開発フェーズ別の使い分け](#3-開発フェーズ別の使い分け)
4. [選択フローチャート](#4-選択フローチャート)
5. [相互参照マップ](#5-相互参照マップ)
6. [よくある質問](#6-よくある質問)

---

## 1. 2つの機構の概要

### 1.1 スキル（Skills）

スキルは2種類に分類されます：

#### リファレンススキル
| 項目 | 内容 |
|------|------|
| **役割** | 品質基準・チェックリスト・出力テンプレート |
| **起動方法** | Claudeが文脈に応じて自動適用、またはエージェントの `skills` フィールドで事前ロード |
| **特徴** | 判定基準と期待される出力形式を定義 |
| **配置場所** | `~/.claude/skills/`（グローバル）または `.claude/skills/`（プロジェクト） |

**例**: `security-baseline`, `coding-standards`, `spec-reviewer`

#### ワークフロースキル（スラッシュコマンド）
| 項目 | 内容 |
|------|------|
| **役割** | ワークフロー自動化 |
| **起動方法** | ユーザーが `/skill-name` で明示的に起動 |
| **特徴** | 一連の作業を自動実行、対話的な確認を含む。`user-invocable: true` で定義 |
| **配置場所** | `~/.claude/skills/`（グローバル）または `.claude/skills/`（プロジェクト） |

**例**: `/spec:init`, `/git:commit`, `/qa:full`

### 1.3 サブエージェント（Agents）

| 項目 | 内容 |
|------|------|
| **役割** | 専門家ペルソナ（特定の視点・参照範囲・責務を持つ） |
| **起動方法** | Claudeが複雑なタスクで自動選択、またはユーザーが指定 |
| **特徴** | 参照範囲の制限、他エージェントとの連携、成果物の引き継ぎ |
| **配置場所** | `.claude/agents/`（プロジェクト固有） |

**例**: `spec-planner`, `code-reviewer`, `tech-leader`

---

## 2. レイヤー構造と役割分担

```
┌─────────────────────────────────────────────────────────────────────┐
│                     スキル (Skills)                                  │
│                                                                     │
│  ワークフロースキル（user-invocable: true）                          │
│  ・ユーザーが /skill-name で明示的に起動                             │
│  ・一連の作業手順を自動化                                            │
│  ・allowed-tools でツールを制限可能                                   │
│  例: /spec:init → テンプレート生成 → TODO挿入 → 次ステップ提案       │
│                                                                     │
│  リファレンススキル                                                   │
│  ・チェックリストと判定基準                                          │
│  ・出力テンプレートと形式                                            │
│  ・エージェントの skills フィールドで事前ロード可能                   │
│  例: security-baseline → 10項目のセキュリティチェック → 重要度判定   │
└───────────────────────────────┬─────────────────────────────────────┘
                                │ 参照・活用
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     サブエージェント (Agents)                        │
│                                                                     │
│  「誰として行動するか」を定義するペルソナ層                           │
│  ・特定の役割と責務                                                  │
│  ・参照範囲の制限（セキュリティ）                                    │
│  ・skills フィールドでスキルを事前ロード                              │
│  ・他エージェントとの連携フロー                                      │
│                                                                     │
│  例: code-reviewer → src/tests参照 → レビュー結果 → code-builderへ   │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.1 使い分けの原則

| 観点 | ワークフロースキル | リファレンススキル | Agent |
|------|---------|-------|-------|
| **起動者** | ユーザー | Claude（自動）/ Agent | Claude（自動） |
| **粒度** | ワークフロー全体 | チェック・判定 | タスク実行 |
| **状態** | 対話的 | 参照的 | 自律的 |
| **参照制限** | allowed-tools | なし | あり |
| **出力** | 次のアクション提案 | 判定結果・レポート | 成果物 |

---

## 3. 開発フェーズ別の使い分け

### 3.1 仕様策定フェーズ

| 作業 | 使用する機構 | 理由 |
|------|-------------|------|
| 仕様書テンプレート生成 | `/spec:init` (ワークフロースキル) | ユーザー起動のワークフロー |
| 仕様書作成 | `spec-planner` (Agent) | 専門家として仕様策定 |
| 仕様書レビュー | `/spec:review` → `spec-reviewer` | ワークフロースキルがフロー、リファレンススキルが基準 |

### 3.2 設計フェーズ

| 作業 | 使用する機構 | 理由 |
|------|-------------|------|
| 技術選定 | `tech-leader` (Agent) | トレードオフ分析が必要 |
| アーキテクチャレビュー | `architecture-reviewer` (Skill) | チェックリストに基づく判定 |
| 実装計画作成 | `/plan:make` (ワークフロースキル) | タスク分解のワークフロー |

### 3.3 実装フェーズ

| 作業 | 使用する機構 | 理由 |
|------|-------------|------|
| タスク実装 | `/impl:run` (ワークフロースキル) | 計画に沿った実装ワークフロー |
| コーディング規約確認 | `coding-standards` (Skill) | 基準に基づくチェック |
| テスト作成 | `test-author` (Skill) | テストパターンの適用 |
| テスト実行 | `/qa:full` (ワークフロースキル) | 実行→結果整理のワークフロー |

### 3.4 レビュー・リリースフェーズ

| 作業 | 使用する機構 | 理由 |
|------|-------------|------|
| コミット | `/git:commit` (ワークフロースキル) | Git操作のワークフロー |
| PR作成 | `/git:pr` (ワークフロースキル) | Git操作のワークフロー |
| PRレビュー | `pr-reviewer` (Skill) + `code-reviewer` (Agent) | 基準適用 + 専門的判断 |
| セキュリティ確認 | `security-baseline` (Skill) | セキュリティチェックリスト |
| レビュー対応 | `/fixup-from-pr-comments` (ワークフロースキル) | 修正ワークフロー |
| リリースノート | `release-notes-writer` (Skill) | テンプレート適用 |

### 3.5 保守フェーズ

| 作業 | 使用する機構 | 理由 |
|------|-------------|------|
| デバッグ | `code-debugger` (Agent) → `debug-triage` (Skill) | Agentが実行、Skillが手法 |
| リファクタリング | `/refactor:cleanup` (ワークフロースキル) | 整理のワークフロー |
| 依存更新 | `dependency-change-reviewer` (Skill) | リスク評価基準 |

---

## 4. 選択フローチャート

```
「やりたいこと」を入力
        │
        ▼
┌───────────────────────────┐
│ スラッシュコマンドで        │
│ 明示的に起動したいか？      │
└───────────────────────────┘
        │
   ┌────┴────┐
   ▼         ▼
  Yes        No
   │         │
   ▼         ▼
ワークフロー  ┌────────────────────────┐
スキル       │ 判断基準が必要か？       │
             │ それとも作業実行が必要か？│
             └────────────────────────┘
                       │
                 ┌─────┴─────┐
                 ▼           ▼
              判断基準      作業実行
                 │           │
                 ▼           ▼
           リファレンス    Agent
             スキル
```

### 簡易判断表

| やりたいこと | 選択 |
|-------------|------|
| 「〜を始めたい」「〜を実行して」 | **ワークフロースキル**（スラッシュコマンド） |
| 「〜をチェックして」「〜の基準で判定して」 | **リファレンススキル** |
| 「〜として考えて」「〜の視点で分析して」 | **Agent** |

---

## 5. 相互参照マップ

### 5.1 ワークフロースキル → リファレンススキル/Agent

| ワークフロースキル | 関連リファレンススキル | 関連Agent |
|---------|----------|-----------|
| `/spec:init` | - | `spec-planner` |
| `/spec:review` | `spec-reviewer` | - |
| `/plan:make` | - | `spec-planner` |
| `/impl:run` | `coding-standards` | `code-builder` |
| `/qa:full` | `test-author` | - |
| `/git:commit` | - | - |
| `/git:pr` | `pr-reviewer` | - |
| `/git:branch` | - | - |
| `/commit-push-pr` | `pr-reviewer` | - |
| `/fixup-from-pr-comments` | - | `code-builder` |
| `/refactor:cleanup` | `coding-standards` | - |
| `/session:compact-smart` | - | - |
| `/ultrathink` | - | - |

### 5.2 リファレンススキル → ワークフロースキル/Agent

| リファレンススキル | 関連ワークフロースキル | 関連Agent（skills フィールド） |
|-------|------------|-----------|
| `spec-reviewer` | `/spec:review` | `spec-planner` |
| `architecture-reviewer` | - | `tech-leader` |
| `coding-standards` | `/impl:run`, `/refactor:cleanup` | `code-builder`, `code-reviewer` |
| `test-author` | `/qa:full` | `code-builder` |
| `debug-triage` | - | `code-debugger` |
| `pr-reviewer` | `/git:pr`, `/commit-push-pr` | `code-reviewer` |
| `security-baseline` | - | `code-reviewer` |
| `dependency-change-reviewer` | - | `tech-leader` |
| `release-notes-writer` | - | - |

### 5.3 Agent → ワークフロースキル/リファレンススキル

| Agent | 関連ワークフロースキル | 事前ロードスキル（skills フィールド） |
|-------|------------|-----------|
| `req-analyzer` | `/spec:init` | - |
| `spec-planner` | `/spec:init`, `/plan:make` | `spec-reviewer` |
| `tech-leader` | - | `architecture-reviewer` |
| `frontend-designer` | - | - |
| `backend-designer` | - | - |
| `code-builder` | `/impl:run`, `/fixup-from-pr-comments` | `coding-standards`, `test-author` |
| `code-reviewer` | `/git:pr` | `pr-reviewer`, `security-baseline`, `coding-standards` |
| `code-debugger` | - | `debug-triage` |
| `code-guide` | - | - |
| `general-purpose` | - | - |

---

## 6. よくある質問

### Q1: ワークフロースキルとリファレンススキルの両方に似た機能がある場合は？

**A**: ワークフロースキル（スラッシュコマンド）を優先してください。ワークフロースキルは全体を管理し、必要に応じてリファレンススキルの基準を内部で使用します。

例: 仕様レビューは `/spec:review` を実行。内部で `spec-reviewer` スキルの基準が適用されます。

### Q2: リファレンススキルとAgentの両方に似た機能がある場合は？

**A**: タスクの性質で判断してください。
- チェック・判定が主目的 → **Skill**
- 作業実行・成果物作成が主目的 → **Agent**

例: デバッグは `code-debugger` (Agent) が担当。調査手法として `debug-triage` (Skill) を参照します。

### Q3: 新しい自動化を追加したい場合、どれで作るべき？

**A**: 以下の基準で判断してください。

| 特徴 | 作成する機構 |
|------|-------------|
| ユーザーが「/xxx」で起動したい | ワークフロースキル（`user-invocable: true`） |
| 判断基準・チェックリストを定義したい | リファレンススキル |
| 特定の役割・参照範囲を持たせたい | Agent |

### Q4: 1つのタスクで複数の機構を使うことはある？

**A**: はい、よくあります。例えば：

```
ユーザー: /git:pr を実行
    │
    ├─→ ワークフロースキル: /git:pr がワークフローを開始
    │       │
    │       ├─→ リファレンススキル: pr-reviewer の基準でPR本文を生成
    │       │
    │       └─→ Agent: code-reviewer の視点でレビュー観点を追加
    │
    └─→ 結果: PRが作成される
```

---

## 付録: 定義ファイル一覧

### Skills（22個 = 9 リファレンス + 13 ワークフロー）
```
~/.claude/skills/
# リファレンススキル（9個）
├── spec-reviewer/        # 仕様書レビュー基準
├── architecture-reviewer/# アーキテクチャレビュー
├── coding-standards/     # コーディング規約
├── test-author/          # テスト作成パターン
├── debug-triage/         # デバッグ手法
├── pr-reviewer/          # PRレビュー基準
├── security-baseline/    # セキュリティチェック
├── dependency-change-reviewer/  # 依存更新レビュー
├── release-notes-writer/ # リリースノート作成
# ワークフロースキル（13個, user-invocable: true）
├── spec-init/            # /spec:init - 仕様書テンプレート生成
├── spec-review/          # /spec:review - 仕様書レビュー
├── plan-make/            # /plan:make - 実装計画作成
├── impl-run/             # /impl:run - タスク実装
├── qa-full/              # /qa:full - フルテスト
├── git-branch/           # /git:branch - ブランチ作成
├── git-commit/           # /git:commit - コミット
├── git-pr/               # /git:pr - PR作成
├── commit-push-pr/       # /commit-push-pr - 統合ワークフロー
├── fixup-from-pr-comments/   # /fixup-from-pr-comments - レビュー対応
├── refactor-cleanup/     # /refactor:cleanup - リファクタリング
├── session-compact-smart/    # /session:compact-smart - セッション整理
└── ultrathink/           # /ultrathink - 深い思考モード
```

### Agents（10個）
```
.claude/agents/
├── req-analyzer.md       # 要求分析者
├── spec-planner.md       # 仕様策定者
├── tech-leader.md        # テックリード
├── frontend-designer.md  # フロントエンド設計者
├── backend-designer.md   # バックエンド設計者
├── code-builder.md       # コードビルダー
├── code-reviewer.md      # コードレビューワー
├── code-debugger.md      # コードデバッガー
├── code-guide.md         # コードガイド
└── general-purpose.md    # 汎用エージェント
```

---

*このガイドは玄人開発フレームワークの一部です。*
