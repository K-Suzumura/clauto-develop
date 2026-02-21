# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [v0.5.0] - 2026-02-21

### Changed

- **Serena MCP の位置づけ変更**: 「必要」→「推奨（特定条件で有効）」に変更
  - 7エージェント（code-builder, code-reviewer, code-debugger, code-guide, backend-designer, frontend-designer, tech-leader）の Serena セクションを `✅ 必要` から `⚡ 推奨` に更新
  - 統合ガイド（JA/EN）のセクション 1.2 を「なぜSerenaが必要か」→「Serenaが有効なケース」に変更
  - README の Serena セクションを更新し「推奨条件」列を追加
- バージョンを 1.3.0 に更新

### Added

- **導入判断フローチャート**（セクション 1.4）: プロジェクト規模・ポリグロット・継続作業の3軸で判断
- **プロジェクトメモリ**（セクション 3.5）: `.serena/memories/` ディレクトリのドキュメント
- **事前インデックス**（セクション 3.6）: `serena project index` コマンドのドキュメント
- **言語別の追加要件**（セクション 3.7）: Go, Rust, Vue の要件を明記
- **ヘルスチェック**（セクション 3.8）: `serena health` コマンドのドキュメント

## [v0.4.0] - 2026-02-21

### Changed

- **コマンド→スキル統合**: 13個のカスタムコマンドをスキル形式（`SKILL.md`）に移行
  - `global-commands/` を廃止し、`global-skills/` に統合
  - `gh:commit-push-pr` を `commit-push-pr` にリネーム
  - 各スキルに `user-invocable`, `argument-hint`, `allowed-tools` フィールドを追加
- **非標準フィールド除去**: スキルの frontmatter から `related_commands`, `related_agents` を除去
- **エージェントに `skills` フィールド追加**: 5つのエージェントにスキル事前ロードを設定
  - `code-builder`: `coding-standards`, `test-author`
  - `code-reviewer`: `pr-reviewer`, `security-baseline`, `coding-standards`
  - `code-debugger`: `debug-triage`
  - `spec-planner`: `spec-reviewer`
  - `tech-leader`: `architecture-reviewer`
- **プラグインパッケージ更新**: `commands` ディレクトリを廃止し、全スキルを `skills/` に統合
- バージョンを 1.2.0 に更新

### Removed

- `global-commands/` ディレクトリ（スキルに統合）
- `plugins/clauto-develop/commands/` ディレクトリ（スキルに統合）
- plugin.json の `"commands"` フィールド

### Reference

- [Claude Code Skills Documentation](https://docs.anthropic.com/en/docs/claude-code/skills)
- [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

## [v0.3.0] - 2026-02-06

### Added

- **YAML Frontmatter**: 全スキル、コマンド、エージェントに YAML frontmatter を追加
  - スキル: kebab-case の英語名、description
  - コマンド: name, description, disable-model-invocation
  - エージェント: name, description, tools, model
- **MCP サーバー統合**: plugin.json に Serena MCP サーバー設定を追加
- **ベストプラクティス参照**: README に公式ベストプラクティスへのリンクを追加
- **推奨セットアップ**: `/init` コマンドの使用を推奨するセクションを追加

### Changed

- **CLAUDE.md.template 簡素化**: ベストプラクティスに従い大幅に簡素化
  - 検証コマンドセクションを追加（最重要）
  - コンパクト時の指示を追加
  - 冗長なセクションを削除
- **エージェント表の改善**: モデルとツールの情報を追加
- バージョンを 1.1.0 に更新

### Reference

- [Best Practices for Claude Code](https://code.claude.com/docs/en/best-practices)

## [v0.2.0] - 2026-02-06

### Added

- **Plugin Marketplace 対応**: Claude Code Plugin Marketplace 形式での配布に対応
  - `.claude-plugin/marketplace.json` - マーケットプレース定義ファイル
  - `plugins/clauto-develop/` - プラグインパッケージ構造
  - `/plugin marketplace add` と `/plugin install` でインストール可能に
- **カスタムスキル（9種類）**: `global-skills/` ディレクトリに追加
  - `architecture-reviewer` - アーキテクチャレビュー
  - `coding-standards` - コーディング規約
  - `debug-triage` - デバッグトリアージ
  - `dependency-change-reviewer` - 依存パッケージ変更レビュー
  - `pr-reviewer` - PRレビュー
  - `release-notes-writer` - リリースノート作成
  - `security-baseline` - セキュリティベースライン
  - `spec-reviewer` - 仕様書レビュー
  - `test-author` - テスト作成
- **カスタムコマンド（13種類）**: `global-commands/` ディレクトリに追加
  - `/spec:init`, `/spec:review` - 仕様書関連
  - `/plan:make`, `/impl:run` - 実装関連
  - `/git:commit`, `/git:pr`, `/git:branch` - Git関連
  - `/gh:commit-push-pr` - 統合ワークフロー
  - `/qa:full`, `/refactor:cleanup` - 品質関連
  - `/fixup-from-pr-comments` - PRコメント対応
  - `/session:compact-smart` - セッション管理
  - `/ultrathink` - 深い思考モード
- `README.md.template` - 新規プロジェクト用READMEテンプレート

### Changed

- `CLAUDE.md.template` をプロジェクト設定に特化した内容に整理
- `/commit-push-pr` を `/gh:commit-push-pr` にリネーム
- README.md を更新し、Plugin Marketplace 経由のインストール方法を追加

### Fixed

- Use this template リンクを正しいオーナー名に修正

## [v0.1.0] - 2026-01-XX

### Added

- **サブエージェント定義（10種類）**: `.claude/agents/` ディレクトリ
  - `req-analyzer` - 要求分析者
  - `spec-planner` - 仕様策定者
  - `tech-leader` - テックリード
  - `backend-designer` - バックエンド設計者
  - `frontend-designer` - フロントエンド設計者
  - `code-builder` - コードビルダー
  - `code-reviewer` - コードレビューワー
  - `code-debugger` - コードデバッガー
  - `code-guide` - コードガイド
  - `general-purpose` - 汎用エージェント
- **フレームワークドキュメント（日英）**: `framework-docs/`
  - ECサイト構築チュートリアル
  - ディレクトリ構成ガイド
  - Claude Code コマンドガイド
  - スキル・サブエージェントガイド
  - カスタムコマンドガイド
  - Serena MCP 統合ガイド
- **Serena MCP 統合**: `.mcp.json` 設定ファイル
- **GitHub テンプレート**: Issue/PR テンプレート
- `CLAUDE.md.template` - Claude Code 設定テンプレート
- Apache License 2.0

---

## インストール方法

### v0.2.0 以降（推奨）

```bash
# Plugin Marketplace からインストール
/plugin marketplace add K-Suzumura/clauto-develop
/plugin install clauto-develop@clauto-develop
```

### v0.1.0（レガシー）

```bash
# Template Repository としてクローン
git clone https://github.com/K-Suzumura/clauto-develop.git
cp CLAUDE.md.template CLAUDE.md
```

[v0.2.0]: https://github.com/K-Suzumura/clauto-develop/compare/v0.1.0...v0.2.0
[v0.1.0]: https://github.com/K-Suzumura/clauto-develop/releases/tag/v0.1.0
