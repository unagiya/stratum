---
paths:
  - "internal/repository/**"
---

# Repository 実装ルール

- repository はインターフェースで定義し、構造体で実装する
- 構造体は `*sqlx.DB` をフィールドに持つ。トランザクション対応が必要なメソッドは `sqlx.ExtContext`（`*sqlx.DB` と `*sqlx.Tx` 両方を受け入れる）を引数に取る
- SQL はこの層にのみ記述する。sqlx の `NamedExec`, `Get`, `Select` を使い、構造体タグ `db:"column_name"` でマッピングする
- PostgreSQL 配列型は `pq.StringArray` で扱う（タグ検索用）
- pgvector カラムは `pgvector.Vector` 型を使う
- コンテキストは必ず `context.Context` を第一引数に取る
- クエリのプレースホルダは PostgreSQL 形式（`$1`, `$2`, ...）を使用する
