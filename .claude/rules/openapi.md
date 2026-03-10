---
paths:
  - "api/openapi.yaml"
---

# OpenAPI スキーマ編集ルール

- スキーマを編集したら、Go と TypeScript 両方のコード生成を実行する（`make generate` または個別の生成コマンド）
- エンドポイントの operationId はキャメルケース（例: `createPost`, `getPostById`）で命名する
- リクエスト/レスポンスのスキーマは `components/schemas` に定義して `$ref` で参照する。インラインスキーマは避ける
- 日時フィールドは `format: date-time` を指定する
- UUID フィールドは `format: uuid` を指定する
- 一覧取得エンドポイントはページネーションパラメータ（`limit`, `offset`）を含める
- エラーレスポンスは共通スキーマ（`ErrorResponse`）を定義し再利用する
