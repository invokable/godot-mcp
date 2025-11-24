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

## GitHub Copilot CLI用MCP設定

macOSとWSLでパスが違うので分けて設定。  
WSLは「DドライブにWindows版をSteamでインストールしている」想定なので違うパスの場合は`src/index.ts`の`detectGodotPath()`を修正。  
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