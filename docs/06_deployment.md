# デプロイメント

## Docker環境の設定

### Dockerfile

```dockerfile
FROM ruby:3.2.2

# 必要なパッケージのインストール
RUN apt-get update -qq && \
    apt-get install -y build-essential libpq-dev nodejs postgresql-client

# アプリケーションディレクトリの作成
WORKDIR /myapp

# Gemfileのコピー
COPY Gemfile* ./

# 依存関係のインストール
RUN bundle install

# アプリケーションのコピー
COPY . .

# ポートの公開
EXPOSE 3000

# サーバーの起動
CMD ["rails", "server", "-b", "0.0.0.0"]
```

### docker-compose.yml

```yaml
version: '3'
services:
  db:
    image: postgres:15
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: password
      POSTGRES_USER: postgres
      POSTGRES_DB: myapp_development

  web:
    build: .
    command: bash -c "rm -f tmp/pids/server.pid && bundle exec rails s -p 3000 -b '0.0.0.0'"
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://postgres:password@db:5432/myapp_development
    depends_on:
      - db

volumes:
  postgres_data:
```

## デプロイメント手順

### 1. 環境変数の設定

`.env`ファイルの作成：

```env
RAILS_MASTER_KEY=your_master_key
DATABASE_URL=postgres://postgres:password@db:5432/myapp_development
```

### 2. ビルドと起動

```bash
# イメージのビルド
docker-compose build

# コンテナの起動
docker-compose up -d

# データベースの作成
docker-compose run web rails db:create

# マイグレーションの実行
docker-compose run web rails db:migrate
```

### 3. ログの確認

```bash
# すべてのログを表示
docker-compose logs

# 特定のサービスのログを表示
docker-compose logs web
```

### 4. コンテナの管理

```bash
# コンテナの停止
docker-compose down

# コンテナの再起動
docker-compose restart

# 特定のサービスの再起動
docker-compose restart web
```

## 本番環境の設定

### 1. 環境変数の設定

`.env.production`ファイルの作成：

```env
RAILS_ENV=production
RAILS_MASTER_KEY=your_master_key
DATABASE_URL=postgres://postgres:password@db:5432/myapp_production
```

### 2. アセットのプリコンパイル

```bash
docker-compose run web rails assets:precompile RAILS_ENV=production
```

### 3. データベースの設定

```bash
# 本番環境のデータベース作成
docker-compose run web rails db:create RAILS_ENV=production

# 本番環境のマイグレーション実行
docker-compose run web rails db:migrate RAILS_ENV=production
```

## バックアップとリストア

### 1. データベースのバックアップ

```bash
# バックアップの作成
docker-compose exec db pg_dump -U postgres myapp_development > backup.sql

# 特定の日付でバックアップ
docker-compose exec db pg_dump -U postgres myapp_development > backup_$(date +%Y%m%d).sql
```

### 2. データベースのリストア

```bash
# バックアップからのリストア
docker-compose exec -T db psql -U postgres myapp_development < backup.sql
```

## トラブルシューティング

### 1. コンテナの状態確認

```bash
# 実行中のコンテナの確認
docker-compose ps

# コンテナの詳細情報
docker-compose ps -a
```

### 2. コンテナのログイン

```bash
# Webコンテナへのログイン
docker-compose exec web bash

# DBコンテナへのログイン
docker-compose exec db bash
```

### 3. データベースの接続確認

```bash
# DBコンテナ内で
psql -U postgres -h localhost

# データベースの一覧表示
\l

# テーブルの一覧表示
\dt
``` 