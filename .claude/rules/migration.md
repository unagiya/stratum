---
paths:
  - "db/migrations/**"
---

# マイグレーション作成ルール

- ファイル名は `NNNNNN_description.up.sql` / `NNNNNN_description.down.sql` のペアで作成する。番号は既存の最大値 + 1
- up で行った変更を down で正確にロールバックできること
- テーブル作成時は `IF NOT EXISTS` を付けない（マイグレーション順序が保証するため）
- `created_at` / `updated_at` カラムは `TIMESTAMPTZ NOT NULL DEFAULT NOW()` で定義する
- UUID 主キーは `UUID PRIMARY KEY DEFAULT gen_random_uuid()` を使用する
- タグカラムは `TEXT[]` 型（PostgreSQL 配列）で定義し、GIN インデックスを作成する
- ベクトルカラムは `vector(1536)` 型（text-embedding-3-small の次元数）で定義する
- SQL キーワードは大文字で記述する（`CREATE TABLE`, `NOT NULL` 等）
