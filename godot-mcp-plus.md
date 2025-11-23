# Godot MCP 拡張機能提案

## 概要

Godot公式のコマンドラインドキュメントを調査した結果、現在のGodot MCPに実装されていないが追加できる有用な機能を特定しました。これらの機能を追加することで、AIアシスタントによるGodot開発の自動化がさらに強力になります。

## 🚀 優先度: 高

### 1. エクスポート・ビルド管理

#### `export_project`
**機能**: プロジェクトを指定したプラットフォームにエクスポート

**パラメータ**:
- `projectPath`: プロジェクトパス
- `presetName`: エクスポートプリセット名（例: "Windows Desktop", "Linux/X11", "Web"）
- `outputPath`: 出力ファイルパス
- `debugMode`: デバッグ/リリースモード（デフォルト: false）

**実装方法**:
```typescript
godot --export-release "Windows Desktop" /path/to/output.exe
godot --export-debug "Windows Desktop" /path/to/output.exe
```

**ユースケース**:
- CI/CDパイプラインでの自動ビルド
- 複数プラットフォーム向けの一括エクスポート
- リリース前の自動ビルド生成

#### `list_export_presets`
**機能**: プロジェクトのエクスポートプリセット一覧を取得

**実装方法**:
- `export_presets.cfg`ファイルを解析
- 各プリセットの名前、プラットフォーム、設定を抽出

**戻り値例**:
```json
[
  {
    "name": "Windows Desktop",
    "platform": "Windows Desktop",
    "runnable": true
  },
  {
    "name": "Linux/X11",
    "platform": "Linux/X11",
    "runnable": true
  }
]
```

#### `create_export_preset`
**機能**: 新しいエクスポートプリセットを作成

**パラメータ**:
- `projectPath`: プロジェクトパス
- `presetName`: プリセット名
- `platform`: プラットフォーム（"Windows Desktop", "Linux/X11", "macOS", "Web", "Android"など）
- `settings`: プリセット設定（オプション）

**実装方法**:
- GDScriptで`export_presets.cfg`を編集
- EditorExportPresetクラスを使用

### 2. プロジェクト管理の高度化

#### `create_project`
**機能**: 新しいGodotプロジェクトを作成

**パラメータ**:
- `projectPath`: 作成先パス
- `projectName`: プロジェクト名
- `renderer`: レンダラー（"forward_plus", "mobile", "gl_compatibility"）
- `versionControl`: バージョン管理システム（"Git", "None"）

**実装方法**:
```bash
mkdir project_dir
cd project_dir
echo 'config_version=5' > project.godot
echo '[application]' >> project.godot
echo 'config/name="ProjectName"' >> project.godot
```

**拡張案**:
- テンプレートからの作成（2D, 3D, VR）
- 初期ディレクトリ構造の自動生成（scenes/, scripts/, assets/）

#### `validate_project`
**機能**: プロジェクトの整合性を検証

**チェック項目**:
- `project.godot`の存在と妥当性
- 参照切れリソースの検出
- 未使用リソースの検出
- シーンの依存関係チェック

**実装方法**:
- GDScriptでResourceLoaderを使用
- `load()`の失敗を検出
- プロジェクトファイルツリーをスキャン

### 3. デバッグ・プロファイリング

#### `run_with_profiling`
**機能**: プロファイリングを有効にしてプロジェクトを実行

**パラメータ**:
- `projectPath`: プロジェクトパス
- `scene`: 実行するシーン（オプション）
- `profilingType`: プロファイリングタイプ（"script", "gpu", "memory"）

**実装方法**:
```bash
godot --profiling --path /path/to/project
```

**出力**:
- パフォーマンスメトリクス
- フレームレート統計
- メモリ使用量
- ボトルネックの特定

#### `run_with_breakpoint`
**機能**: ブレークポイントを設定してデバッグ実行

**パラメータ**:
- `projectPath`: プロジェクトパス
- `breakpoints`: ブレークポイント配列 `[{file, line}]`
- `debugServer`: リモートデバッグサーバー設定

**実装方法**:
```bash
godot -d --path /path/to/project --debug-server tcp://127.0.0.1:6007
```

#### `get_performance_metrics`
**機能**: 実行中プロジェクトのパフォーマンスメトリクスを取得

**戻り値**:
- FPS（フレームレート）
- メモリ使用量
- ノード数
- 描画コール数

## 🎯 優先度: 中

### 4. スクリプト管理

#### `run_script`
**機能**: 独立したGDScriptを実行

**パラメータ**:
- `projectPath`: プロジェクトパス
- `scriptPath`: スクリプトパス
- `arguments`: スクリプト引数（オプション）

**実装方法**:
```bash
godot --headless --path /path/to/project --script script.gd
```

**ユースケース**:
- バッチ処理スクリプト
- データ変換スクリプト
- メンテナンスタスク

#### `create_script`
**機能**: 新しいGDScriptファイルを作成

**パラメータ**:
- `projectPath`: プロジェクトパス
- `scriptPath`: スクリプトの保存先
- `template`: テンプレート（"Node", "Resource", "EditorScript"）
- `className`: クラス名（オプション）

**テンプレート例**:
```gdscript
extends Node

# Called when the node enters the scene tree
func _ready():
    pass

# Called every frame
func _process(delta):
    pass
```

#### `validate_script`
**機能**: GDScriptの構文をチェック

**実装方法**:
- Godotのコンパイラを使用してエラーチェック
- 構文エラー、型エラー、未定義参照を検出

### 5. アセット・リソース管理

#### `import_asset`
**機能**: 外部アセットをプロジェクトにインポート

**パラメータ**:
- `projectPath`: プロジェクトパス
- `assetPath`: インポート元ファイルパス
- `targetPath`: プロジェクト内の配置先
- `importSettings`: インポート設定（圧縮、フィルタなど）

**対応形式**:
- 画像: PNG, JPG, SVG, WebP
- 3Dモデル: glTF, FBX, OBJ
- オーディオ: WAV, OGG, MP3

**実装方法**:
- ファイルをコピー
- `.import`ファイルを生成
- Godotに再インポートさせる

#### `list_resources`
**機能**: プロジェクト内のリソースを検索

**パラメータ**:
- `projectPath`: プロジェクトパス
- `resourceType`: リソースタイプ（"Scene", "Script", "Texture", "AudioStream"）
- `pattern`: 検索パターン（正規表現）

**戻り値**:
```json
[
  {
    "path": "res://scenes/player.tscn",
    "type": "PackedScene",
    "size": 4096
  }
]
```

#### `convert_resource`
**機能**: リソース形式を変換

**パラメータ**:
- `projectPath`: プロジェクトパス
- `resourcePath`: 変換元リソース
- `outputFormat`: 出力形式（"text" or "binary"）

**実装方法**:
```gdscript
# .res ⇄ .tres 変換
var resource = load("res://resource.res")
ResourceSaver.save(resource, "res://resource.tres")
```

### 6. テスト自動化（GUTサポート）

#### `run_tests`
**機能**: GUT（Godot Unit Test）テストを実行

**パラメータ**:
- `projectPath`: プロジェクトパス
- `testDirectory`: テストディレクトリ（デフォルト: "res://test"）
- `testPattern`: テストファイルパターン
- `xmlOutput`: JUnit XML出力パス（CI用）

**実装方法**:
```bash
godot -s addons/gut/gut_cmdln.gd -gdir=res://test -gxml_output=results.xml
```

**前提条件**:
- GUTアドオンがプロジェクトにインストールされている

#### `create_test`
**機能**: 新しいテストスクリプトを生成

**パラメータ**:
- `projectPath`: プロジェクトパス
- `testPath`: テストファイル保存先
- `targetScript`: テスト対象スクリプト

**テンプレート**:
```gdscript
extends GutTest

func test_example():
    assert_true(true, "Example test")
```

## 🔧 優先度: 低（将来の拡張）

### 7. プラグイン管理

#### `install_plugin`
**機能**: アセットライブラリからプラグインをインストール

**パラメータ**:
- `projectPath`: プロジェクトパス
- `pluginId`: アセットライブラリID
- `version`: バージョン（オプション）

#### `list_plugins`
**機能**: インストール済みプラグイン一覧を取得

#### `enable_plugin` / `disable_plugin`
**機能**: プラグインの有効化/無効化

### 8. ドキュメント生成

#### `generate_docs`
**機能**: GDScriptのdocstringからドキュメントを生成

**パラメータ**:
- `projectPath`: プロジェクトパス
- `outputFormat`: 出力形式（"html", "markdown", "xml"）
- `outputPath`: ドキュメント出力先

**実装方法**:
- GDScriptファイルをパース
- `##`コメント（docstring）を抽出
- MarkdownまたはHTMLに整形

### 9. バージョン管理統合

#### `init_git`
**機能**: プロジェクトでGitを初期化し、`.gitignore`を設定

**自動設定内容**:
```
.import/
*.translation
export_presets.cfg
.mono/
```

#### `check_version_control`
**機能**: プロジェクトのバージョン管理状態をチェック

### 10. リモート・ネットワーク機能

#### `connect_remote_debug`
**機能**: リモートデバッグセッションを開始

**パラメータ**:
- `host`: デバッグサーバーホスト
- `port`: デバッグサーバーポート

#### `deploy_to_device`
**機能**: モバイルデバイスにデプロイ

**対応**:
- Android端末
- iOS端末（要Xcode設定）

## 実装の優先順位付け

### Phase 1: ビルド・エクスポート（即時価値）
1. `export_project` ⭐⭐⭐
2. `list_export_presets` ⭐⭐⭐
3. `create_export_preset` ⭐⭐

### Phase 2: プロジェクト管理
4. `create_project` ⭐⭐⭐
5. `validate_project` ⭐⭐
6. `run_script` ⭐⭐

### Phase 3: デバッグ・品質保証
7. `run_with_profiling` ⭐⭐
8. `run_tests` (GUTサポート) ⭐⭐
9. `validate_script` ⭐

### Phase 4: アセット管理
10. `import_asset` ⭐⭐
11. `list_resources` ⭐
12. `convert_resource` ⭐

### Phase 5: 高度な機能
13. プラグイン管理 ⭐
14. ドキュメント生成 ⭐
15. リモートデバッグ ⭐

## 技術的課題と解決策

### 課題1: エクスポートプリセットの管理
**問題**: `export_presets.cfg`ファイルの複雑なフォーマット

**解決策**:
- ConfigFileクラスを使用してGDScriptで解析
- または正規表現で直接パース

### 課題2: リアルタイムプロファイリングデータの取得
**問題**: 実行中のGodotプロセスからメトリクスを抽出

**解決策**:
- `--profiling`フラグ + ログ出力のパース
- 専用のデバッグスクリプトを注入してJSON出力

### 課題3: GUTの依存関係
**問題**: テスト機能はGUTアドオンの存在に依存

**解決策**:
- プロジェクトにGUTが存在するかチェック
- なければエラーメッセージで案内
- または自動インストールを提案

### 課題4: プラットフォーム依存の機能
**問題**: モバイルデプロイなどプラットフォーム固有の設定

**解決策**:
- 段階的実装（まずデスクトップから）
- プラットフォームチェックでサポート範囲を明示

## コミュニティツールとの統合可能性

### GDRETools
- PCK/APK/EXEファイルからのリソース抽出
- リバースエンジニアリング機能
- 統合することでより強力なリソース管理が可能

### Godot Project Builder
- ビジュアルな自動化パイプライン構築ツール
- MCPツールを組み合わせたワークフロー定義が可能

## まとめ

Godotのコマンドライン機能は非常に充実しており、現在のGodot MCPはその一部のみを活用しています。上記の拡張機能を実装することで、AIアシスタントは以下が可能になります：

1. **完全なCI/CDパイプライン構築** - エクスポート、テスト、デプロイの自動化
2. **プロジェクトのライフサイクル管理** - 作成から検証、リリースまで
3. **高度なデバッグ・最適化** - パフォーマンス問題の自動検出
4. **リソース管理の自動化** - インポート、変換、整理
5. **品質保証の統合** - 自動テスト、構文チェック

これらの機能により、**AI主導のゲーム開発ワークフロー**が現実のものとなります。
