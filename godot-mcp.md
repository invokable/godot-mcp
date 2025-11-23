# Godot MCP サーバー 技術解説

## 概要

Godot MCPは、Node.js製のModel Context Protocol (MCP)サーバーで、AIアシスタント（ClaudeやCursorなど）がGodotゲームエンジンと対話できるようにします。TypeScriptで記述され、Godot 4.x系と統合されています。

## プロジェクト構成

```
godot-mcp/
├── src/
│   ├── index.ts              # メインサーバー実装（約2200行）
│   └── scripts/
│       └── godot_operations.gd  # Godot操作用GDScriptバンドル（約1200行）
├── build/                    # TypeScriptコンパイル出力
├── package.json              # プロジェクト設定
└── tsconfig.json            # TypeScript設定
```

## MCPツール一覧

### 1. プロジェクト管理ツール

#### `launch_editor`
- **機能**: 指定したプロジェクトのGodotエディタを起動
- **パラメータ**: `projectPath` (プロジェクトディレクトリパス)
- **実装**: `spawn`でGodotを`-e --path`オプション付きで起動

#### `list_projects`
- **機能**: ディレクトリ内のGodotプロジェクトを検索
- **パラメータ**: `directory`, `recursive` (再帰検索フラグ)
- **実装**: `project.godot`ファイルの存在をチェックして一覧化

#### `get_project_info`
- **機能**: プロジェクトの詳細情報を取得
- **戻り値**: プロジェクト名、パス、Godotバージョン、構成（シーン数、スクリプト数、アセット数）
- **実装**: `project.godot`を解析し、ファイル拡張子でカウント

### 2. プロジェクト実行・デバッグツール

#### `run_project`
- **機能**: デバッグモードでGodotプロジェクトを実行
- **パラメータ**: `projectPath`, `scene` (オプション)
- **実装**: `spawn`で`-d --path`オプション付きで起動し、stdout/stderrをキャプチャ

#### `get_debug_output`
- **機能**: 実行中プロジェクトの出力とエラーを取得
- **実装**: アクティブプロセスの蓄積された出力を返却

#### `stop_project`
- **機能**: 実行中のGodotプロジェクトを停止
- **実装**: アクティブプロセスを`kill()`で終了

### 3. シーン管理ツール

#### `create_scene`
- **機能**: 新しいシーンファイル(.tscn)を作成
- **パラメータ**: 
  - `projectPath`: プロジェクトパス
  - `scenePath`: シーンの保存先パス
  - `rootNodeType`: ルートノードタイプ（デフォルト: Node2D）
- **実装**: GDScriptバンドルの`create_scene`関数を呼び出し

#### `add_node`
- **機能**: 既存シーンにノードを追加
- **パラメータ**:
  - `projectPath`, `scenePath`: シーンの位置
  - `parentNodePath`: 親ノードパス（デフォルト: "root"）
  - `nodeType`: 追加するノードタイプ
  - `nodeName`: ノード名
  - `properties`: ノードプロパティ（オプション）
- **実装**: GDScriptでシーンをロード→ノード追加→再保存

#### `save_scene`
- **機能**: シーンへの変更を保存
- **パラメータ**: `projectPath`, `scenePath`, `newPath` (バリアント作成用)
- **実装**: シーンを再パックして保存

### 4. アセット管理ツール

#### `load_sprite`
- **機能**: Sprite2Dノードにテクスチャをロード
- **パラメータ**:
  - `projectPath`, `scenePath`: シーンの位置
  - `nodePath`: Sprite2Dノードのパス
  - `texturePath`: テクスチャファイルパス
- **実装**: GDScriptでシーンをロード→テクスチャ設定→保存

#### `export_mesh_library`
- **機能**: 3DシーンをMeshLibraryリソースとしてエクスポート（GridMap用）
- **パラメータ**:
  - `projectPath`: プロジェクトパス
  - `scenePath`: エクスポート元シーン(.tscn)
  - `outputPath`: MeshLibrary出力先(.res)
  - `meshItemNames`: 特定メッシュ名のリスト（オプション）
- **実装**: GDScriptで全子ノードをスキャン→MeshInstance3Dを抽出→MeshLibraryに変換

### 5. UID管理ツール（Godot 4.4+専用）

#### `get_uid`
- **機能**: 特定ファイルのUIDを取得
- **パラメータ**: `projectPath`, `filePath`
- **実装**: `.uid`ファイルを読み取り
- **バージョンチェック**: Godot 4.4未満では拒否

#### `update_project_uids`
- **機能**: プロジェクト全体のUID参照を更新
- **パラメータ**: `projectPath`
- **実装**: 全リソースを再保存してUIDを生成・更新
- **対象**: .tscn, .gd, .shader, .gdshaderファイル

### 6. バージョン情報

#### `get_godot_version`
- **機能**: インストールされているGodotのバージョンを取得
- **実装**: `godot --version`コマンドを実行

## Godot操作の仕組み

### アーキテクチャ: バンドル方式

従来の一時ファイル方式と異なり、**単一のGDScriptファイル**（`godot_operations.gd`）に全操作を集約：

1. **コマンド構造**:
   ```bash
   godot --headless --path <project_path> --script godot_operations.gd <operation> <json_params>
   ```

2. **パラメータ形式**:
   - TypeScript側: `camelCase`形式のパラメータを受け取り
   - 変換層: `snake_case`に自動変換（GDScript規約に合わせる）
   - JSON文字列としてシリアライズし、コマンドライン引数として渡す

3. **実行フロー**:
   ```
   MCP Client (AI) 
     ↓ ツール呼び出し
   TypeScript Server (index.ts)
     ↓ パラメータ正規化（camelCase → snake_case）
     ↓ Godot起動（--headless --script）
   GDScript Bundle (godot_operations.gd)
     ↓ JSON解析、操作実行
   Godot Engine
     ↓ リソース操作（シーン作成・編集・保存）
   ファイルシステム
     ↓ stdout/stderr
   TypeScript Server
     ↓ レスポンス整形
   MCP Client
   ```

### GDScriptバンドルの主要機能

#### 1. ノードインスタンス化 (`instantiate_class`)
```gdscript
func instantiate_class(name_of_class):
    # ClassDB（組み込みクラス）チェック
    if ClassDB.class_exists(name_of_class):
        return ClassDB.instantiate(name_of_class)
    
    # カスタムスクリプトチェック
    var script = get_script_by_name(name_of_class)
    if script is GDScript:
        return script.new()
```

#### 2. シーン操作パイプライン
```gdscript
# シーン作成
1. ルートノードをインスタンス化
2. owner設定（scene_root.owner = scene_root）
3. PackedScene.pack()でパック化
4. ResourceSaver.save()で保存

# ノード追加
1. load()でシーンをロード
2. scene.instantiate()でインスタンス化
3. 親ノードを取得（get_node()）
4. add_child()で追加、owner設定
5. 再パック・保存
```

#### 3. ファイルシステム処理
- **パス正規化**: 常に`res://`プレフィックスを付与
- **ディレクトリ作成**: `DirAccess.make_dir_recursive()`で自動作成
- **存在確認**: `FileAccess.file_exists()`と`DirAccess.dir_exists_absolute()`を併用

### Godot自動検出

TypeScript側で複数の検出戦略を実装：

1. **環境変数チェック**: `GODOT_PATH`
2. **PATH検索**: `godot`コマンドが実行可能か確認
3. **プラットフォーム固有パス**:
   - **macOS**: `/Applications/Godot.app/Contents/MacOS/Godot`
   - **Windows**: `C:\Program Files\Godot\Godot.exe`
   - **Linux**: `/usr/bin/godot`, `/snap/bin/godot`
4. **Steam版**: `~/Library/Application Support/Steam/steamapps/common/Godot Engine/`

検証方法: `godot --version`の実行成功をチェック

### エラーハンドリング

#### TypeScript側
- パス検証（`..`を含むパストラバーサル攻撃を防止）
- Godot実行可能ファイルの存在確認
- `project.godot`ファイルの検証
- 詳細なエラーメッセージと解決策の提示

#### GDScript側
- デバッグモード（`--debug-godot`フラグで有効化）
- 各ステップでのログ出力（`log_debug`, `log_info`, `log_error`）
- ファイル操作の検証（保存後に`file_exists()`で確認）
- エラーコードの詳細化（`ERR_CANT_CREATE`, `ERR_FILE_NO_PERMISSION`など）

## パラメータ正規化システム

### camelCase ⇄ snake_case マッピング
```typescript
private parameterMappings: Record<string, string> = {
  'project_path': 'projectPath',
  'scene_path': 'scenePath',
  'root_node_type': 'rootNodeType',
  'parent_node_path': 'parentNodePath',
  'node_type': 'nodeType',
  'node_name': 'nodeName',
  'texture_path': 'texturePath',
  // ... 他のマッピング
};
```

- MCP API: camelCase（JavaScriptの慣習）
- GDScript: snake_case（Godot/Pythonの慣習）
- 双方向変換を自動化し、両者の規約を尊重

## デバッグ機能

### TypeScript側
- `DEBUG="true"` 環境変数で有効化
- Godotコマンド、パラメータ、出力を詳細ログ
- プロセス管理の状態追跡

### GDScript側
- `--debug-godot`フラグで有効化（TypeScript側から自動付与）
- ファイル操作の各ステップを追跡
- パス解決の詳細情報
- リソース保存の検証

## セキュリティ対策

1. **パストラバーサル防止**: `validatePath()`で`..`を拒否
2. **パス正規化**: `normalize()`で一貫したパス形式
3. **プロセス分離**: 各Godot操作は独立したヘッドレスプロセス
4. **エラーサニタイゼーション**: センシティブ情報の漏洩防止

## パフォーマンス最適化

1. **パス検証キャッシュ**: 一度検証したGodotパスをメモリに保持
2. **非同期実行**: `execAsync`で非ブロッキング
3. **プロセス再利用**: エディタ起動は別プロセス、操作は都度ヘッドレス起動
4. **最小限の一時ファイル**: バンドル方式でファイルI/Oを削減

## 依存関係

### Node.js側
- `@modelcontextprotocol/sdk`: MCPプロトコル実装
- `axios`: HTTP通信（将来の拡張用）
- `fs-extra`: ファイルシステム操作拡張

### Godot側
- Godot 4.x以上（UID機能は4.4+）
- ヘッドレスモード対応
- GDScript 2.0 API

## 将来の拡張可能性

1. **リソースインポート**: アセットの自動インポート・設定
2. **ビルドパイプライン**: エクスポートプリセットの管理
3. **プラグイン管理**: エディタプラグインの有効化/無効化
4. **プロジェクトテンプレート**: 定型プロジェクトの自動生成
5. **リアルタイムコラボレーション**: 複数AIの同時編集

## まとめ

Godot MCPは、**AIとゲームエンジンの橋渡し**を実現する革新的なツールです。バンドル方式のアーキテクチャにより、一時ファイルを作成せずに複雑なGodot操作を実行できます。TypeScriptとGDScriptの双方向通信により、AIアシスタントがGodotプロジェクトの作成・編集・デバッグを自律的に行えます。

このプロジェクトは、**AIによるゲーム開発の自動化**という新しい可能性を切り開いています。
