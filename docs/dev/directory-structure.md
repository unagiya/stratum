# stratum — ディレクトリ構成

## 全体像

```
stratum/
├── go.mod / go.sum          # Go モジュール定義（ルート配置）
├── cmd/
│   └── server/              # アプリケーションエントリーポイント
├── internal/
│   ├── handler/             # HTTP ハンドラ（oapi-codegen 生成 I/F の実装）
│   ├── service/             # ビジネスロジック
│   ├── repository/          # DB アクセス層
│   └── model/               # ドメインモデル
├── api/
│   ├── openapi.yaml         # OpenAPI スキーマ（フロント・バック共有）
│   └── gen/
│       ├── go/              # oapi-codegen 生成コード
│       └── ts/              # openapi-typescript 生成型
├── db/
│   └── migrations/          # golang-migrate 用マイグレーションファイル
├── frontend/                # React + TypeScript + Vite
│   ├── src/
│   ├── package.json
│   └── Dockerfile
├── docker-compose.yml
├── Dockerfile               # バックエンド用
├── Makefile
├── .air.toml                # Go ホットリロード設定
└── docs/
    ├── product/             # プロダクト仕様
    └── dev/                 # 開発ドキュメント
```

## リポジトリ方針

本リポジトリはモノレポ構成を採用し、Go バックエンドと React フロントエンドを同一リポジトリで管理する。Go モジュール（`go.mod`）はリポジトリルートに配置し、フロントエンドは `frontend/` ディレクトリに格納する。

## 各ディレクトリの説明

### `go.mod` / `go.sum`（ルート）

Go モジュールの定義ファイル。リポジトリルートに配置することで、`cmd/`・`internal/` 配下のすべての Go パッケージが単一モジュールに属する。マルチモジュール構成にしない理由は以下の通り:

- プロジェクト規模に対してマルチモジュールは過剰
- 依存管理が単純になり、`go mod tidy` 一発で完結する
- `internal/` パッケージの相互参照が自然に行える

### `cmd/server/`

アプリケーションのエントリーポイント（`main.go`）を配置する。ルーター（chi）の初期化、ミドルウェアの登録、サーバー起動処理などの組み立て（ワイヤリング）を担当する。ビジネスロジックは含めず、`internal/` パッケージへ委譲する。

**配置するファイル:**
- `main.go` — サーバー起動・DI の組み立て

### `internal/`

Go の `internal` パッケージ規約により、このディレクトリ配下のコードはモジュール外部からインポートできない。アプリケーション固有のロジックをすべてここに集約する。

#### `internal/handler/`

HTTP リクエストの受け付けとレスポンスの返却を担当する層。oapi-codegen が `api/openapi.yaml` から生成するインターフェースを実装する形でハンドラを定義する。

**配置するファイル:**
- 各エンドポイントの実装ファイル

**責務:**
- リクエストのバリデーション・パース
- service 層の呼び出し
- レスポンスの組み立て・返却

#### `internal/service/`

ビジネスロジックを実装する層。handler から呼び出され、repository を通じてデータにアクセスする。HTTP やデータベースの詳細に依存しない純粋なロジックを記述する。

**配置するファイル:**
- ユースケース単位のサービスファイル（例: `post_service.go`）

**責務:**
- ビジネスルールの実装
- 複数リポジトリの組み合わせ・トランザクション管理
- 外部 API（OpenAI Embeddings 等）との連携

#### `internal/repository/`

データベースアクセスを担当する層。sqlx を使用して SQL を直接記述し、構造体へのマッピングを行う。

**配置するファイル:**
- テーブル/集約単位のリポジトリファイル（例: `post_repository.go`）

**責務:**
- SQL クエリの実行
- データの永続化・取得
- pgvector を使ったベクトル検索クエリ

#### `internal/model/`

ドメインモデル（エンティティ・値オブジェクト）を定義する。データベーステーブルとの 1:1 対応に限定せず、ビジネスドメインの概念を表現する。

**配置するファイル:**
- ドメインモデルの構造体定義（例: `post.go`, `tag.go`）

### `api/`

API 定義と生成コードを一元管理するディレクトリ。OpenAPI スキーマをフロント・バック間の契約（Contract）として配置し、そこから生成されるコードも同ディレクトリ配下にまとめる。

将来リポジトリを分離する際に、`api/`（定義＋生成物）と `db/`（マイグレーション）をそのまま独立した契約定義リポジトリとして切り出せる構成を意図している。

**配置するファイル:**
- `openapi.yaml` — API 定義の単一ソース
- `gen/go/` — oapi-codegen が生成する Go コード（サーバーインターフェース・リクエスト/レスポンス型）
- `gen/ts/` — openapi-typescript が生成する TypeScript 型定義

**共有ディレクトリとした理由:**
- `internal/` に置くとフロントエンドから参照しづらい
- `frontend/` に置くとバックエンドから参照しづらい
- ルート直下の `api/` に配置することで、どちらからも自然にアクセスできる
- 生成コードを `api/gen/` にまとめることで、定義と生成物の対応関係が明確になる

### `db/migrations/`

golang-migrate 用のマイグレーションファイルを格納する。ファイル名は `NNNNNN_description.up.sql` / `NNNNNN_description.down.sql` の形式に従う。

**配置するファイル:**
- `*.up.sql` — スキーマ変更の適用
- `*.down.sql` — スキーマ変更のロールバック

### `frontend/`

React + TypeScript + Vite によるフロントエンドアプリケーション。独自の `package.json` と `Dockerfile` を持ち、フロントエンド固有の依存・ビルド設定を管理する。

**配置するファイル:**
- `package.json` — フロントエンド依存管理
- `Dockerfile` — フロントエンドコンテナビルド
- `src/` — ソースコード（コンポーネント・ページ・フック等）

**主要な依存:**
- Tailwind CSS（スタイル）
- openapi-typescript / openapi-fetch（API 型生成・クライアント）
- TanStack Query（サーバー状態管理）
- react-markdown（Markdown 表示）
- react-grid-layout（ダッシュボード）

### ルート直下の設定ファイル

| ファイル | 役割 |
|----------|------|
| `docker-compose.yml` | 開発環境の定義（Go サーバー、フロントエンド、PostgreSQL 等） |
| `Dockerfile` | バックエンド用コンテナビルド |
| `Makefile` | 開発タスクの統一インターフェース（ビルド、テスト、コード生成等） |
| `.air.toml` | Air（Go ホットリロードツール）の設定 |

### `docs/`

プロジェクトのドキュメントを格納する。

| サブディレクトリ | 内容 |
|------------------|------|
| `docs/product/` | プロダクト仕様（概要・機能定義・技術スタック等） |
| `docs/dev/` | 開発ドキュメント（ディレクトリ構成・コーディング規約等） |
