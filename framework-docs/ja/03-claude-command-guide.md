# Claude Code コマンドガイド

Claude Code の基本コマンドとスラッシュコマンドによる作業自動化について解説します。

---

## 目次

1. [CLI 基本コマンド](#1-cli-基本コマンド)
2. [ビルトインスラッシュコマンド](#2-ビルトインスラッシュコマンド)
3. [キーボードショートカット](#3-キーボードショートカット)
4. [カスタムスラッシュコマンド](#4-カスタムスラッシュコマンド)
5. [実践的なコマンド例](#5-実践的なコマンド例)
6. [ベストプラクティス](#6-ベストプラクティス)

---

## 1. CLI 基本コマンド

### 1.1 インストールと起動

```bash
# インストール（Node.js 18以上が必要）
npm install -g @anthropic-ai/claude-code

# API キーの設定
export ANTHROPIC_API_KEY="your-api-key"

# 対話モードで起動
claude

# ヘルプの表示
claude --help
```

### 1.2 実行モード

| コマンド | 説明 | 使用例 |
|---------|------|--------|
| `claude` | 対話モードで起動 | `claude` |
| `claude -p "query"` | 1回実行して終了（Print モード） | `claude -p "このファイルを説明して"` |
| `claude -c` | 直前の会話を継続 | `claude -c` |
| `claude -r "session-id"` | 特定セッションを再開 | `claude -r "abc123" "続きを実行"` |

### 1.3 主要な CLI オプション

```bash
# モデルの指定
claude --model opus

# 思考モードの有効化（より深い推論）
claude --thinking

# 出力形式の指定
claude -p "query" --output-format json

# 会話履歴の表示
claude --history

# 設定の確認
claude config list
```

### 1.4 設定ファイルの階層

Claude Code は以下の階層で設定を読み込みます：

```
~/.claude/settings.json          # ユーザーレベル（グローバル）
.claude/settings.json            # プロジェクトレベル
.claude/settings.local.json      # ローカル（.gitignore 推奨）
```

---

## 2. ビルトインスラッシュコマンド

対話モード中に使用できる組み込みコマンドです。

### 2.1 セッション管理

| コマンド | 説明 |
|---------|------|
| `/clear` | 会話履歴とコンテキストをリセット |
| `/compact [instructions]` | 会話を要約して圧縮（オプションで指示を追加） |
| `/exit` | Claude Code を終了 |

### 2.2 情報・ヘルプ

| コマンド | 説明 |
|---------|------|
| `/help` | 利用可能なコマンド一覧を表示 |
| `/cost` | 現在のセッションのコストと時間を表示 |
| `/doctor` | インストール状態のヘルスチェック |

### 2.3 設定・統合

| コマンド | 説明 |
|---------|------|
| `/config` | 設定パネルを開く |
| `/ide` | IDE 統合の管理 |
| `/mcp` | MCP（Model Context Protocol）機能にアクセス |
| `/permissions` | ツール権限の管理 |

### 2.4 MCP（Model Context Protocol）

MCP は Claude Code の機能を拡張するためのプロトコルです。外部ツールやサービスを Claude Code に統合できます。

#### MCP サーバーの管理

```bash
# MCP サーバーの一覧表示
claude mcp list

# プロジェクトに MCP サーバーを追加
claude mcp add <name> -- <command> [args...]

# グローバルに MCP サーバーを追加（全プロジェクトで使用可能）
claude mcp add --scope user <name> -- <command> [args...]

# MCP サーバーを削除
claude mcp remove <name>
```

#### .mcp.json による設定

プロジェクトルートに `.mcp.json` を配置することで、MCP サーバーを設定できます：

```json
{
  "mcpServers": {
    "server-name": {
      "type": "stdio",
      "command": "command-to-run",
      "args": ["arg1", "arg2"],
      "env": {
        "ENV_VAR": "value"
      }
    }
  }
}
```

#### Serena（本プロジェクトに同梱）

本プロジェクトには Serena MCP サーバーの設定（`.mcp.json`）が含まれています。Serena は LSP を活用したセマンティックコード操作ツールです。

**前提条件**: uv パッケージマネージャーが必要です。

```bash
# uv のインストール（未インストールの場合）
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Claude Code 起動時に自動的に Serena が読み込まれます。詳細は [Serena 統合ガイド](./08-serena-integration-guide.md) を参照してください。

### 2.5 使用例

```
# 長い会話をリセットしてメモリを解放
/clear

# 会話を要約して継続
/compact 重要な決定事項を保持して

# 現在のコストを確認
/cost

# ヘルプを表示
/help
```

---

## 3. キーボードショートカット

対話モード中に使用できるショートカットです。

### 3.1 入力関連

| ショートカット | 説明 |
|---------------|------|
| `Enter` | メッセージを送信（1行の場合） |
| `Shift + Enter` | 改行を挿入（複数行入力） |
| `Ctrl + C` | 現在の処理を中断 |
| `Ctrl + D` | セッションを終了 |

### 3.2 ナビゲーション

| ショートカット | 説明 |
|---------------|------|
| `↑` / `↓` | 履歴の前後移動 |
| `Ctrl + L` | 画面をクリア |
| `Tab` | コマンド・ファイルパスの補完 |

### 3.3 編集・操作

| ショートカット | 説明 |
|---------------|------|
| `Ctrl + A` | 行頭に移動 |
| `Ctrl + E` | 行末に移動 |
| `Ctrl + U` | 行をクリア |
| `Ctrl + W` | 前の単語を削除 |

---

## 4. カスタムスラッシュコマンド

繰り返し行う作業を自動化するためのカスタムコマンドを作成できます。

### 4.1 コマンドの配置場所

| 配置場所 | スコープ | 呼び出し方 |
|---------|---------|-----------|
| `.claude/commands/` | プロジェクト固有 | `/project:コマンド名` |
| `~/.claude/commands/` | 全プロジェクト共通 | `/user:コマンド名` |

### 4.2 基本構造

コマンドは Markdown ファイルとして作成します。ファイル名がコマンド名になります。

```
.claude/commands/
├── commit.md          → /project:commit
├── pr.md              → /project:pr
├── test.md            → /project:test
└── review/
    └── security.md    → /project:review:security
```

### 4.3 フロントマター（メタデータ）

```yaml
---
description: コミットメッセージを自動生成
allowed-tools: ["Bash(git:*)"]
model: haiku
argument-hint: <コミットタイプ>
---
```

| オプション | 説明 |
|-----------|------|
| `description` | `/help` に表示される説明 |
| `allowed-tools` | 許可するツール（Bash コマンドなど） |
| `model` | 使用モデル（`haiku` / `sonnet` / `opus`） |
| `argument-hint` | 引数のヒント表示 |

### 4.4 変数の使用

| 変数 | 説明 | 例 |
|-----|------|-----|
| `$ARGUMENTS` | すべての引数 | `/commit fix typo` → `fix typo` |
| `$1`, `$2`, ... | 位置引数 | `/branch add-feature` → `$1 = add-feature` |

### 4.5 Bash コマンドの実行

`!` プレフィックスで Bash コマンドを実行し、結果をプロンプトに注入できます。

```markdown
---
description: Git の状態を確認してコミット
---

以下の変更内容を確認してください：

!git status
!git diff --staged

上記の変更に対して、適切なコミットメッセージを生成してください。
```

### 4.6 ファイル参照

`@` プレフィックスでファイル内容を参照できます。

```markdown
以下のファイルをレビューしてください：

@src/components/Button.tsx
@src/styles/button.css
```

---

## 5. 実践的なコマンド例

### 5.1 コミットメッセージ自動生成

`.claude/commands/commit.md`:

```markdown
---
description: 変更内容からコミットメッセージを生成
allowed-tools: ["Bash(git:*)"]
model: haiku
---

以下の変更内容を分析してください：

!git diff --staged

Conventional Commits 形式でコミットメッセージを生成してください：
- feat: 新機能
- fix: バグ修正
- docs: ドキュメント
- refactor: リファクタリング
- test: テスト
- chore: その他

日本語で簡潔に記述してください。
```

### 5.2 プルリクエスト作成

`.claude/commands/pr.md`:

```markdown
---
description: PRを作成
allowed-tools: ["Bash(git:*)", "Bash(gh:*)"]
model: sonnet
---

以下の情報を確認してください：

!git log origin/main..HEAD --oneline
!git diff origin/main..HEAD --stat

この変更に対する Pull Request を作成してください：
1. タイトルは変更内容を簡潔に（日本語）
2. 本文には変更の概要、目的、テスト方法を記載
3. `gh pr create` コマンドで作成
```

### 5.3 テスト実行と修正

`.claude/commands/test.md`:

```markdown
---
description: テストを実行し、失敗したら修正
allowed-tools: ["Bash(npm:*)", "Bash(npx:*)"]
model: sonnet
---

テストを実行してください：

!npm test

失敗したテストがあれば：
1. エラー内容を分析
2. 原因を特定
3. 修正を実施
4. 再度テストを実行して確認
```

### 5.4 コードレビュー

`.claude/commands/review.md`:

```markdown
---
description: 変更内容をレビュー
model: sonnet
argument-hint: <ファイルパス>
---

以下のファイルをレビューしてください：

@$ARGUMENTS

レビュー観点：
- コード品質（可読性、保守性）
- バグの可能性
- セキュリティリスク
- パフォーマンスの問題

問題点と改善提案を日本語で報告してください。
```

### 5.5 ブランチ作成

`.claude/commands/branch.md`:

```markdown
---
description: 機能ブランチを作成
allowed-tools: ["Bash(git:*)"]
model: haiku
argument-hint: <機能の説明>
---

以下の機能用のブランチを作成してください：
$ARGUMENTS

ブランチ名の規則：
- 機能追加: feat/説明
- バグ修正: fix/説明
- リファクタリング: refactor/説明

英語のケバブケースで命名し、`git checkout -b` で作成してください。
```

### 5.6 イシュー対応

`.claude/commands/fix-issue.md`:

```markdown
---
description: GitHub Issueを修正
allowed-tools: ["Bash(git:*)", "Bash(gh:*)"]
model: sonnet
argument-hint: <Issue番号>
---

Issue #$1 を修正してください。

まず、Issue の内容を確認：
!gh issue view $1

次に：
1. 問題を分析
2. 関連コードを特定
3. 修正を実装
4. テストを作成
5. コミット

修正完了後、Issue をクローズする PR を作成してください。
```

---

## 6. ベストプラクティス

### 6.1 モデル選択の戦略

| タスク | 推奨モデル | 理由 |
|-------|-----------|------|
| リント・フォーマット | `haiku` | 高速・低コスト |
| コミットメッセージ生成 | `haiku` | シンプルなタスク |
| コードレビュー | `sonnet` | 複雑な分析が必要 |
| バグ修正 | `sonnet` | 深い推論が必要 |
| アーキテクチャ設計 | `opus` | 最高品質の推論 |

### 6.2 権限の最小化

```yaml
# 良い例：必要な権限のみ
allowed-tools: ["Bash(git:*)"]

# 避けるべき例：過剰な権限
allowed-tools: ["Bash(*)"]
```

### 6.3 シェルエイリアスの活用

```bash
# ~/.bashrc または ~/.zshrc に追加

# クイックコミット
alias cc="claude -p '/project:commit'"

# テスト実行
alias ct="claude -p '/project:test'"

# PR作成
alias cpr="claude -p '/project:pr'"
```

### 6.4 コマンドの組織化

```
.claude/commands/
├── git/
│   ├── commit.md
│   ├── branch.md
│   └── pr.md
├── test/
│   ├── unit.md
│   └── e2e.md
└── review/
    ├── security.md
    └── performance.md
```

### 6.5 タスク間のコンテキスト管理

```
# タスクを切り替える前にコンテキストをクリア
/clear

# 会話が長くなったら要約
/compact 重要な決定事項と未完了タスクを保持
```

---

## 参考資料

- [Claude Code 公式](https://claude.ai/code)
- [Anthropic 公式サイト](https://www.anthropic.com/)
- [Claude Code Slash Commands ドキュメント](https://code.claude.com/docs/en/slash-commands)
- [Claude Code ベストプラクティス](https://www.anthropic.com/engineering/claude-code-best-practices)

---

*このガイドは随時更新されます。最新情報は公式ドキュメントを参照してください。*
