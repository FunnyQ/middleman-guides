---
title: 詳細設定
---

# 詳細設定

## 了解 Middleman 的設定

Middleman 有驚人的可設定性，而且有各種擴充功能可以選擇來讓 Middleman 更加強大。與其將所有的設定給文件化，我們選擇了賦予 Middleman 直接告訴你，自己可以如何去設定的能力。

先啟動預覽伺服器，接著透過瀏覽器存取 `http://localhost:4567/__middleman/config/`，將會顯示出所有可更動的設定、以及所有可用的擴充功能。其中包含了設定的名稱、簡單的說明、預設值，也會告訴你這個專案目前是怎樣的配置。

## 變更設定

幾乎所有的設定都是在 `config.rb` 中透過 `set` 來設定：

```ruby
set :js_dir, 'js'
```

也可以使用比較新的語法：

```ruby
config[:js_dir] = 'js'
```

這些將適用於幾乎所有 Middleman 的全域設定。

## 擴充功能的設定

擴充功能通常啟動後就可以接受設定。大部份的擴充功能，可以在 `activate` 後透過 Hash 來設定，或是使用程式區塊來設定也可以：

```ruby
activate :asset_hash, :exts => %w(.jpg) # 僅啟動 .jpg

# 或者：

activate :asset_hash do |opts|
  opts.exts += $(.ico)
end
```

## 依環境個別設定

如果只針對 development 環境或是只在 Build 時啟動某些功能，可以透過區塊來做設定：

```ruby
configure :development do
  set :debug_assets, true
end

configure :build do
  activate :minify_css
end
```
