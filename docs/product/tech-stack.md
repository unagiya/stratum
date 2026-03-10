# stratum — 技術スタック

## バックエンド

| カテゴリ | 選定 | 備考 |
|----------|------|------|
| 言語 | Go | |
| ルーター | chi | 軽量で net/http 互換。ミドルウェア設計が優秀 |
| DB アクセス | sqlx | SQL を直接書きつつ構造体マッピングが可能 |
| API 設計 | OpenAPI (Swagger) | スキーマファーストで API を定義 |
| コード生成 | oapi-codegen | OpenAPI 定義から Go のハンドラインターフェース・型を生成 |
| マイグレーション | golang-migrate | スキーマ変更の管理 |

## フロントエンド

| カテゴリ | 選定 | 備考 |
|----------|------|------|
| 言語 | TypeScript | |
| UI ライブラリ | React | |
| ビルドツール | Vite | 高速ビルド |
| スタイル | Tailwind CSS | ユーティリティファースト |
| API 型生成 | openapi-typescript | OpenAPI 定義から TypeScript の型を生成 |
| API クライアント | openapi-fetch | 生成した型と連携する型安全な fetch ラッパー |
| サーバー状態管理 | TanStack Query | API データのフェッチ・キャッシュ |
| Markdown 表示 | react-markdown | 投稿の表示用 |
| ダッシュボード | react-grid-layout | タイル型ウィジェットの配置 |

## データストア

| カテゴリ | 選定 | 備考 |
|----------|------|------|
| メイン DB | PostgreSQL | タグ検索に配列型が使える |
| ベクトル検索 | pgvector | PostgreSQL 拡張。意味検索用 |
| Embedding 生成 | OpenAI Embeddings API | text-embedding-3-small を使用 |

## インフラ

| カテゴリ | 選定 | 備考 |
|----------|------|------|
| コンテナ | Docker / Docker Compose | 開発環境の統一 |
| デプロイ先 | 未定 | Cloud Run / Fly.io 等を検討 |
| オブジェクトストレージ | 未定 | 画像添付が必要になった場合に検討 |

## 認証

| カテゴリ | 選定 | 備考 |
|----------|------|------|
| 認証方式 | 未定 | 初期は JWT、SSO 対応時に Auth0 等を検討 |
