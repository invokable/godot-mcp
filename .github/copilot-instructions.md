# Godot MCPサーバー フォーク版

https://github.com/Coding-Solo/godot-mcp をフォークして独自にメンテナンスしていくプロジェクト。ローカルで動かすMCPサーバーなのでフォーク版でも問題なく使える。

## プロジェクト構成

```
godot-mcp/
├── src/
│   ├── index.ts              # メインサーバー実装
│   └── scripts/
│       └── godot_operations.gd  # Godot操作用GDScriptバンドル
├── scripts/
│   └── build.js              # ビルドスクリプト
├── build/                    # TypeScriptコンパイル出力
├── package.json              # npm設定
└── tsconfig.json            # TypeScript設定
```

## コマンド

```shell
npm run build    # TypeScriptをコンパイルしてbuild/に出力
```

`src`内を変更後は`build`コマンドを実行する。

## プルリクエストの取り込み

主な作業は元のリポジトリへのプルリクエストを取り込むことなので
「https://github.com/Coding-Solo/godot-mcp/pull/* を取り込んでマージして」の指示に対して以下の作業を行う。

### 手順

1. **PRの情報取得**
   ```bash
   # GitHub APIでPRの詳細とdiffを取得
   gh pr view {PR番号} --repo Coding-Solo/godot-mcp
   ```

2. **PRブランチのフェッチ**
   ```bash
   # upstreamリモートからPRをローカルブランチとしてフェッチ
   git fetch upstream pull/{PR番号}/head:pr-{PR番号}
   ```

3. **マージの実行**
   ```bash
   # 現在のブランチにマージ（通常はpatchブランチ）
   git merge pr-{PR番号} --no-edit
   ```

4. **ビルドとテスト**
   ```bash
   # TypeScriptのコンパイル
   npm run build
   
   # 動作確認（必要に応じて）
   node build/index.js
   ```

5. **確認**
   ```bash
   # マージコミットの確認
   git log --oneline -5
   
   # 変更内容の確認
   git diff HEAD~1 HEAD
   ```

### 注意点

- `upstream`リモートは `https://github.com/Coding-Solo/godot-mcp.git` を指している必要がある
- オリジナルでマージされてないバラバラなプルリクエストを取り込むのでコンフリクトが発生することが多い
- `@modelcontextprotocol/sdk`をバージョンアップして`src/index.ts`のコードがかなり変わっているのでそのままマージはできない場合が多い
- 競合が発生した場合は手動で解決してからコミット
- マージ後は必ず`npm run build`を実行してビルド成功を確認
- README.mdが変更された場合は日本語版`README_ja.md`も更新すること

## GitHub Copilot CLI用MCP設定

macOSとWSLでパスが違うので分けて設定。Godotプロジェクトが少ないならプロジェクトローカルの`.github/mcp-config.json`で設定。多いならグローバルな`~/.copilot/mcp-config.json`で設定。  
WSLは「DドライブにWindows版をSteamでインストールしている」想定なので違うパスの場合は`src/index.ts`の`detectGodotPath()`のlinux用パスを修正。  
MCP設定の`env`で指定してもおそらく動かない。環境変数`GODOT_PATH`の指定でも動かない。Copilot CLIのバグかもしれない。

```json
{
  "mcpServers": {
    "godot-mac": {
      "tools": [
        "*"
      ],
      "type": "local",
      "command": "node",
      "args": [
        "/Users/{user}/**/godot-mcp/build/index.js"
      ]
    },
    "godot-win": {
      "tools": [
        "*"
      ],
      "type": "local",
      "command": "node",
      "args": [
        "/home/{user}/**/godot-mcp/build/index.js"
      ]
    }
  }
}
```
