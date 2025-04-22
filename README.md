# Ruby on Rails 学習用プロジェクト

このプロジェクトは、Dockerを使用してRuby on Railsの開発環境を構築するためのものです。

## 環境要件

- Docker
- Docker Compose

## プロジェクト構成

```
.
├── app/                    # アプリケーションの主要なコード
│   ├── assets/            # スタイルシート、JavaScript、画像などのアセット
│   ├── controllers/       # コントローラー（リクエストの処理を担当）
│   ├── models/           # モデル（データベースとのやり取りを担当）
│   ├── views/            # ビュー（HTMLテンプレート）
│   └── helpers/          # ビューヘルパー（ビューで使用するメソッド）
├── config/                # 設定ファイル
│   ├── database.yml      # データベース接続設定
│   ├── routes.rb         # ルーティング設定
│   └── master.key        # 暗号化キー（gitignoreに含めるべき）
├── db/                    # データベース関連
│   └── migrate/          # マイグレーションファイル
├── lib/                   # ライブラリコード
├── log/                   # ログファイル
├── public/                # 静的ファイル
├── test/                  # テストファイル
├── tmp/                   # 一時ファイル
├── vendor/                # サードパーティのコード
├── Gemfile                # Rubyの依存関係管理
├── Gemfile.lock           # 依存関係のバージョン固定
├── Dockerfile             # Dockerイメージの定義
├── docker-compose.yml     # Docker Compose設定
└── entrypoint.sh          # コンテナ起動時のスクリプト
```

## セットアップ手順

1. プロジェクトのクローン
```bash
git clone [リポジトリURL]
cd [プロジェクトディレクトリ]
```

2. コンテナのビルドと起動
```bash
docker-compose build
docker-compose up
```

3. ブラウザで確認
- http://localhost:3000 にアクセス

## 開発環境の使い方

- アプリケーションのコードは `app` ディレクトリにあります
- データベースの設定は `config/database.yml` にあります
- ルーティングの設定は `config/routes.rb` にあります

## トラブルシューティング

### 1. データベース接続エラー
- `config/database.yml`の設定を確認
- PostgreSQLのコンテナが正常に起動しているか確認
- 環境変数`DATABASE_URL`が正しく設定されているか確認

### 2. マスターキー関連のエラー
- `config/master.key`が存在するか確認
- 環境変数`RAILS_MASTER_KEY`が設定されているか確認
- 必要に応じて`rails credentials:edit`で新しいキーを生成

### 3. コンテナ起動時のエラー
- `docker-compose down`でコンテナを停止
- `docker-compose build --no-cache`でイメージを再ビルド
- `docker-compose up`で再起動

## 注意事項

- 初回起動時はデータベースのマイグレーションが必要です
- 開発中は `docker-compose up` でサーバーを起動したままにしてください
- コードの変更は自動的に反映されます
- `config/master.key`は機密情報なので、gitignoreに追加することを推奨します

## よくあるエラーと解決方法

### 1. "Missing `secret_key_base` for 'production' environment"
- 環境変数`RAILS_ENV=development`を設定
- `config/master.key`の存在を確認
- `docker-compose.yml`に`RAILS_MASTER_KEY`を設定

### 2. "Could not find gem 'rails'"
- `bundle install`を実行
- `Gemfile`の内容を確認
- コンテナを再ビルド

### 3. データベース接続エラー
- PostgreSQLのコンテナが起動しているか確認
- `config/database.yml`の設定を確認
- 環境変数`DATABASE_URL`を確認

## 開発のヒント

1. コントローラーの作成
```bash
docker-compose run web rails generate controller [コントローラー名] [アクション名]
```

2. モデルの作成
```bash
docker-compose run web rails generate model [モデル名] [カラム名:型]
```

3. マイグレーションの実行
```bash
docker-compose run web rails db:migrate
```

4. コンソールの起動
```bash
docker-compose run web rails console
```
