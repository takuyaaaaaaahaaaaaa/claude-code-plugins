# claude-code-plugins

[Claude Code](https://claude.com/claude-code) 用のプラグイン・スキル集。このリポジトリ自体がマーケットプレイスです。

## インストール方法

```bash
# 1. このリポジトリをマーケットプレイスとして登録
claude plugin marketplace add takuyaaaaaaahaaaaaa/claude-code-plugins

# 2. 使いたいプラグインをインストール
claude plugin install xcode-tools
```

## 収録プラグイン

### xcode-tools

Xcode MCP（`xrun mcpbridge`）経由でSwiftUIの`#Preview`をレンダリングし、スクリーンショットのファイルパスを取得するスキル。

- `skills/render-swiftui-preview` — 実行フロー本体。トークン節約のためHaikuサブエージェントに実行を委譲し、失敗時のみOpusアドバイザーにエスカレーションする設計
- `agents/render-preview-executor` — `RenderPreview`/`XcodeListWindows`のみにツールを絞った軽量サブエージェント

前提条件・トラブルシューティングは `xcode-tools/skills/render-swiftui-preview/references/setup-troubleshooting.md` を参照してください（Xcode 26.3以降、macOS 26.2以降が必要）。

## 更新の反映

```bash
claude plugin marketplace update claude-code-plugins
```
