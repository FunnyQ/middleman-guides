---
title: Frontmatter
---

# Frontmatter

Frontmatter 是以 YAML 或 JSON 格式，記錄在樣板中最上方，讓每個獨立的頁面可以擁有自己獨立的變數的功能。

## YAML 格式

我們用簡單的 ERb 樣板作為例子，在其中加入專屬此頁面獨立的變數，並讓它指定特定 Layout 來做輸出。

``` html
---
layout: "custom"
my_list:
  - one
  - two
  - three
---

<h1>清單</h1>
<ol>
  <% current_page.data.my_list.each do |f| %>
  <li><%= f %></li>
  <% end %>
</ol>
```

Frontmatter 是記錄在樣板檔案中最上方，獨立於樣板的主要內容，頭尾用連續三個半形連字號 `---` 包起來的一段程式碼。在這個區塊中可以加入專屬於此頁面樣板的變數，並使用 `current_page.data` 來以 Hash 的形式取用。`layout` 的設定會直接傳給 Middleman，指定用來輸出的 Layout。`ignore`、`directory_index` 等頁面設定參數也可以用 Forntmatter 的方法來設定。

## JSON 格式

Frontmatter 也可以使用 JSON 格式來記錄。不同的地方在於 JSON 格式 Frontmatter 的頭尾必須用連續三個分號 `;;;` 來與樣板主要內容做區隔：

``` html
;;;
"layout": "custom",
"my_list": [
  "one",
  "two",
  "three"
]
;;;
```

在頁面樣板中 JSON 的使用方法和 YAML 完全相同。
