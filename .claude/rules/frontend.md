---
paths:
  - "frontend/src/**"
---

# フロントエンド実装ルール

## API 連携

- API クライアントは openapi-fetch の `createClient` で生成する。手動で fetch や axios を使わない
- API の型は `api/gen/ts/` から import する。手動で API 型を定義しない
- データ取得は `useQuery`、更新は `useMutation`（TanStack Query）を使う
- カスタムフックとして `useXxx` の形式で切り出す（例: `usePosts`, `useCreatePost`）

## コンポーネント

- `export function ComponentName()` 形式で定義する（`React.FC` は使わない）
- props の型は `type Props = { ... }` で定義し、コンポーネントと同一ファイルに置く
- スタイルは Tailwind CSS のユーティリティクラスのみ。CSS ファイルを個別に作らない

## Markdown・ダッシュボード

- 投稿の表示には react-markdown を使用する
- ダッシュボードのレイアウトには react-grid-layout を使用する
