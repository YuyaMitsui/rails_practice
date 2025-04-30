# ビューの実装

## レイアウトファイル

`app/views/layouts/application.html.erb`の基本構造：

```erb
<!DOCTYPE html>
<html>
  <head>
    <title>BookshelfApp</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>

  <body>
    <div class="container mx-auto px-4">
      <% if notice %>
        <div class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded relative" role="alert">
          <span class="block sm:inline"><%= notice %></span>
        </div>
      <% end %>

      <% if alert %>
        <div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative" role="alert">
          <span class="block sm:inline"><%= alert %></span>
        </div>
      <% end %>

      <%= yield %>
    </div>
  </body>
</html>
```

## パーシャルファイル

`app/views/books/_form.html.erb`の実装例：

```erb
<%= form_with(model: book) do |form| %>
  <% if book.errors.any? %>
    <div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative mb-4">
      <h2 class="font-bold"><%= pluralize(book.errors.count, "error") %> prohibited this book from being saved:</h2>
      <ul>
        <% book.errors.each do |error| %>
          <li><%= error.full_message %></li>
        <% end %>
      </ul>
    </div>
  <% end %>

  <div class="mb-4">
    <%= form.label :title, class: "block text-gray-700 text-sm font-bold mb-2" %>
    <%= form.text_field :title, class: "shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" %>
  </div>

  <div class="mb-4">
    <%= form.label :author, class: "block text-gray-700 text-sm font-bold mb-2" %>
    <%= form.text_field :author, class: "shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" %>
  </div>

  <div class="mb-4">
    <%= form.label :description, class: "block text-gray-700 text-sm font-bold mb-2" %>
    <%= form.text_area :description, class: "shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" %>
  </div>

  <div class="flex items-center justify-between">
    <%= form.submit class: "bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" %>
  </div>
<% end %>
```

## ビューファイル

### インデックスビュー

`app/views/books/index.html.erb`の実装例：

```erb
<div class="flex justify-between items-center mb-6">
  <h1 class="text-3xl font-bold">Books</h1>
  <%= link_to "New Book", new_book_path, class: "bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded" %>
</div>

<% if @books.any? %>
  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    <% @books.each do |book| %>
      <div class="bg-white shadow-md rounded-lg p-6">
        <h2 class="text-xl font-semibold mb-2"><%= book.title %></h2>
        <p class="text-gray-600 mb-2">著者: <%= book.author %></p>
        <p class="text-gray-700 mb-4"><%= truncate(book.description, length: 100) %></p>
        <div class="flex space-x-2">
          <%= link_to "Show", book, class: "bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" %>
          <%= link_to "Edit", edit_book_path(book), class: "bg-yellow-500 hover:bg-yellow-700 text-white font-bold py-2 px-4 rounded" %>
          <%= button_to "Destroy", book, method: :delete, class: "bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded", form: { data: { turbo_confirm: "Are you sure?" } } %>
        </div>
      </div>
    <% end %>
  </div>
<% else %>
  <div class="text-center py-8">
    <p class="text-gray-500">No books found.</p>
  </div>
<% end %>
```

### 詳細ビュー

`app/views/books/show.html.erb`の実装例：

```erb
<div class="bg-white shadow-md rounded-lg p-6">
  <div class="mb-4">
    <h1 class="text-2xl font-bold mb-2"><%= @book.title %></h1>
    <p class="text-gray-600">著者: <%= @book.author %></p>
  </div>

  <div class="mb-6">
    <h2 class="text-lg font-semibold mb-2">Description</h2>
    <p class="text-gray-700"><%= @book.description %></p>
  </div>

  <div class="flex space-x-2">
    <%= link_to "Edit", edit_book_path(@book), class: "bg-yellow-500 hover:bg-yellow-700 text-white font-bold py-2 px-4 rounded" %>
    <%= link_to "Back", books_path, class: "bg-gray-500 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded" %>
  </div>
</div>
```

## ヘルパーメソッド

`app/helpers/books_helper.rb`の実装例：

```ruby
module BooksHelper
  def format_date(date)
    date.strftime("%Y年%m月%d日")
  end

  def book_status(book)
    if book.published?
      content_tag(:span, "公開中", class: "bg-green-100 text-green-800 text-xs font-medium px-2.5 py-0.5 rounded")
    else
      content_tag(:span, "下書き", class: "bg-gray-100 text-gray-800 text-xs font-medium px-2.5 py-0.5 rounded")
    end
  end
end
``` 