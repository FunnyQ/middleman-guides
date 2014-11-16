---
title: Local Data
---

# Local Data

有時候，將內容的資料從視圖中給獨立出來會帶來很多好處。透過這樣的方式，某些團隊成員可以專注在資料內容的建立，同時其他成員可以全力的在網站架構上努力。Local Data 讓你可以在 `data` 資料夾中建立 `.yml`、`.yaml` 或是 `.json` 檔案，並可以在樣板中使用這些檔案中的資料。`data` 資料夾和 `source` 是在同一個階層，位於專案的跟目錄下。

例如，`data/people.yml` 中有以下內容：

``` yaml
friends:
  - Tom
  - Dick
  - Harry
```

在樣板檔案中的某個地方，我們可以透過下面的方式來存取 Local Data 中的資料：

``` html
<h1>好友列表</h1>
<ol>
  <% data.people.friends.each do |f| %>
  <li><%= f %></li>
  <% end %>
</ol>
```

這將會被輸出成以下內容：

``` html
<h1>友だち一覧</h1>
<ol>
  <li>Tom</li>
  <li>Dick</li>
  <li>Harry</li>
</ol>
```

`.yml`（people）的資料在樣板中將可以透過 `data.people` 來存取。Local Data 允許使用子目錄結構。例如有個檔案 `data/people/tom.yml`，將可以在樣板中透過 `data.people.tom` 來存取。

你也可以用 JSON 格式來取代 YAML 格式。例如上面的例子如果用 JSON 格式 `data/people.json` 來記錄的話，將會像下面這樣：

``` json
{
  "friends": [
    "Tom",
    "Dick",
    "Harry"
  ]
}
```
