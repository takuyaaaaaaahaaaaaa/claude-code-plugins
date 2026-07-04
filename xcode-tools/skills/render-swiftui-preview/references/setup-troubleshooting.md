# Xcode MCP Preview セットアップ＆トラブルシューティング

このファイルは失敗時（Opusアドバイザーへのエスカレーション時）または初回セットアップ時にのみ読み込む。通常の成功パスでは読まない。初回セットアップ・障害診断のどちらでも、以下を上から順に確認すればよい。

前提バージョン: Xcode 26.3以降、macOS 26.2以降。

## チェックリスト

1. **`xcode-select`と起動中のXcodeのバージョンが一致しているか**
   ```bash
   xcode-select -p
   ```
   起動中のXcode.appと不一致だと `tools/list` がエラーなく無限ハングする（原因特定に時間がかかりやすい）。不一致なら切り替える。`sudo`が必要で対話パスワード入力を伴うため**Claudeからは実行できない。ユーザー自身にターミナルでの実行を依頼する**。
   ```bash
   sudo xcode-select -s /Applications/Xcode.app
   ```

2. **Xcode本体でMCPが有効になっているか**
   Xcode > Settings > Intelligence > Model Context Protocol > 「Xcode Tools」がONかユーザーに確認してもらう

3. **対象プロジェクトがXcodeで開かれているか**
   `XcodeListWindows` が空を返す場合はプロジェクト/ワークスペースを開く必要がある

4. **Claude Code側にMCPサーバーが登録・接続されているか**
   ```bash
   claude mcp list | grep '^xcode:'
   ```
   `xcode:` で始まる行だけを見て `✔ Connected` になっているか確認する。**`claude mcp list`の出力全体を目視するのは避ける** — `XcodeBuildMCP`など他のXcode関連MCPサーバーが同時に登録されていて`✘ Failed to connect`になっていることがあり、それを`xcode`自体の障害と誤読しやすい。行を絞ってから判定すること。
   未登録・未接続なら登録する:
   ```bash
   claude mcp add --transport stdio xcode -- xcrun mcpbridge -s user
   ```
   `-s user` を付けないとプロジェクトローカルスコープになり、他プロジェクトで使えなくなる

5. **`sourceFilePath` が正しいか**
   Xcodeプロジェクト内のグループ構造基準の相対パスになっているか確認。`XcodeGlob`/`XcodeLS` で実在パスを確認する。
   よくある原因: メインループがユーザー指定のパスを「リポジトリルートからの相対パス」に書き換え、`.xcodeproj` の親ラッパーディレクトリ（例: `PictureBookLendingAdminApp/`）を余分に付加してしまうケース。`XcodeGlob(pattern="**/<ファイル名>")` で実際にXcodeから見えるパスを列挙し、それと突き合わせる。

6. **タイムアウトが足りているか**
   初回ビルドは重い。`timeout` を300〜600に伸ばして再試行する
