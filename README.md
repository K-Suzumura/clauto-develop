# 玄人開発（clauto-develop）

Claude Code を活用した AI 駆動開発フレームワーク

## コンセプト

Claude Code を最大限に活用し、AI と人間が協調して開発を進めるための実践的なフレームワークです。

## 概要

本プロジェクトは、Claude Code のベストプラクティスを基盤とし、仕様駆動開発のためのサブエージェントを統合した開発手法を提供します。

---

## クイックスタート

### 方法1: Plugin Marketplace からインストール（推奨）

Claude Code の Plugin Marketplace 機能を使って簡単にインストールできます。

```bash
# マーケットプレースを追加
/plugin marketplace add K-Suzumura/clauto-develop

# プラグインをインストール
/plugin install clauto-develop@clauto-develop
```

インストール後、以下が使用可能になります：
- 10種類のサブエージェント
- 9種類のスキル
- 13種類のコマンド（`/spec:init`, `/git:commit` など）

---

### 方法2: Template Repository から新規プロジェクトを作成

1. **テンプレートからリポジトリを作成**

   GitHub で「Use this template」ボタンをクリック、または以下のリンクから作成：

   👉 [Use this template](https://github.com/new?template_name=clauto-develop&template_owner=K-Suzumura)

   - 「Create a new repository」を選択
   - リポジトリ名を入力（例: `my-project`）
   - 「Create repository」をクリック

2. **ローカルにクローン**

   ```bash
   git clone https://github.com/[your-username]/[your-repo-name].git
   cd [your-repo-name]
   ```

3. **テンプレートをコピーして編集**

   ```bash
   # Claude Code 設定ファイルをコピー
   cp CLAUDE.md.template CLAUDE.md

   # プロジェクト README をコピー
   cp README.md.template README.md
   ```

   各ファイルを編集：

   - **CLAUDE.md**: Claude Code の動作設定（言語設定、コーディング規約など）
   - **README.md**: プロジェクトの概要、技術スタック、セットアップ手順など

4. **不要なファイルを削除（オプション）**

   ```bash
   # フレームワーク説明ドキュメントが不要な場合
   rm -rf framework-docs/
   ```

5. **カスタムコマンド・スキルのインストール（オプション）**

   **推奨: Plugin Marketplace からインストール**
   ```bash
   /plugin marketplace add K-Suzumura/clauto-develop
   /plugin install clauto-develop@clauto-develop
   ```

   <details>
   <summary>手動インストール（レガシー）</summary>

   ```bash
   # カスタムコマンドをグローバルにインストール
   mkdir -p ~/.claude/commands
   cp global-commands/*.md ~/.claude/commands/

   # カスタムスキルをグローバルにインストール
   mkdir -p ~/.claude/skills
   cp -r global-skills/* ~/.claude/skills/
   ```

   </details>

   これで `/spec:init`, `/git:commit` などのコマンドと、`security-baseline`, `coding-standards` などのスキルが使用可能になります。

6. **Serena MCP の有効化（オプション）**

   本プロジェクトには Serena MCP サーバーの設定（`.mcp.json`）が含まれています。Serena を使用するには uv が必要です：

   ```bash
   # uv のインストール（未インストールの場合）
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```

   Claude Code 起動時に `.mcp.json` が自動的に読み込まれ、Serena が有効になります。詳細は `framework-docs/ja/08-serena-integration-guide.md` を参照してください。

7. **開発を開始**

   ```bash
   claude
   ```

   Claude Code 内で：

   ```
   「ユーザー認証機能を実装したい」
   → req-analyzer を使って要求を分析
   → spec-planner を使って仕様を策定
   → code-builder を使って実装
   ```

---

### 方法3: 既存プロジェクトに導入

1. **必要なファイルをコピー**

   ```bash
   # サブエージェント定義をコピー
   cp -r clauto-develop/.claude/agents/ your-project/.claude/agents/

   # CLAUDE.md をコピーして編集
   cp clauto-develop/CLAUDE.md.template your-project/CLAUDE.md
   ```

2. **CLAUDE.md を編集**

   プロジェクトに合わせて言語設定やコーディング規約を調整

3. **開発を開始**

   ```bash
   cd your-project
   claude
   ```

---

## チュートリアル

ECサイト構築を例にした実践的なチュートリアルを用意しています：

👉 [ECサイト構築チュートリアル](framework-docs/ja/00-clauto-develop-tutorial.md)

このチュートリアルでは以下を学べます：

- Template repositoryからプロジェクトを作成する方法
- CLAUDE.mdをプロジェクトに合わせて設定する方法
- サブエージェントを活用した開発フローの実践
- 機能追加・バグ修正の演習

---

## プロジェクト構成

```
clauto-develop/
├── .claude/
│   └── agents/              # サブエージェント定義（10種類）
├── .claude-plugin/
│   └── marketplace.json     # Plugin Marketplace 定義
├── plugins/
│   └── clauto-develop/      # プラグインパッケージ
│       ├── .claude-plugin/
│       │   └── plugin.json  # プラグインマニフェスト
│       ├── agents/          # サブエージェント（10種類）
│       ├── commands/        # コマンド（13種類）
│       ├── skills/          # スキル（9種類）
│       └── README.md
├── docs/
│   └── specs/               # 仕様書配置用（空）
├── src/                     # ソースコード用（空）
├── tests/                   # テスト用（空）
├── framework-docs/          # フレームワーク説明（削除可能）
│   ├── ja/                  # 日本語ドキュメント
│   └── en/                  # 英語ドキュメント
├── .github/                 # GitHub テンプレート
│   ├── ISSUE_TEMPLATE/      # Issue テンプレート
│   └── PULL_REQUEST_TEMPLATE.md
├── global-commands/         # カスタムコマンド（13種類）- レガシー
├── global-skills/           # カスタムスキル（9種類）- レガシー
├── .mcp.json                # Serena MCP設定
├── .gitignore               # Git除外設定
├── LICENSE                  # Apache License 2.0
├── CLAUDE.md.template       # Claude Code 設定テンプレート
├── README.md.template       # プロジェクト README テンプレート
└── README.md                # このファイル
```

---

## サブエージェント

### エージェント一覧

| エージェント | 役割 | モデル | ツール |
|-------------|------|--------|--------|
| **req-analyzer** | 要求分析者 | haiku | Read, Glob, Grep, WebSearch |
| **spec-planner** | 仕様策定者 | sonnet | Read, Write, Glob, Grep |
| **tech-leader** | テックリード | opus | Read, Glob, Grep, WebSearch |
| **backend-designer** | バックエンド設計者 | sonnet | Read, Write, Glob, Grep |
| **frontend-designer** | フロントエンド設計者 | sonnet | Read, Write, Glob, Grep |
| **code-builder** | コードビルダー | sonnet | Read, Write, Edit, Glob, Grep, Bash |
| **code-reviewer** | コードレビューワー | opus | Read, Glob, Grep |
| **code-debugger** | コードデバッガー | sonnet | Read, Glob, Grep, Bash |
| **code-guide** | コードガイド | haiku | Read, Glob, Grep |
| **general-purpose** | 汎用エージェント | sonnet | All |

### 開発フロー

```
[ユーザー要求]
    │
    ▼
req-analyzer（要求分析）
    │
    ▼
spec-planner（仕様策定）
    │
    ▼
tech-leader（技術選定）
    │
    ├──► backend-designer（バックエンド設計）
    │
    ├──► frontend-designer（フロントエンド設計）
    │
    ▼
code-builder（実装）
    │
    ▼
code-reviewer（レビュー）
    │
    ▼
[完成]
```

---

## カスタムコマンド

Plugin Marketplace からインストールすると、以下のスラッシュコマンドが使用可能：

| コマンド | 説明 |
|---------|------|
| `/spec:init` | 仕様書テンプレート生成 |
| `/spec:review` | 仕様書レビュー |
| `/plan:make` | 実装計画作成 |
| `/impl:run` | タスク実装実行 |
| `/qa:full` | フルテスト実行 |
| `/git:commit` | 規約準拠コミット |
| `/git:pr` | PR作成 |
| `/git:branch` | ブランチ作成 |
| `/gh:commit-push-pr` | commit→push→PR統合 |
| `/fixup-from-pr-comments` | PRコメント対応 |
| `/refactor:cleanup` | リファクタリング |
| `/session:compact-smart` | セッションコンパクト |
| `/ultrathink` | 深い思考モード |

<details>
<summary>手動インストール（レガシー）</summary>

```bash
# カスタムコマンドをグローバルにインストール
mkdir -p ~/.claude/commands
cp global-commands/*.md ~/.claude/commands/

# カスタムスキルをグローバルにインストール
mkdir -p ~/.claude/skills
cp -r global-skills/* ~/.claude/skills/
```

</details>

詳細は `framework-docs/ja/06-custom-commands-guide.md` を参照

---

## カスタムスキル

Plugin Marketplace からインストールすると、以下のスキルが自動適用：

| スキル | 説明 |
|-------|------|
| `architecture-reviewer` | アーキテクチャレビュー |
| `coding-standards` | コーディング規約 |
| `debug-triage` | デバッグトリアージ |
| `dependency-change-reviewer` | 依存パッケージ変更レビュー |
| `pr-reviewer` | PRレビュー基準 |
| `release-notes-writer` | リリースノート作成 |
| `security-baseline` | セキュリティチェック |
| `spec-reviewer` | 仕様書レビュー基準 |
| `test-author` | テスト作成 |

詳細は `framework-docs/ja/04-claude-skills-guide.md` を参照

---

## Serena MCP（推奨）

Serena は LSP（Language Server Protocol）を活用したコードのセマンティック理解・編集ツールです。サブエージェントと組み合わせることで、より高精度なコード操作が可能になります。

### 主な機能

| 機能 | 説明 |
|------|------|
| シンボル検索 | 関数、クラス、変数の定義を構造的に検索 |
| 参照検索 | シンボルの使用箇所を網羅的に検出 |
| 呼び出し階層 | 関数の呼び出し関係を可視化 |
| セマンティック編集 | リネームなどの安全なリファクタリング |

### 導入方法

```bash
# プロジェクトに追加
claude mcp add serena -- uvx --from git+https://github.com/oraios/serena serena start-mcp-server --context claude-code --project-from-cwd
```

または、`.mcp.json` をプロジェクトルートに配置（本テンプレートに同梱済み）。

### サブエージェントとの連携

| エージェント | Serena活用 |
|-------------|-----------|
| code-guide | シンボル検索、定義ジャンプでナビゲーション精度向上 |
| code-builder | セマンティック編集、影響範囲の特定 |
| code-reviewer | 参照関係の追跡、診断情報の取得 |
| code-debugger | コールスタック追跡、原因追跡 |

詳細は `framework-docs/ja/08-serena-integration-guide.md` を参照

---

## ベストプラクティス

### 公式ベストプラクティス

Claude Code の公式ベストプラクティスに準拠しています：

👉 [Best Practices for Claude Code](https://code.claude.com/docs/en/best-practices)

**特に重要なポイント:**

1. **検証方法を提供する** - テスト、スクリーンショット、期待される出力を含める
2. **探索 → 計画 → 実装** - Plan Mode で計画を立ててから実装
3. **CLAUDE.md を簡潔に** - 各行について「これがないと Claude がミスするか？」を問う
4. **コンテキストを積極的に管理** - `/clear` でタスク間のコンテキストをリセット
5. **サブエージェントで調査** - 調査はサブエージェントに委譲し、メインコンテキストをクリーンに保つ

### 推奨セットアップ

プラグインインストール後、プロジェクトに合わせて設定を行います：

```bash
# CLAUDE.md の自動生成（推奨）
/init

# 生成された CLAUDE.md をプロジェクトに合わせてカスタマイズ
# 特に検証コマンド（テスト、型チェック、リント）を設定
```

### Boris Cherny 氏の 7 つのベストプラクティス

Claude Code 作成者が実践する、効率的な AI 開発のためのベストプラクティス：

1. **複数インスタンスの並行実行** - 5つのインスタンスを同時実行
2. **環境間のシームレスな切り替え** - ローカルとウェブセッションの組み合わせ
3. **Opus 4.5 with Thinking モード** - 高品質な出力を得る
4. **共有ドキュメントの継続的更新** - Git管理で改善サイクル
5. **Plan モードでの計画重視** - 計画を固めてから実行
6. **スラッシュコマンドによる自動化** - 繰り返しタスクを自動化
7. **検証ループの重視** - 出力品質を2〜3倍向上

---

## 参考資料

### Boris Cherny 氏のワークフロー

| リソース | 説明 |
|----------|------|
| [Boris Cherny 氏の Twitter スレッド](https://x.com/bcherny/status/2007179832300581177) | 原典：本人による直接の解説 |
| [VentureBeat 記事](https://venturebeat.com/technology/the-creator-of-claude-code-just-revealed-his-workflow-and-developers-are) | 英語での詳細な解説記事 |
| [Zenn スクラップ](https://zenn.dev/hiraoku/scraps/e3dc6c573a82ac) | 日本語での解説 |
| [Paddo.dev ブログ](https://paddo.dev/blog/how-boris-uses-claude-code/) | ワークフローの詳細分析 |

### 公式リソース

- [Claude Code 公式](https://claude.ai/code)
- [Anthropic 公式サイト](https://www.anthropic.com/)

---

## ライセンス

Apache License 2.0

## 貢献

Issue や Pull Request を歓迎します。
