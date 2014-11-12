---
title: 動態頁面
---

# 動態頁面

## Proxy 的定義

Middleman 輸出頁面的功能並不侷限於與樣板檔案的 1 對 1 的關係。換句話說，你可以透過不同的變數，根據一個樣板檔案來輸出數個不同的頁面。我們可以在 `config.rb` 中用 `proxy` 方法來產生一個 Proxy，將特定的 URL 路徑指定給特定的樣板檔案。下面就是 `config.rb` 設定的一個例子：

``` ruby
# 假設 source/about/template.html.erb 是實際存在的檔案
["tom", "dick", "harry"].each do |name|
  proxy "/about/#{name}.html", "/about/template.html", :locals => { :person_name => name }
end
```

在上述設定下，專案編譯輸出的時候，將會產生四個檔案：

* '/about/tom.html' (樣板中的 `person_name` 將會被指定為 "tom")
* '/about/dick.html' (樣板中的 `person_name` 將會被指定為 "dick")
* '/about/harry.html' (樣板中的 `person_name` 將會被指定為 "harry")
* '/about/template.html' (樣板中的 `person_name` 將會被指定為 nil )

在絕大多數的情形下，你應該不會希望 template.html 被輸出且包含了個 nil，Middleman 可以透過下面的方式來略過輸出 template 本身：

``` ruby
["tom", "dick", "harry"].each do |name|
  proxy "/about/#{name}.html", "/about/template.html", :locals => { :person_name => name }, :ignore => true
end
```

這樣一來就只會輸出 `about/tom.html`, `about/dick.html` 和 `about/harry.html` 等三個檔案了。

## 需要忽略的檔案

在 `config.rb` 使用 `ignore` 方法，可以在編譯輸出時忽略任意路徑上的檔案。

``` ruby
ignore "/ignore-this-template.html"
```

ignore 方法除了正確的路徑之外，也可以透過正規表示式來指定複數個特定的檔案。
