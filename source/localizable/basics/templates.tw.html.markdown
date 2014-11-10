---
title: 樣板系統
---

# 樣板系統

Middleman 提供了許多可應用的樣板語言來簡化你在開發 HTML 時會遇到的問題。從基本的使用 Ruby 變數或迴圈，到提供可編譯成 HTML 的簡化格式（如 Haml）。Middleman 預設就支援 ERb、Haml、Sass、Scss 與 CoffeeScript 等語言格式。其他還有許多可供選擇的樣板語言，[請參考此列表](#其他樣板語言)。

## 樣板的基本應用

預設的樣板語言為 ERb。ERb 看起來就像是一般的 HTML，但他額外提供了使用變數、呼叫 Ruby 方法、迴圈、以及 if 等條件判斷式等功能。下面將會列出幾個 ERb 基本應用的例子。

在 Middleman 中所有的樣板檔案都會在檔名包含該樣板的副檔名。例如，使用 ERb 編寫的 index 頁面，其檔名就會被命名為 `index.html.erb`，包含了完整的原始檔名 `index.html`，以及 ERb 專屬的副檔名 `.erb`。

首先，我們先在這個檔案寫入單純的 HTML：

``` html
<h1>歡迎光臨</h1>
```

我們可以很簡單地加入一些迴圈：

``` html
<h1>歡迎光臨</h1>
<ul>
  <% 5.times do |num| %>
    <li>邦伯數數： <%= num %></li>
  <% end %>
</ul>
```

## Layout

Layout 功能讓你可以在不同的獨立頁面中，使用部分共同的 HTML 結構。舉例來說，有經驗的 PHP 開發者可能會使用 include 函式，在每個不同頁面的起始與結束部分都分別引入相同的 header 與 footer。但在 Ruby 的世界裡，包含 Middleman，我們使用了可以說完全相反的做法來達成相同的目的。「Layout」包含了相同的 header 與 footer 結構、並將不同的頁面內容給引入 Layout 中的主要區塊。

最基本的 Layout 會包含一些需要共用的內容，以及一個 `yield` 區塊讓樣板系統置入不同的頁面內容。

以 ERb 為例，Layout 的使用方法如下：

``` html
<html>
<head>
  <title>我的網站</title>
</head>
<body>
  <%= yield %>
</body>
</html> 
```

同樣以 ERb 為例，獨立頁面的樣板則會像下面這樣：

``` html
<h1>Hello World</h1>
```

經過組合後出書的 HTML 靜態頁面則會像下面這樣：

``` html
<html>
<head>
  <title>我的網站</title>
</head>
<body>
  <h1>Hello World</h1>
</body>
</html>
```

關於副檔名與編譯器，因為 Layout 功能在編譯的過程中會和樣板有些不同的功能，所以一定要注意副檔名是否正確。理由如下：

在樣板中不同的區塊將會被集合在一起，所以檔案副檔名是有重要意義的。例如，Layout 的檔名若是 `layout.html.erb` 的話，會告訴編譯器這個檔案需要從 erb 編譯成 html。

也就是說，一個檔案的編譯取決於副檔名的結構，由右而左依序編譯，而最後會被編譯輸出為最左邊副檔名形式的檔案。以上面的例子來說，檔案會被從 erb 編譯成 html，並最後輸出為 `layout.html` 的形式。

但 Layout 和樣板不同，並不需要被輸出成 html 形式的檔案。所以如果 Layout 的檔名包含了 `.html` 會在編譯（Build）時發生錯誤。所以，Layout 應該只包含你所使用的樣版引擎需要的副檔名即可，例如 `layout.erb`。

### 自訂 Layout

Middleman 預設會把專案中每個頁面套用到同樣的 Layout。但有時候我們會需要指定特定的頁面到不同的 Layout 上。例如，一個網站可能會有公開的 public 與 admin 兩個不同的區塊。

初始的 Layout 會在 `source` 資料夾中被命名為 "layout" 並包含你使用的樣版引擎所需要的副檔名，預設會是 `layout.erb`。若有複數個 Layout 的情形下，你應該把這些不同的 Layout 放到 `source/layouts` 資料夾中收納好。

若要製作一個 admin 用的 Layout，我們需要在 `source/layouts` 資料夾中新增一個檔案「admin.erb」。檔案的內容如下：

``` html
    <html>
    <head>
      <title>管理頁面</title>
    </head>
    <body>
      <%= yield %>
    </body>
    </html>
```

接著，我們需要指定哪些頁面會使用這個 Layout 來輸出結果。有以下兩個方法可以使用。如果你想要將數個頁面的群組一次指定使用 admin Layout，可以在 `config.rb` 檔案中透過「page」命令來指定。例如，`source` 資料夾中有一個 `admin` 資料夾，這個目錄下的頁面都要指定使用 admin Layout 的話，只需要在 `config.rb` 中加入下述內容：

``` ruby
page "/admin/*", :layout => "admin"
```

在頁面的路徑使用 wildcard（萬用字元），可以將 admin 資料夾下的所有頁面一次指定使用 admin Layout。

此外，也可直接指定單一頁面，例如，source 資料夾下有個 `login.html.erb` 檔案，但我們想要指定它使用 admin Layout。這個樣板的內容假設如下：

``` html
<h1>Login</h1>
<form>
  <input type="text" placeholder="Email">
  <input type="password">
  <input type="submit">
</form>
```

接著我們可以透過下面的方式來指定自訂 Layout：

``` ruby
page "/login.html", :layout => "admin"
```

這麼一來 login 頁面就會使用 admin Layout 來輸出了。但除了使用 `config.rb` 來指定自訂 Layout 的方法之外，利用 [Frontmatter] 功能也可以更靈活地在每個獨立的頁面分別指定不同的 Layout。用先前的例子來說，`login.html.erb` 如果要個別指定他要使用的 Layout 的話，可以用下面這樣的方式來指定：

``` html
---
layout: admin
---

<h1>Login</h1>
<form>
  <input type="text" placeholder="Email">
  <input type="password">
  <input type="submit">
</form>
```

### 巢狀 Layout

巢狀 Layout 允許你使用成組的 Layout。要理解這個功能，最簡單的例子就是透過 `middleman-blog` 擴充功能。Blog 的文章是整個網站所有內容的子集合。它會有獨立的內容與結構，但卻還是被網站的基本構造給封裝著（例如 header、footer 等）。

以下是簡單的例子

``` html
<html>
  <body>
    <header>頁首</header>
    <%= yield %>
    <footer>頁尾</footer>
  </body>
</html>
```

Blog 文章會放在 `blog/my-article.html.markdown` 中。在 `config.rb` 中我們把所有的 Blog 文章指定使用 `article_layout` Layout。檔案內容如下：

``` ruby
page "blog/*", :layout => :article_layout
```

而 `layouts/article_layout.erb` 的內容如下：

``` html
<% wrap_layout :layout do %>
  <article>
    <%= yield %>
  </article>
<% end %>
```

和一般的 Layout 一樣，`yield` 是樣板輸出內容的區塊。以這個例子來說輸出結果會如下所示：

``` html
<html>
  <body>
    <header>頁首</header>
    <article>
      <!-- 樣板 / Blog 文章內容 -->
    </article>
    <footer>頁尾</footer>
  </body>
</html>
```

### 完全關閉 Layout 功能

有些情況下，我們可能會希望完全不使用 Layout 提供的功能。我們可以在 `config.rb` 中完全關閉 Layout 功能。

``` ruby
set :layout, false

# 若是要關閉每些特定頁面的 Layout 功能
page '/foo.html', :layout => false
```

## Partial（頁面片段）

Partial 是在不同的頁面中共用相同的內容，避免內容重複的一種方法。Partial 在 Layout 和樣板中都可以使用。延續上面的例子，我們有兩個不同的 Layout，一般頁面與 admin 管理頁面，這兩個頁面都有相同的 footer 內容。為了避免重複，我們可以把 footer 做成 partial 讓兩個 Layout 都能使用。

partial 的檔案命名會用底線「`_`」為起始，並包含你使用的樣版引擎所需的副檔名。舉例來說，`source` 資料夾中新增一個 `_footer.erb` 檔案表示我們建立了一個 footer 頁面片段：

``` html
<footer>
  Copyright 2011
</footer>
```

接著，我們使用「partial」方法來把剛剛建立的頁面片段應用到預設的 Layout 中：

``` html
<html>
<head>
  <title>我的網站</title>
</head>
<body>
  <%= yield %>
  <%= partial "footer" %>
</body>
</html>
```

接著是 admin Layout：

``` html
<html>
<head>
  <title>管理頁面</title>
</head>
<body>
  <%= yield %>
  <%= partial "footer" %>
</body>
</html>
```

這樣一來，`_footer.erb` 的所有變動都會同時應用到使用了這個 partial 的樣板或 Layout 上。

當你發現一直在重複做複製貼上的動作時，就是考慮要建立 partial 的最佳時機。

當你開始使用 partial 之後，可能會發現需要傳遞不同的變數到 partial 中，下面的方法可以解決這個需求：

``` html
<%= partial(:paypal_donate_button, :locals => { :amount => 1, :amount_text => "Pay $1" }) %>
<%= partial(:paypal_donate_button, :locals => { :amount => 2, :amount_text => "Pay $2" }) %>
```

接著，在此 partial 中你可以像下面這樣設定好相應的變數位置：

``` html
<form action="https://www.paypal.com/cgi-bin/webscr" method="post">
  <input name="amount" type="hidden" value="<%= "#{amount}.00" %>" >
  <input type="submit" value="<%= amount_text %>" >
</form>
```

若要更詳細的說明，可以參考 [Padrino partial helper] 的文件。

## 樣版引擎選項

在 `config.rb` 可以對樣版引擎進行更深入的設定：

```ruby
set :haml, { :ugly => true, :format => :html5 }
```

## Markdown

在 `config.rb` 可以選擇你喜歡的 Markdown 編譯器，並根據喜好自訂設定：

```ruby
set :markdown_engine, :redcarpet
set :markdown, :fenced_code_blocks => true, :smartypants => true
```

使用 RedCarpet 的情形下，Middleman 可以使用預設的 helper 來處理 link 與 image tags，也就是說 `:relative_links` 和 `:asset_hash` 可以正常的運作。但 Middleman 預設是使用 Kramdown 作為 markdown 的編譯器，因為安裝上相對比較容易。

### 其他樣板語言

以下是 Tilt 支援的樣版引擎語言與其對應 RubyGems 的列表。必須經過安裝（並在 `config.rb` 中做好設定）才能使用。(此列表出自 [Tilt](https://github.com/rtomayko/tilt/))

樣版引擎                 | 檔案副檔名              | 需要的函式庫
------------------------|------------------------|----------------------------
Slim                    | .slim                  | slim
Erubis                  | .erb, .rhtml, .erubis  | erubis
Less CSS                | .less                  | less
Builder                 | .builder               | builder
Liquid                  | .liquid                | liquid
RDiscount               | .markdown, .mkd, .md   | rdiscount
Redcarpet               | .markdown, .mkd, .md   | redcarpet
BlueCloth               | .markdown, .mkd, .md   | bluecloth
Kramdown                | .markdown, .mkd, .md   | kramdown
Maruku                  | .markdown, .mkd, .md   | maruku
RedCloth                | .textile               | redcloth
RDoc                    | .rdoc                  | rdoc
Radius                  | .radius                | radius
Markaby                 | .mab                   | markaby
Nokogiri                | .nokogiri              | nokogiri
CoffeeScript            | .coffee                | coffee-script
Creole (Wiki markup)    | .wiki, .creole         | creole
WikiCloth (Wiki markup) | .wiki, .mediawiki, .mw | wikicloth
Yajl                    | .yajl                  | yajl-ruby
Stylus                  | .styl                  | stylus

[Haml]: http://haml-lang.com/
[Slim]: http://slim-lang.com/
[Markdown]: http://daringfireball.net/projects/markdown/
[these guides are written in Markdown]: https://raw.github.com/middleman/middleman-guides/master/source/guides/basics-of-templates.html.markdown
[Frontmatter]: /tw/basics/frontmatter/
[Padrino partial helper]: http://www.padrinorb.com/api/classes/Padrino/Helpers/RenderHelpers.html
