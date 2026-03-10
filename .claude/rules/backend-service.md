---
paths:
  - "internal/service/**"
---

# Service 実装ルール

- service はインターフェースで定義し、構造体で実装する。テスト時にモック差し替えが可能な設計にする
- repository インターフェースをフィールドに持ち、コンストラクタで注入する
- 複数テーブルを跨ぐ変更はトランザクション内で実行する。`*sqlx.Tx` を repository メソッドに渡すか、トランザクション管理用ヘルパーを使う
- HTTP の詳細（ステータスコード、リクエスト/レスポンス型）に依存しない。handler 層の生成型を import しない
- 外部 API 呼び出し（OpenAI Embeddings 等）もインターフェース経由で抽象化する
- ドメインモデル（`internal/model`）の型を入出力に使う
