---
title: Pretty URLs (Directory Indexes URL 美化)
---

# Pretty URLs (Directory Indexes URL 美化)

在預設的情形下，Middleman 會依照你在專案中的描述正確地輸出相對應的檔案。舉例來說 `source` 資料夾中若有一個檔案叫做 `about-us.html.erb`，那麼在編譯輸出時就會產生一個檔案叫做 `about-us.html`。如果把這個網站放到網址為 `example.com` 的伺服器上，那麼這個頁面的存取網址將會是：`http://example.com/about-us.html`

Middleman 提供 Directory Indexes 的擴充功能，可以將每個 `.html` 獨立製作成一個資料夾，並將其內容輸出為該資料夾中的 index。想使用這樣的功能只需要在 `config.rb` 檔案中加入下面的描述即可：

``` ruby
activate :directory_indexes
```

如此一來，這個專案在編譯輸出時，`about-us.html.erb` 檔案會被輸出為 `about-us/index.html` 的降世。將編譯後的檔案上傳到支援 "index files" 的伺服器（如 Apache 或 Amazon S3）時，存取這個頁面的網址將會像下面這樣：

``` ruby
http://example.com/about-us
```

若想要輸出成別的檔名，可以透過 `index_file` 變數來設定。例如，IIS 伺服器會需要使用 default.html，設定會像下面這樣：

``` ruby
set :index_file, "default.html"
```

若是希望輸出成 php 檔案的話：

``` ruby
set :index_file, "index.php"
```

#### 關於 assets 路徑的注意事項

開啟 Directory Indexes 功能的狀況下，僅單純使用檔名來存取 assets 將會發生錯誤（例如圖片）。我們將會需要使用絕對路徑來存取 assets。

``` ruby
![神奇圖片](/posts/2013-09-23-some-interesting-post/amazing-image.png)
```

若希望把這個過程自動化，可以讓 MardDown 先經過 ERb 處理。例如 `/posts/2013-09-23-some-interesting-post.html.markdown.erb` 中我們就可以使用下面的方法來存取 assets：

``` ruby
![神奇圖片](<%= current_page.url %>some-image.png)
```

## 排除例外

若有些頁面我們不想使用 Directory Indexes 功能的話，可以透過下面的方式來排除：

``` ruby
page "/i-really-want-the-extension.html", :directory_index => false
```

若想要一次排除多個檔案，`page` 也可以接受使用正規表示式。

此外，在獨立的頁面樣板透過 [YAML 格式的 Frontmatter](/tw/basics/frontmatter/) 加入 `directory_index: false` 敘述，也可以達成相同的目的。

## 手動 Indexes

頁面樣板的檔名原本就是 `index.html` 的情形下，Middleman 將不會對這個檔案進行處理。例如 `my-page/index.html.erb` 將會和你想的一樣，被輸出為 `my-page/index.html`。
