---
title: Asset Pipeline
---

# Asset Pipeline

## 套件相依性管理

[Sprockets] 是一套為了管理 Javascript（與 CoffeeScript）的函式庫而開發的工具，除了可以宣告相依性，也可讀取引入第三方的程式碼。Sprockets 讓 .js 和 .coffee 檔案擁有 `require` 方法的能力，可以簡單的將專案中其他的外部檔案讀取引入，或將第三方 gem 的檔案給引入 .js 或 .coffee。

假設我有一個檔案就做 `jquery.js`，裡面有 jQuery 函式庫；另外還有一個 `app.js` 有我專案中需要的程式碼。下面的例子，表示我可以在 app 檔案執行前先把 jquery 給讀取引入到專案程式碼：

``` javascript
//= require "jquery"

$(document).ready(function() {
  $(".item").pluginCode({
    param1: true,
    param2: "maybe"
  });
});
```

同樣的功能在 CSS 檔案中也可以使用：

``` css
/*
 *= require base
 */

body {
  font-weight: bold;
}

```

如果你使用的是 Sass，那麼你應該使用 Sass 所提供的 `@import` 功能，而不是使用 Sprockets 提供的方法。

## 只部署合併後的 assets 檔案

如果你希望 `middleman build` 指令執行後，在 `build` 資料夾中只有合併後的 assets 檔案，那麼「被合併」的對象檔案名稱必須在起始處包含一個底線（_）。例如，主要的 `/source/javascripts/all.js` 包含了以下的相依性關係：

``` javascript
//= require "_jquery"
//= require "_my_lib_code"
//= require "_my_other_code"
```

那麼，在 `/source/javascripts/` 目錄下必須存在以下檔案：`_jquery.js`, `_my_lib_code.js`, `_my_other_code.js`。輸出的結果，`/build/javascripts/` 目錄下將只會有 `app.js` 一個包含了所有相依程式碼的檔案。

## Asset Gems

你可以使用 Gems 中的 Assets，只要像下面這樣，透過 `Gemfile` 來導入 gem：

```ruby
gem "bootstrap-sass", :require => false
```

`:require => false` 很重要。由於不少 gem 預設情形下是為了 Rails 設計的，當這些 gem 要嘗試 hook 上 Rails 或 Compass 的時候會發生錯誤。只要先避免 require 這些 gem 來迴避錯誤的發生，Middleman 將會幫你處理好後續的雜務。

只要將這些 gem 給加入相依性關聯，其中的圖形或字型等檔案就會被自動讀入專案。在 JavaScript 或 CSS 檔案中就可以透過 `require` 或 `@import` 來使用這些 assets。

假如你並不想在透過 require 或 @import 的方式來使用這些 assets，而希望在 HTML 中直接的取用 gem 中的 stylesheet 或 JS 檔案，則必須在 `config.rb` 檔案中明白表示需要將這些檔案給讀取進來。

```ruby
sprockets.import_asset 'jquery-mobile'
```

這樣的話就可以透過 `script` 標籤或是 `javascript_include_tag` 來直接使用那些檔案。

## 將檔案路徑加入 Sprockets

如果你有 `:js_dir` 或 `:css_dir` 之外的其他檔案想要透過 Sprockets 的功能來引入專案，可以透過編輯 `config.rb` 的方式來將額外的檔案路徑給追加到 Sprockets 中。

*請注意*，只能夠透過 `#append_path` 來追加檔案路徑一次，不能重複。否則的話會在 `middleman` 的 Sitemap 中重複出現 —— 因為每個 `#append_path` 敘述都會會依序加入 `#appended_paths` 列表。若有重複的情形將會讓編譯輸出的時間變得冗長。此外 assets 檔案在做最小化壓縮處理的過程中發生衝突也多是這個原因造成的。

`config.rb` 中的敘述寫法如下：

```ruby
# 透過字串加入
sprockets.append_path '/my/shared/assets1/'

# 透過 Pathname 來新增也可以
sprockets.append_path Pathname.new('/my/shared/assets2/')
```

如果你有整個列表想要加入 Sprockets，可以透過下面這段 code snippet 來處理，確保每個路徑只會被加入一次：

```ruby
%w(path1 longer/path2 longer/path3).each do |path|
  next if sprockets.appended_paths.include? path

  sprockets.append_path path
end
```

Sprockets 也支援 Bower。只需要將 Bower 的 Components 檔案路徑加入即可：

```ruby
sprockets.append_path 'bower_components'
```

bower 管理下的 assets 檔案 —— 圖片、字型等等，要在 Middleman 專案中使用的話，必須透過 `sprockets.import_asset` 方法來將它們導入。例如，你可以在 `config.rb` 中透過下面的方法，把 `jquery` 元件中、`bower.json` 在 [`main` 區塊](https://github.com/bower/bower.json-spec)有提到的所有檔案都一次導入。

```ruby
sprockets.import_asset 'jquery'
```

如果你希望指導入特定的 assets 檔案，可以使用如 `<component_name>/<assets_的檔案路徑>` 的相對路徑：

```ruby
sprockets.import_asset 'jquery/dist/jquery.js'
```

如果你需要一個完全獨立自訂的輸出路徑，可以透過 `#import_asset` 區塊來描述。這個區塊會將 'jquery/dist/jquery.js' 當作 Locical path，並回傳結合 `Pathname` 之後的完整獨立路徑。

```ruby
sprockets.import_asset('jquery/dist/jquery.js') do |logical_path|
  Pathname.new('javascripts_new.d') + logical_path
  # => javascripts_new.d/jquery/dist/jquery.js
end
```

如果要在這個區塊中使用中括號的話，請務必將 `#import_asset` 的參數加上小括號。如果省略小括號的話，中括號區塊可能會傳遞到其他的方法而不是原本預想的 `#import_asset`，而造成你覺得奇怪為什麼輸出的路徑不正確。

```ruby
sprockets.import_asset('jquery/dist/jquery.js') { |logical_path| Pathname.new('javascripts_new.d') + logical_path }
```

如果要讓這個工作流程更自動化一些，可以透過 [rake](https://github.com/jimweirich/rake) 來對檔案清單做操作。或是你習慣使用 [hike](https://github.com/sstephenson/hike) 也是可以的。但是*不能*夠適用於 `sprockets.each_file`，因為在 `config.rb` 中的 `sprockets` 是在 [faked sprocket
environment](https://github.com/middleman/middleman-sprockets/blob/master/lib/middleman-sprockets/config_only_environment.rb) 下運作，所以沒有這個方法。另外請注意，要實現這個動作，你可能需要事先將 `gem "rake"` 或 `gem "hike"` 給加入 Gemfile 中。

```ruby
require 'rake/file_list'
require 'pathname'

bower_directory = 'vendor/assets/components'

# 建立一個搜尋的模式
patterns = [
  '.png',  '.gif', '.jpg', '.jpeg', '.svg', # 圖片
  '.eot',  '.otf', '.svc', '.woff', '.ttf', # 字型
  '.js',                                    # Javascript
].map { |e| File.join(bower_directory, "**", "*#{e}" ) }

# 建立檔案清單、排除不需要的檔案
Rake::FileList.new(*patterns) do |l|
  l.exclude(/src/)
  l.exclude(/test/)
  l.exclude(/demo/)
  l.exclude { |f| !File.file? f }
end.each do |f|
  # 導入相對路徑
  sprockets.import_asset(Pathname.new(f).relative_path_from(Pathname.new(bower_directory)))
end
```

## Helpers

`*.scss` 檔案中可以使用的 Helpers：

* `image-path()`, `image-url()`
* `font-path()`, `font-url()`

這些 Helper 可以轉換為正確的相對或絕對路徑。例如 `image-path('lightbox2/img/close.png')` 會被轉換為 `images/lightbox2/img/close.png` 。bower 管理之下的 assets 檔案，必須透過相對路徑來取用。例如 `lightbox2` component 中的一個圖片、必須透過 `lightbox2/img/close.png` 來取用。

## Compass

Middleman 預設就支援使用 [Compass]。Compass 是一個 Sass 的框架，讓我們可以方便地寫出支援跨瀏覽器的 stylesheet。Compass 也有它自己的擴充套件，例如 [Susy]，也可以在 Middleman 中使用。所有的 Sporockets 路徑 Helper 例如 `image-url` 會被連結至 Middleman 的 sitemap 中，因此其他如（:asset_hash）也會影響你的 Stylesheet。

[Sprockets]: https://github.com/sstephenson/sprockets
[Compass]: http://compass-style.org
[Susy]: http://susy.oddbird.net
