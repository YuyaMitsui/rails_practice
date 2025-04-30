# データベース設定とマイグレーション

## データベース設定

`config/database.yml`の設定：

```yaml
development:
  adapter: postgresql
  encoding: unicode
  database: myapp_development
  pool: 5
  username: postgres
  password: password
  host: db
```

## モデルの作成

```bash
# Bookモデルの作成（scaffoldを使用）
rails g scaffold Book title:string author:string description:text
```

## マイグレーションの実行

```bash
# データベースの作成
rails db:create

# マイグレーションの実行
rails db:migrate

# データベースのリセットが必要な場合
rails db:drop db:create db:migrate
```

## よくあるエラーと解決方法

### 1. テーブルが既に存在する場合

```bash
# データベースをリセット
rails db:drop db:create db:migrate
```

### 2. マイグレーションエラー

```bash
# マイグレーションの状態を確認
rails db:migrate:status

# 特定のマイグレーションを再実行
rails db:migrate:redo VERSION=20250430153022
```

## データベースのバックアップとリストア

```bash
# バックアップ
pg_dump -U postgres myapp_development > backup.sql

# リストア
psql -U postgres myapp_development < backup.sql
``` 