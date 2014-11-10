---
title: 目錄結構
---

# 目錄結構

在預設的情形下，Middleman 所生成的目錄結構會像下面這個樣子：

``` ruby
mymiddlemansite/
+-- Gemfile
+-- Gemfile.lock
+-- config.rb
+-- source
    |   .gitignore
    +-- images
    ¦   +-- background.png
    ¦   +-- middleman.png
    +-- index.html.erb
    +-- javascripts
    ¦   +-- all.js
    +-- layouts
    ¦   +-- layout.erb
    +-- stylesheets
        +-- all.css
        +-- normalize.css
```

## 主要的資料夾

Middleman 針對特定不同的目的，會使用到 `source`、`build`、`data` 和 `lib` 等資料夾。每個資料夾會存在於 Middleman 專案的根目錄。

### source 資料夾

`source` 資料夾會用來存放頁面樣板的 JavaScript、CSS、以及圖像等，最後會編譯成靜態網站的原始碼。

### build 資料夾

`build` 資料夾會用來存放編譯後所輸出的靜態檔案。

### data 資料夾

Local Data 功能所需要的 `.yml`、`.yaml` 或是 `.json` 檔案，都必須存放在 `data` 資料夾中，這些檔案將能夠在頁面樣板中透過 Local Data 功能依需求取用。這個 `data` 資料夾必須存在於專案的跟目錄，與 `source` 同一個階層，才能正常運作。詳細請參考 [Local Data](/tw/advanced/local-data/) 文件內容。

### lib 資料夾

`lib` 資料夾是用來存放額外自行擴充 Ruby module，包含 [樣板輔助函式（Helper）](/tw/basics/helpers/)等開發應用過程中會用到的自訂擴充函式庫。若你曾經使用過 Rails 的話，應該會對這樣的配置方式很熟悉。

