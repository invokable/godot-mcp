# Godot MCP

[![Github-sponsors](https://img.shields.io/badge/sponsor-30363D?style=for-the-badge&logo=GitHub-Sponsors&logoColor=#EA4AAA)](https://github.com/sponsors/Coding-Solo)

[![](https://badge.mcpx.dev?type=server 'MCP Server')](https://modelcontextprotocol.io/introduction)
[![Made with Godot](https://img.shields.io/badge/Made%20with-Godot-478CBF?style=flat&logo=godot%20engine&logoColor=white)](https://godotengine.org)
[![](https://img.shields.io/badge/Node.js-339933?style=flat&logo=nodedotjs&logoColor=white 'Node.js')](https://nodejs.org/en/download/)
[![](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white 'TypeScript')](https://www.typescriptlang.org/)

[![](https://img.shields.io/github/last-commit/Coding-Solo/godot-mcp 'Last Commit')](https://github.com/Coding-Solo/godot-mcp/commits/main)
[![](https://img.shields.io/github/stars/Coding-Solo/godot-mcp 'Stars')](https://github.com/Coding-Solo/godot-mcp/stargazers)
[![](https://img.shields.io/github/forks/Coding-Solo/godot-mcp 'Forks')](https://github.com/Coding-Solo/godot-mcp/network/members)
[![](https://img.shields.io/badge/License-MIT-red.svg 'MIT License')](https://opensource.org/licenses/MIT)

```text
                           (((((((             (((((((                          
                        (((((((((((           (((((((((((                      
                        (((((((((((((       (((((((((((((                       
                        (((((((((((((((((((((((((((((((((                       
                        (((((((((((((((((((((((((((((((((                       
         (((((      (((((((((((((((((((((((((((((((((((((((((      (((((        
       (((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((      
     ((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((    
    ((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((    
      (((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((     
        (((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((       
         (((((((((((@@@@@@@(((((((((((((((((((((((((((@@@@@@@(((((((((((        
         (((((((((@@@@,,,,,@@@(((((((((((((((((((((@@@,,,,,@@@@(((((((((        
         ((((((((@@@,,,,,,,,,@@(((((((@@@@@(((((((@@,,,,,,,,,@@@((((((((        
         ((((((((@@@,,,,,,,,,@@(((((((@@@@@(((((((@@,,,,,,,,,@@@((((((((        
         (((((((((@@@,,,,,,,@@((((((((@@@@@((((((((@@,,,,,,,@@@(((((((((        
         ((((((((((((@@@@@@(((((((((((@@@@@(((((((((((@@@@@@((((((((((((        
         (((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((        
         (((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((((        
         @@@@@@@@@@@@@((((((((((((@@@@@@@@@@@@@((((((((((((@@@@@@@@@@@@@        
         ((((((((( @@@(((((((((((@@(((((((((((@@(((((((((((@@@ (((((((((        
         (((((((((( @@((((((((((@@@(((((((((((@@@((((((((((@@ ((((((((((        
          (((((((((((@@@@@@@@@@@@@@(((((((((((@@@@@@@@@@@@@@(((((((((((         
           (((((((((((((((((((((((((((((((((((((((((((((((((((((((((((          
              (((((((((((((((((((((((((((((((((((((((((((((((((((((             
                 (((((((((((((((((((((((((((((((((((((((((((((((                
                        (((((((((((((((((((((((((((((((((                       
                                                                                

                          /$$      /$$  /$$$$$$  /$$$$$$$ 
                         | $$$    /$$$ /$$__  $$| $$__  $$
                         | $$$$  /$$$$| $$  \__/| $$  \ $$
                         | $$ $$/$$ $$| $$      | $$$$$$$/
                         | $$  $$$| $$| $$      | $$____/ 
                         | $$\  $ | $$| $$    $$| $$      
                         | $$ \/  | $$|  $$$$$$/| $$      
                         |__/     |__/ \______/ |__/       
```

Godotゲームエンジンと連携するためのModel Context Protocol (MCP) サーバーです。

## はじめに

Godot MCPは、AIアシスタントがGodotエディターの起動、プロジェクトの実行、デバッグ出力の取得、プロジェクト実行の制御を標準化されたインターフェースを通じて行えるようにします。

この直接的なフィードバックループにより、ClaudeなどのAIアシスタントが実際のGodotプロジェクトで何が機能し何が機能しないかを理解できるようになり、より良いコード生成とデバッグ支援が可能になります。

## 機能

- **Godotエディターの起動**: 特定のプロジェクトに対してGodotエディターを開く
- **Godotプロジェクトの実行**: デバッグモードでGodotプロジェクトを実行
- **デバッグ出力の取得**: コンソール出力とエラーメッセージを取得
- **実行制御**: プログラム的にGodotプロジェクトを開始・停止
- **Godotバージョンの取得**: インストールされているGodotのバージョンを取得
- **Godotプロジェクトの一覧表示**: 指定されたディレクトリ内のGodotプロジェクトを検索
- **プロジェクト解析**: プロジェクト構造に関する詳細情報を取得
- **シーン管理**:
  - 指定したルートノードタイプで新しいシーンを作成
  - カスタマイズ可能なプロパティを持つノードを既存のシーンに追加
  - Sprite2Dノードにスプライトとテクスチャを読み込む
  - 3DシーンをGridMap用のMeshLibraryリソースとしてエクスポート
  - バリアント作成オプション付きでシーンを保存
  - 階層的なノード情報、プロパティ、シグナル接続を含むシーン構造を解析
- **UID管理** (Godot 4.4以降向け):
  - 特定のファイルのUIDを取得
  - リソースを再保存してUID参照を更新

## 必要要件

- システムに[Godot Engine](https://godotengine.org/download)がインストールされていること
- Node.jsとnpm
- MCPをサポートするAIアシスタント(Cline、Cursorなど)

## インストールと設定

### ステップ1: インストールとビルド

まず、リポジトリをクローンしてMCPサーバーをビルドします:

```bash
git clone https://github.com/Coding-Solo/godot-mcp.git
cd godot-mcp
npm install
npm run build
```

### ステップ2: AIアシスタントの設定

#### オプションA: Clineで設定

ClineのMCP設定ファイル(`~/Library/Application Support/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`)に追加:

```json
{
  "mcpServers": {
    "godot": {
      "command": "node",
      "args": ["/absolute/path/to/godot-mcp/build/index.js"],
      "env": {
        "DEBUG": "true"                  // オプション: 詳細ログを有効化
      },
      "disabled": false,
      "autoApprove": [
        "launch_editor",
        "run_project",
        "get_debug_output",
        "stop_project",
        "get_godot_version",
        "list_projects",
        "get_project_info",
        "create_scene",
        "add_node",
        "load_sprite",
        "export_mesh_library",
        "save_scene",
        "get_uid",
        "update_project_uids",
        "get_scene_structure"
      ]
    }
  }
}
```

#### オプションB: Cursorで設定

**Cursor UIを使用:**

1. **Cursor Settings** > **Features** > **MCP**に移動
2. **+ Add New MCP Server**ボタンをクリック
3. フォームに入力:
   - Name: `godot` (または任意の名前)
   - Type: `command`
   - Command: `node /absolute/path/to/godot-mcp/build/index.js`
4. 「Add」をクリック
5. MCPサーバーカードの右上にあるリフレッシュボタンを押してツールリストを更新する必要がある場合があります

**プロジェクト固有の設定を使用:**

プロジェクトディレクトリに`.cursor/mcp.json`ファイルを作成し、以下の内容を記述:

```json
{
  "mcpServers": {
    "godot": {
      "command": "node",
      "args": ["/absolute/path/to/godot-mcp/build/index.js"],
      "env": {
        "DEBUG": "true"                  // 詳細ログを有効化
      }
    }
  }
}
```

#### オプションC: Windsurfで設定

Windsurfの設定ファイルmcp_config.jsonに追加:

```json
{
  "mcpServers": {
    "godot": {
      "command": "node",
      "args": ["/absolute/path/to/godot-mcp/build/index.js"],
      "env": {
        "DEBUG": "true"
      }
    }
  }
}
```


### ステップ3: オプションの環境変数

以下の環境変数でサーバーの動作をカスタマイズできます:

- `GODOT_PATH`: Godot実行ファイルへのパス(自動検出を上書き)
- `DEBUG`: サーバー側の詳細デバッグログを有効にするには"true"に設定

## プロンプトの例

設定が完了すると、AIアシスタントは必要に応じて自動的にMCPサーバーを実行します。以下のようなプロンプトを使用できます:

```text
"/path/to/projectにある私のプロジェクトのGodotエディターを起動して"

"私のGodotプロジェクトを実行してエラーを表示して"

"私のGodotプロジェクトの構造情報を取得して"

"私のGodotプロジェクトの構造を解析して改善案を提案して"

"このGodotプロジェクトのエラーをデバッグするのを手伝って: [エラーを貼り付け]"

"ダブルジャンプと壁滑りのあるキャラクターコントローラーのGDScriptを書いて"

"私のGodotプロジェクトにPlayerノードを持つ新しいシーンを作成して"

"私のプレイヤーシーンにSprite2Dノードを追加してキャラクターテクスチャを読み込んで"

"GridMapで使用するために3DモデルをMeshLibraryとしてエクスポートして"

"ゲームのメインメニュー用にボタンとラベルを含むUIシーンを作成して"

"Godot 4.4プロジェクトの特定のスクリプトファイルのUIDを取得して"

"4.4にアップグレードした後、GodotプロジェクトのUID参照を更新して"

"Main.tscnのシーン構造を確認して、プレイヤーのhealth_changedシグナルがHealthBar UI要素に届かない理由を追跡して"
```

## 実装の詳細

### アーキテクチャ

Godot MCPサーバーは、複雑な操作にバンドルされたGDScriptアプローチを使用しています:

1. **直接コマンド**: エディターの起動やプロジェクト情報取得などのシンプルな操作は、Godotの組み込みCLIコマンドを直接使用します。
2. **バンドルされた操作スクリプト**: シーンの作成やノードの追加などの複雑な操作は、すべての操作を処理する単一の包括的なGDScriptファイル(`godot_operations.gd`)を使用します。

このアーキテクチャは以下のような利点を提供します:

- **一時ファイル不要**: 一時スクリプトファイルの必要性を排除し、システムをクリーンに保つ
- **簡素化されたコードベース**: すべてのGodot操作を1つの(ある程度)整理されたファイルに集約
- **優れた保守性**: 新しい操作の追加や既存の操作の変更が容易
- **改善されたエラーハンドリング**: すべての操作で一貫したエラー報告を提供
- **オーバーヘッドの削減**: ファイルI/O操作を最小化してパフォーマンスを向上

バンドルされたスクリプトは操作タイプとパラメータをJSONとして受け取り、各操作ごとに一時ファイルを生成することなく、柔軟で動的な操作実行を可能にします。

## トラブルシューティング

- **Godotが見つからない**: GODOT_PATH環境変数をGodot実行ファイルに設定してください
- **接続の問題**: サーバーが実行中であることを確認し、AIアシスタントを再起動してください
- **無効なプロジェクトパス**: パスがproject.godotファイルを含むディレクトリを指していることを確認してください
- **ビルドの問題**: `npm install`を実行してすべての依存関係がインストールされていることを確認してください
- **Cursor固有の問題**:
-   Cursor設定(Settings > MCP)でMCPサーバーが表示され有効になっていることを確認してください
-   MCPツールはAgent chatプロファイル(Cursor ProまたはBusinessサブスクリプション)でのみ実行できます
-   MCPツールリクエストを自動的に実行するには「Yolo Mode」を使用してください

## ライセンス

このプロジェクトはMITライセンスの下でライセンスされています - 詳細は[LICENSE](LICENSE)ファイルを参照してください。

[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/coding-solo-godot-mcp-badge.png)](https://mseep.ai/app/coding-solo-godot-mcp)
