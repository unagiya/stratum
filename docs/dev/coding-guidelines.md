# stratum — コーディングガイドライン

## バックエンド（Go）

### レイヤー構成

依存方向は handler → service → repository の一方向とし、逆転させない。

- **handler**: リクエストの受け付け・バリデーション・レスポンス返却のみ。ビジネスロジックは service 層へ委譲する
- **service**: ビジネスルールの実装。複数リポジトリの組み合わせやトランザクション管理を担当
- **repository**: SQL クエリの実行・データ永続化。SQL は repository 層のみで記述する

### API 変更フロー

API エンドポイントの追加・変更は以下の手順で行う:

1. `api/openapi.yaml` を更新
2. oapi-codegen でコード再生成
3. 生成されたインターフェースに対する handler 実装を追加・修正

### テスト

- テストファイルは `*_test.go` で対象ファイルと同一パッケージに配置する

### エラーハンドリング

- エラーは `fmt.Errorf("コンテキスト情報: %w", err)` でラップして返す
- 各レイヤーで適切なコンテキスト情報を付与する

## フロントエンド（TypeScript / React）

### API 連携

- API の型は openapi-typescript で生成したものを使用する。手動で API 型を定義しない
- API クライアントは openapi-fetch を使用する
- サーバー状態の取得・更新は TanStack Query を使用する

### コンポーネント

- 関数コンポーネントで記述する

### スタイリング

- Tailwind CSS のユーティリティクラスを使用する

## データベース

### マイグレーション

- スキーマ変更は必ず golang-migrate のマイグレーションファイルで管理する
- up（適用）と down（ロールバック）のペアで作成する
- ファイル名は `NNNNNN_description.up.sql` / `NNNNNN_description.down.sql` 形式に従う
