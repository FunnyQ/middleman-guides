---
title: 快速入門
---

# 簡介

Middleman 是一個整合了各種現代 Web 開發工具、為了製作靜態網站而開發的 CLI 工具。因此，使用 Middleman 必須不排斥終端機操作，並對指令操作方式有一定程度的瞭解。

Middleman 以 Ruby 語言以及 Sinatra 框架為基礎開發而成。若能深入了解 Ruby 與 Sinatra，使用者應該就更能理解 Middleman 底層的運作方式。

## 安裝

Middleman 的發布是經由 RubyGems 這個套件管理工具。也就是說，要使用 Middleman 的話必須先有可運行 Ruby 的開發環境並且先安裝好 RubyGems。

Mac OS X 一開機就內建了 Ruby 與 RubyGems，因為有些 Middleman 的相依套件在安裝時可能需要編譯（Compile），而 OS X 必須安裝 Xcode 才能進行編譯的動作。Xcode 可以透過 [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?ls=1&mt=12) 來下載安裝。若你有 Apple Developer 的帳號，這個[下載頁面](https://developer.apple.com/downloads/index.action)也可以找到 command Line Tools for Xcode 的安裝檔。

當準備好 Ruby 與 RubyGems 之後，在終端機執行以下的指令：

``` bash
gem install middleman
```

這將會一併安裝 Middleman、以及相依的函式庫、command-line tools 等必要的工具。

安裝完成後，你的開發環境將會增加一個新的指令「middleman」，包含三個功能：

* middleman init
* middleman server
* middleman build

其個別的功用將會在下面一一介紹。

## 開始開發一個新的網站: middleman init

在開始之前，我們必須建立一個專案資料夾來讓 Middleman 能夠工作。可以使用已經存在的資料夾、或是使用 `middleman init` 指令來讓 Middleman 幫你自動製作一個。

透過 `middleman init` 指令指定專案資料夾，Middleman 將會在其中建立好需要的檔案與目錄。（若指定的資料夾不存在的話 Middleman 會為你建立資料夾）

``` bash
middleman init my_new_project
```

### 架構

每次建立新的專案，都會自動生成一個基本的網站開發用架構。這整個自動生成的架構中，所有檔案與目錄結構都是專案開發過程中會使用到的。

一個全新的專案會包含一個 `source` 資料夾和 `config.rb` 檔案。source 資料夾是你開發網站的地方。這個架構中會包含 javascript、CSS 以及 images 資料夾，你可以依照自己的習慣來自訂這些資料夾的名稱。

`config.rb` 檔案是 Middleman 的設定檔。編譯時是否進行壓縮、「Blog 模式」是否開啟等進階功能也是在這個檔案中進行設定。

#### Gemfile

Middleman 會以 Bundler 的 Gemfile 檔案為標準來管理 gems 的相依性。當建立了一個新的專案，Middleman 會同時產生一個 Gemfile，其中包含了你所使用的 Middleman 版本。這將會讓 Middleman 鎖定在固定的版本（例如 3.x 系列）。當然你也可以透過修改 Gemfile 中的設定，用 `:git` 參數指定 Github 上的最新版本。專案中所使用的 Plugins 或 Library 都可以透過 Gemfile 來管理，如此一來 Middleman 會在啟動時自動 `require` 這些相依的套件。 

#### config.ru

config.ru 檔案中描述了支援 Rack 的伺服器所需要使用到的設定。這個檔案可以讓使用者在開發時可以利用像 Heroku 這樣支援 Rack 的伺服器來方便地進行開發。但請不要忘記 Middleman 是為了建立「靜態網站」而開發的這件事。

新建專案時，如果要讓專案資料夾內包含 config.ru 檔案的基本雛型，可以在 init 指令後加入 `--rack` flag：

``` bash
middleman init my_new_project --rack
```

若專案已經完成初始化程序（在既有的專案中），卻想要利用 pow 或其他的開發伺服器，只要建立一個包含以下內容的 config.ru 檔案就可以了：

```
require 'rubygems'
require 'middleman/rack'

run Middleman.server
```

### 專案樣板

除了預設的基本專案架構之外，Middleman 也包含了 [HTML5 Boilerplate], [SMACSS], 或 [Mobile Boilerplate](http://html5boilerplate.com/mobile/) 等額外的專案樣板。Middleman 擴充功能（例如[middleman-blog](/jp/basics/blogging/)）也同樣可以使用各自不同的專案樣板。想要使用不同的專案樣板來建立新專案，只需要透過 `-T` 或是 `--template` flag 就可以指定想要使用的專案樣板。例如，如果想要使用 HTML5 Boilerplate 的專案樣板，新建專案的指令就會像下面這樣：

``` bash
middleman init my_new_boilerplate_project --template=html5
```

最後，你可以在 `~/.middleman/` 資料夾中建立屬於自己的專案樣板架構。例如，如果建立了 `~/.middleman/mobile` 資料夾，把製作行動應用網站的相關檔案都放在其中的話，就能夠在以後方便地透過專案樣板的功能快速建立新的專案。

help flag 可以列出所有可用的專案樣板

``` bash
middleman init --help
```


例如這個指令能夠使用自己的 mobile 專案樣板來建立新專案：

``` bash
middleman init my_new_mobile_project --template=mobile
```

### 內建的專案樣板

Middleman 內建了幾個基本的專案樣板如下:

**[HTML5 Boilerplate]**

``` bash
middleman init my_new_mobile_project --template=html5
```

**[SMACSS]**

``` bash
middleman init my_new_smacss_project --template=smacss
```

**[Mobile Boilerplate](http://html5boilerplate.com/mobile/)**

``` bash
middleman init my_new_mobile_project --template=mobile
```

### 社群提供的專案樣板

這邊有一些 [使用者社群所開發的專案樣板](http://directory.middlemanapp.com/#/templates/all) 可以提供選擇使用。

## 開發環境 (middleman server)

Middleman 從一開始就將開發環境的程式碼與生產環境（production）的程式碼給分離開了。這樣可以在開發時使用許多方便的工具（例如 [Haml](http://haml-lang.com) 或 [Sass](http://sass-lang.com) 等等）而不影響最後產出的 production。我們把這些不同的環境分類為「開發環境（Development Cycle）」和「靜態網站」。

使用 Middleman 時大部分的時間都會在開發環境中。

透過終端機，我們可以在我們的專案資料夾中啟動一個預覽成果用的伺服器：

``` bash
cd my_project
bundle exec middleman server
```

這個指令將會建立一個本地端的網頁伺服器： `http://localhost:4567/`

我們可以在 `source` 資料夾中建立或編輯需要的檔案，在預覽伺服器上即時地觀察編輯後的成果。

在終端機中按下 `CTRL-C` 組合鍵，即可停止預覽伺服器的運作。

### 素顏的 middleman 指令

單純地使用 `middleman` 同樣會啟動預覽伺服器。

``` bash
bundle exec middleman
```

這個指令和 `middleman server` 是完全一模一樣的。

## 輸出靜態網站 (middleman build)

最後，當我們準備好要輸出靜態網站、或是在 "Blog 模式" 下想要產出靜態的部落格，我們將需要進行一個 Build 的動作。在我們的專案資料夾下，於終端機執行 `middleman build`：

``` bash
cd my_project
bundle exec middleman build
```

這個指令會將 `source` 資料夾中的每個檔案轉換成靜態的檔案。樣板檔案將會被編譯、靜態檔案會被複製、Build 過程中指定使用的功能（如壓縮檔案）也會在這個時候執行。Middleman 會自動地清除上次 Build 時不需要的檔案。

[HTML5 Boilerplate]: http://html5boilerplate.com/
[SMACSS]: http://smacss.com/
