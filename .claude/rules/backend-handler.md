---
paths:
  - "internal/handler/**"
---

# Handler 実装ルール

- oapi-codegen が `api/gen/go/` に生成するサーバーインターフェースを実装する。メソッドシグネチャは生成コードに従う
- handler 構造体は service インターフェースをフィールドに持つ。具体型ではなくインターフェースに依存する
- handler 内で SQL を書かない。repository を直接呼び出さない。必ず service 経由
- リクエストボディのパースには生成された型を使用する
- レスポンスは生成された型ヘルパー（例: `PostJSON200Response`）で返す
- バリデーションエラーは 400、認証エラーは 401、認可エラーは 403、未検出は 404 を返す
- ハンドラのコンストラクタは `NewXxxHandler(svc XxxService) *XxxHandler` の形式
