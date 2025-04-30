# テストの実装

## テスト環境の設定

`config/environments/test.rb`の設定：

```ruby
Rails.application.configure do
  config.cache_classes = true
  config.eager_load = false
  config.public_file_server.enabled = true
  config.public_file_server.headers = {
    'Cache-Control' => "public, max-age=#{1.hour.to_i}"
  }
  config.consider_all_requests_local = true
  config.action_controller.perform_caching = false
  config.cache_store = :null_store
  config.action_dispatch.show_exceptions = false
  config.action_controller.allow_forgery_protection = false
  config.active_storage.service = :test
  config.action_mailer.perform_caching = false
  config.action_mailer.delivery_method = :test
  config.active_support.deprecation = :stderr
end
```

## モデルのテスト

`test/models/book_test.rb`の実装例：

```ruby
require "test_helper"

class BookTest < ActiveSupport::TestCase
  def setup
    @book = Book.new(
      title: "テストタイトル",
      author: "テスト著者",
      description: "テスト説明"
    )
  end

  test "should be valid" do
    assert @book.valid?
  end

  test "title should be present" do
    @book.title = "     "
    assert_not @book.valid?
  end

  test "author should be present" do
    @book.author = "     "
    assert_not @book.valid?
  end

  test "title should not be too long" do
    @book.title = "a" * 256
    assert_not @book.valid?
  end

  test "author should not be too long" do
    @book.author = "a" * 256
    assert_not @book.valid?
  end
end
```

## コントローラーのテスト

`test/controllers/books_controller_test.rb`の実装例：

```ruby
require "test_helper"

class BooksControllerTest < ActionDispatch::IntegrationTest
  setup do
    @book = books(:one)
  end

  test "should get index" do
    get books_url
    assert_response :success
  end

  test "should get new" do
    get new_book_url
    assert_response :success
  end

  test "should create book" do
    assert_difference("Book.count") do
      post books_url, params: {
        book: {
          title: "新しい本",
          author: "新しい著者",
          description: "新しい説明"
        }
      }
    end

    assert_redirected_to book_url(Book.last)
  end

  test "should show book" do
    get book_url(@book)
    assert_response :success
  end

  test "should get edit" do
    get edit_book_url(@book)
    assert_response :success
  end

  test "should update book" do
    patch book_url(@book), params: {
      book: {
        title: "更新されたタイトル"
      }
    }
    assert_redirected_to book_url(@book)
  end

  test "should destroy book" do
    assert_difference("Book.count", -1) do
      delete book_url(@book)
    end

    assert_redirected_to books_url
  end
end
```

## システムテスト

`test/system/books_test.rb`の実装例：

```ruby
require "application_system_test_case"

class BooksTest < ApplicationSystemTestCase
  setup do
    @book = books(:one)
  end

  test "visiting the index" do
    visit books_url
    assert_selector "h1", text: "Books"
  end

  test "creating a Book" do
    visit books_url
    click_on "New Book"

    fill_in "Title", with: "システムテスト本"
    fill_in "Author", with: "システムテスト著者"
    fill_in "Description", with: "システムテスト説明"
    click_on "Create Book"

    assert_text "Book was successfully created"
    click_on "Back"
  end

  test "updating a Book" do
    visit books_url
    click_on "Edit", match: :first

    fill_in "Title", with: "更新されたタイトル"
    click_on "Update Book"

    assert_text "Book was successfully updated"
    click_on "Back"
  end

  test "destroying a Book" do
    visit books_url
    page.accept_confirm do
      click_on "Destroy", match: :first
    end

    assert_text "Book was successfully destroyed"
  end
end
```

## フィクスチャ

`test/fixtures/books.yml`の実装例：

```yaml
one:
  title: "テスト本1"
  author: "テスト著者1"
  description: "テスト説明1"

two:
  title: "テスト本2"
  author: "テスト著者2"
  description: "テスト説明2"
```

## テストの実行

```bash
# すべてのテストを実行
rails test

# 特定のテストファイルを実行
rails test test/models/book_test.rb

# 特定のテストメソッドを実行
rails test test/models/book_test.rb:10

# システムテストのみ実行
rails test:system
```

## テストカバレッジ

`Gemfile`に追加：

```ruby
group :test do
  gem 'simplecov', require: false
end
```

`test/test_helper.rb`の設定：

```ruby
require 'simplecov'
SimpleCov.start 'rails'
```

カバレッジレポートの確認：

```bash
# テスト実行後、coverage/index.htmlをブラウザで開く
open coverage/index.html
``` 