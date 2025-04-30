# セキュリティ対策

## 認証と認可

### Deviseの設定

`Gemfile`に追加：

```ruby
gem 'devise'
```

インストールと設定：

```bash
# Deviseのインストール
rails generate devise:install

# Userモデルの作成
rails generate devise User

# マイグレーションの実行
rails db:migrate
```

### コントローラーの認証設定

```ruby
class BooksController < ApplicationController
  before_action :authenticate_user!
  before_action :set_book, only: [:show, :edit, :update, :destroy]

  # ... 既存のコード ...
end
```

## パスワードポリシー

`config/initializers/devise.rb`の設定：

```ruby
Devise.setup do |config|
  # パスワードの最小文字数
  config.password_length = 8..128

  # パスワードの複雑さの要件
  config.password_regex = /\A(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d]+\z/

  # パスワードの有効期限（日数）
  config.expire_password_after = 90.days
end
```

## セッション管理

### セッションタイムアウト

`config/initializers/session_store.rb`の設定：

```ruby
Rails.application.config.session_store :cookie_store,
  key: '_bookshelf_app_session',
  expire_after: 30.minutes,
  secure: Rails.env.production?,
  httponly: true
```

## CSRF対策

### トークンの設定

`app/controllers/application_controller.rb`の設定：

```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  before_action :set_csrf_cookie

  private

  def set_csrf_cookie
    cookies['CSRF-TOKEN'] = form_authenticity_token
  end
end
```

## セキュリティヘッダー

### セキュリティヘッダーの設定

`config/initializers/security_headers.rb`の作成：

```ruby
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'
    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end

Rails.application.config.middleware.insert_before 0, Rack::Attack

Rails.application.config.middleware.use Rack::Protection::StrictTransportSecurity
Rails.application.config.middleware.use Rack::Protection::XSSHeader
Rails.application.config.middleware.use Rack::Protection::FrameOptions
Rack::Attack.throttle('req/ip', limit: 300, period: 1.minute) do |req|
  req.ip
end
```

## データベースセキュリティ

### パスワードの暗号化

`app/models/user.rb`の設定：

```ruby
class User < ApplicationRecord
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable

  # パスワードの暗号化
  has_secure_password

  # パスワードのバリデーション
  validates :password, presence: true,
                      length: { minimum: 8 },
                      format: { with: /\A(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d]+\z/ }
end
```

## ログ管理

### ログの設定

`config/environments/production.rb`の設定：

```ruby
config.log_level = :info
config.log_tags = [:request_id]
config.log_formatter = ::Logger::Formatter.new

# ログのローテーション
config.logger = ActiveSupport::Logger.new(config.paths['log'].first, 5, 100.megabytes)
```

## エラーハンドリング

### エラーページの設定

`app/controllers/application_controller.rb`の設定：

```ruby
class ApplicationController < ActionController::Base
  rescue_from ActiveRecord::RecordNotFound, with: :render_404
  rescue_from ActionController::RoutingError, with: :render_404
  rescue_from Exception, with: :render_500

  private

  def render_404
    render file: "#{Rails.root}/public/404.html", status: 404, layout: false
  end

  def render_500
    render file: "#{Rails.root}/public/500.html", status: 500, layout: false
  end
end
```

## セキュリティ監査

### Brakemanの設定

`Gemfile`に追加：

```ruby
group :development, :test do
  gem 'brakeman'
end
```

セキュリティチェックの実行：

```bash
# セキュリティチェックの実行
bundle exec brakeman

# レポートの出力
bundle exec brakeman -o security_report.html
```

## 依存関係のセキュリティチェック

### Bundler Auditの設定

`Gemfile`に追加：

```ruby
group :development, :test do
  gem 'bundler-audit'
end
```

セキュリティチェックの実行：

```bash
# 依存関係のセキュリティチェック
bundle audit check --update

# 特定の脆弱性のチェック
bundle audit check --ignore CVE-2023-1234
``` 