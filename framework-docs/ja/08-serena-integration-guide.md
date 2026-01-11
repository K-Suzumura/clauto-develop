# Serena MCP 統合ガイド

Serenaは、LSP（Language Server Protocol）を活用してコードのセマンティック理解と編集機能を提供するMCPサーバーです。本フレームワークのサブエージェントと組み合わせることで、より高精度なコード操作が可能になります。

---

## 目次

1. [Serenaとは](#1-serenaとは)
2. [導入方法](#2-導入方法)
3. [設定オプション](#3-設定オプション)
4. [提供ツール](#4-提供ツール)
5. [サブエージェントとの連携](#5-サブエージェントとの連携)
6. [使用例](#6-使用例)
7. [トラブルシューティング](#7-トラブルシューティング)
8. [ベストプラクティス](#8-ベストプラクティス)

---

## 1. Serenaとは

### 1.1 概要

Serenaは、AIコーディングエージェントのためのツールキットで、以下の特徴があります：

| 特徴 | 説明 |
|------|------|
| **セマンティック理解** | 単なるテキスト検索ではなく、コードの構造を理解した操作 |
| **LSP統合** | 30以上のプログラミング言語に対応 |
| **IDE級の機能** | 定義ジャンプ、参照検索、リファクタリングなど |
| **MCP対応** | Claude Codeとシームレスに連携 |

### 1.2 なぜSerenaが必要か

通常のファイル検索（Glob, Grep）との違い：

```
┌─────────────────────────────────────────────────────────────┐
│                    通常のファイル検索                          │
├─────────────────────────────────────────────────────────────┤
│ Grep "function getUser"                                      │
│   → テキストマッチで検索                                      │
│   → 同名の異なる関数も全てヒット                              │
│   → コメント内の記述もヒット                                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    Serenaのセマンティック検索                  │
├─────────────────────────────────────────────────────────────┤
│ find_symbol "getUser"                                        │
│   → シンボルとして認識された関数のみ検索                      │
│   → 型情報、パラメータ情報も取得                             │
│   → 呼び出し階層、参照関係も把握可能                         │
└─────────────────────────────────────────────────────────────┘
```

### 1.3 対応言語

| カテゴリ | 言語 |
|---------|------|
| スクリプト | Python, TypeScript, JavaScript, Ruby |
| システム | Rust, Go, C, C++ |
| JVM | Java, Kotlin, Scala, Groovy |
| その他 | C#, PHP, Swift, Lua など |

---

## 2. 導入方法

### 2.1 前提条件

- **uv パッケージマネージャ**: Pythonのパッケージ管理ツール
- **Claude Code**: MCPクライアントとして動作

```bash
# uv のインストール（未インストールの場合）
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 2.2 プロジェクトへの導入

**方法1: Claude Code コマンドで追加（推奨）**

```bash
cd your-project

# プロジェクトローカルに追加
claude mcp add serena -- uvx --from git+https://github.com/oraios/serena serena start-mcp-server --context claude-code --project-from-cwd
```

**方法2: .mcp.json を直接作成**

プロジェクトルートに `.mcp.json` を作成：

```json
{
  "mcpServers": {
    "serena": {
      "type": "stdio",
      "command": "uvx",
      "args": [
        "--from",
        "git+https://github.com/oraios/serena",
        "serena",
        "start-mcp-server",
        "--context",
        "claude-code",
        "--project-from-cwd"
      ],
      "env": {
        "ENABLE_TOOL_SEARCH": "true"
      }
    }
  }
}
```

**方法3: グローバルインストール**

すべてのプロジェクトでSerenaを使用する場合：

```bash
claude mcp add --scope user serena -- uvx --from git+https://github.com/oraios/serena serena start-mcp-server --context=claude-code --project-from-cwd
```

### 2.3 動作確認

Claude Codeを起動して、Serenaが認識されているか確認：

```bash
claude

# Claude Code内で
「serenaのツールを使ってこのプロジェクトのシンボル一覧を表示して」
```

---

## 3. 設定オプション

### 3.1 コンテキスト設定

`--context` オプションで動作モードを指定：

| コンテキスト | 説明 |
|-------------|------|
| `claude-code` | Claude Code向け最適化。不要なツールを無効化 |
| `ide-assistant` | IDE拡張向け。フル機能 |
| `minimal` | 最小構成 |

### 3.2 プロジェクト設定

| オプション | 説明 |
|-----------|------|
| `--project <path>` | プロジェクトディレクトリを明示的に指定 |
| `--project-from-cwd` | カレントディレクトリをプロジェクトとして自動検出 |

### 3.3 環境変数

| 変数 | 説明 | 推奨値 |
|------|------|--------|
| `ENABLE_TOOL_SEARCH` | オンデマンドツール読み込み（トークン節約） | `true` |

### 3.4 プロジェクト固有設定

`.serena/project.yml` を作成して詳細設定：

```yaml
# .serena/project.yml
name: my-project
language_servers:
  - typescript-language-server
  - pyright
exclude_patterns:
  - "**/node_modules/**"
  - "**/dist/**"
  - "**/.git/**"
```

---

## 4. 提供ツール

### 4.1 シンボル検索

| ツール | 説明 | 使用例 |
|--------|------|--------|
| `get_symbols` | モジュール/クラス/関数の一覧取得 | 「このファイルのシンボル一覧を表示」 |
| `find_symbol` | 特定のシンボルを検索 | 「UserService クラスを探して」 |
| `semantic_search` | 意味的に関連するコードを検索 | 「認証に関連するコードを探して」 |

### 4.2 ナビゲーション

| ツール | 説明 | 使用例 |
|--------|------|--------|
| `go_to_definition` | シンボルの定義箇所にジャンプ | 「この関数の定義を見せて」 |
| `find_references` | シンボルの使用箇所を検索 | 「この関数はどこで使われている？」 |
| `get_call_hierarchy` | 呼び出し階層を取得 | 「この関数の呼び出し元を表示」 |

### 4.3 情報取得

| ツール | 説明 | 使用例 |
|--------|------|--------|
| `get_hover_info` | 型情報、ドキュメントを表示 | 「この変数の型を教えて」 |
| `get_diagnostics` | 静的解析エラー・警告を取得 | 「このファイルの問題を確認」 |

### 4.4 編集（code-builder専用）

| ツール | 説明 | 使用例 |
|--------|------|--------|
| `apply_edit` | セマンティックな編集（リネーム等） | 「この関数名を変更して」 |
| `insert_after_symbol` | シンボルの後にコードを挿入 | 「このクラスにメソッドを追加」 |

---

## 5. サブエージェントとの連携

### 5.1 エージェント別Serena活用

| エージェント | Serena使用 | 主な用途 |
|-------------|-----------|----------|
| **code-guide** | ✅ 必要（読み取り専用） | シンボル検索、定義ジャンプでナビゲーション精度向上 |
| **code-builder** | ✅ 必要（フル機能） | セマンティック編集、影響範囲の特定 |
| **code-reviewer** | ✅ 必要（読み取り専用） | 参照関係の追跡、診断情報の取得 |
| **code-debugger** | ✅ 必要（読み取り専用） | コールスタック追跡、原因追跡 |
| **general-purpose** | ⚠️ オプション | 必要に応じて使用 |
| **その他** | ❌ 不要 | 基本ツールで十分 |

### 5.2 連携フロー

```
┌──────────────────────────────────────────────────────────────┐
│                    開発フローでのSerena活用                    │
└──────────────────────────────────────────────────────────────┘

[要求分析] req-analyzer
    │ ※ Serena不要（仕様レベルの作業）
    ▼
[仕様策定] spec-planner
    │ ※ Serena不要（仕様レベルの作業）
    ▼
[設計] backend-designer / frontend-designer
    │ ※ Serena不要（設計レベルの作業）
    ▼
[実装] code-builder ← Serenaフル活用
    │   - get_symbols: 既存構造の把握
    │   - find_references: 影響範囲の確認
    │   - apply_edit: セマンティックなリファクタリング
    │   - get_diagnostics: エラー確認
    ▼
[レビュー] code-reviewer ← Serena読み取り活用
    │   - get_call_hierarchy: 呼び出し関係の確認
    │   - find_references: 変更の影響範囲確認
    │   - get_diagnostics: 静的解析結果の確認
    ▼
[デバッグ] code-debugger ← Serena読み取り活用
        - go_to_definition: 原因追跡
        - get_call_hierarchy: コールスタック分析
        - get_hover_info: 型情報確認
```

---

## 6. 使用例

### 6.1 コード理解（code-guide）

```
「認証機能の実装箇所を教えて」

→ code-guide が Serena を使用：
  1. semantic_search で「認証」に関連するコードを検索
  2. get_symbols で関連クラス・関数を一覧化
  3. go_to_definition で各シンボルの定義を確認
```

### 6.2 リファクタリング（code-builder）

```
「UserService クラスの getUser メソッドを getUserById に変更して」

→ code-builder が Serena を使用：
  1. find_symbol で対象メソッドを特定
  2. find_references で全ての使用箇所を確認
  3. apply_edit でシンボルレベルでリネーム
  4. get_diagnostics で変更後のエラーがないか確認
```

### 6.3 バグ調査（code-debugger）

```
「この NullPointerException の原因を調査して」

→ code-debugger が Serena を使用：
  1. go_to_definition でエラー発生箇所から原因を追跡
  2. get_call_hierarchy で呼び出し元を確認
  3. get_hover_info で変数の型情報を確認
  4. find_references で関連する他の使用箇所を調査
```

### 6.4 コードレビュー（code-reviewer）

```
「この PR の変更をレビューして」

→ code-reviewer が Serena を使用：
  1. get_diagnostics で静的解析の問題を確認
  2. find_references で変更の影響範囲を確認
  3. get_call_hierarchy で呼び出し関係の変化を確認
```

---

## 7. トラブルシューティング

### 7.1 Serenaが起動しない

**症状**: Claude Codeでserenaのツールが認識されない

**原因と対策**:

| 原因 | 対策 |
|------|------|
| uvがインストールされていない | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| .mcp.jsonの構文エラー | JSONの構文を確認 |
| ネットワーク問題 | GitHubへのアクセスを確認 |

```bash
# 手動でSerenaを起動してテスト
uvx --from git+https://github.com/oraios/serena serena start-mcp-server --help
```

### 7.2 言語サーバーが動作しない

**症状**: 特定の言語でシンボル検索ができない

**対策**:

1. 対象言語の言語サーバーがインストールされているか確認
2. `.serena/project.yml` で言語サーバーを明示的に指定

```yaml
# .serena/project.yml
language_servers:
  - typescript-language-server  # TypeScript/JavaScript
  - pyright                     # Python
  - rust-analyzer               # Rust
  - gopls                       # Go
```

### 7.3 パフォーマンスが遅い

**症状**: Serenaの応答が遅い

**対策**:

1. `ENABLE_TOOL_SEARCH=true` を設定（トークン節約モード）
2. 不要なディレクトリを除外

```yaml
# .serena/project.yml
exclude_patterns:
  - "**/node_modules/**"
  - "**/dist/**"
  - "**/build/**"
  - "**/.git/**"
```

---

## 8. ベストプラクティス

### 8.1 使い分けの判断基準

| シチュエーション | 推奨ツール |
|----------------|-----------|
| ファイル名でファイルを探す | Glob（基本ツール） |
| キーワードでコードを探す | Grep（基本ツール） |
| 関数/クラスの定義を探す | Serena `find_symbol` |
| 関数の使用箇所を探す | Serena `find_references` |
| 変数の型を確認する | Serena `get_hover_info` |
| リネームリファクタリング | Serena `apply_edit` |

### 8.2 効果的な活用パターン

**大規模コードベースでの調査**:

```
1. semantic_search で大まかな範囲を特定
2. get_symbols で構造を把握
3. go_to_definition で詳細を確認
4. find_references で関連箇所を網羅
```

**安全なリファクタリング**:

```
1. find_references で影響範囲を事前確認
2. get_diagnostics で現状のエラーを確認
3. apply_edit でセマンティックに編集
4. get_diagnostics で新規エラーがないか確認
```

### 8.3 Serenaを使わない方がよい場合

| シチュエーション | 理由 |
|----------------|------|
| 単純なテキスト検索 | Grepの方が高速 |
| 設定ファイルの編集 | 基本ツールで十分 |
| 新規ファイル作成 | Writeツールを使用 |
| 小規模プロジェクト | オーバーヘッドが大きい |

---

## 参考リンク

- [Serena 公式リポジトリ](https://github.com/oraios/serena)
- [Serena ドキュメント](https://oraios.github.io/serena/)
- [サブエージェントガイド](./05-claude-subagent-guide.md)
- [Command・Skill・Agent 使い分けガイド](./07-command-skill-agent-guide.md)

---

*このガイドは clauto-develop フレームワークの一部です。*
