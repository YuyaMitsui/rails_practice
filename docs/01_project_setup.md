# プロジェクトの基本設定

## プロジェクトの作成

```bash
# Railsプロジェクトの作成（PostgreSQLを使用）
rails new bookshelf_app --database=postgresql
```

## 必要なGemの追加

`Gemfile`に以下のgemを追加：

```ruby
# データベース
gem 'pg'

# フロントエンド
gem 'tailwindcss-rails'
```

## 環境変数の設定

`.env`ファイルを作成し、以下の環境変数を設定：

```env
RAILS_MASTER_KEY=your_master_key
DATABASE_URL=postgres://postgres:password@db:5432/myapp_development
```

## ディレクトリ構造

```
bookshelf_app/
├── app/
│   ├── controllers/
│   ├── models/
│   └── views/
├── config/
├── db/
└── docs/
```

## 初期設定コマンド

```bash
# 依存関係のインストール
bundle install

# データベースの作成
rails db:create

# アセットのプリコンパイル
rails assets:precompile
``` 