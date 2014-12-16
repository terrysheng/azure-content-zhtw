<properties linkid="dev-ruby-web-app-with-linux-vm" urlDisplayName="Ruby on Rails Web App on Azure using Linux VM" pageTitle="Ruby on Rails Web App on Azure using Linux VM" metaKeywords="Azure Ruby web application, Azure Ruby application, Ruby app Azure, Ruby azure vm, ruby virthal machine, ruby linux vm" description="Host a Ruby on Rails-based website on Azure using a Linux virtual machine. " metaCanonical="" services="virtual-machines" documentationCenter="Ruby" title="Ruby on Rails Web application on an Azure VM" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="ruby" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Azure VM 上的 Ruby on Rails Web 應用程式

本教學課程說明如何使用 Linux 虛擬機器，在 Azure 上裝載 Ruby on Rails 型網站。本教學課程假設您先前沒有使用 Azure 的經驗。完成本教學課程後，您將在雲端啟動並執行 Ruby on Rails 型應用程式。

您將了解如何：

-   設定開發環境

-   設定 Azure 虛擬機器代管 Ruby on Rails。

-   建立新 Rails 應用程式

-   使用 scp 將檔案複製到虛擬機器

以下是完成後應用程式的螢幕擷取畫面：

![顯示列出貼文的瀏覽器][顯示列出貼文的瀏覽器]

## 本文內容

-   [設定開發環境][設定開發環境]

-   [建立 Rails 應用程式][建立 Rails 應用程式]

-   [測試應用程式][測試應用程式]

-   [建立 Azure 虛擬機器][建立 Azure 虛擬機器]

-   [將應用程式複製到 VM][將應用程式複製到 VM]

-   [安裝 gem 並啟動應用程式][安裝 gem 並啟動應用程式]

-   [後續步驟][後續步驟]

## <span id="setup"></span></a>設定開發環境

1.  在開發環境中安裝 Ruby。端視您的作業系統而定，步驟可能不同。

    -   **Apple OS X** - Ruby 有許多 OS X 版的散發套件。本教學課程已在 OS X 上利用 [Homebrew][Homebrew] 來安裝 **rbenv** 和 **ruby-build** 而完成驗證。在 [][]<https://github.com/sstephenson/rbenv/></a> 可找到安裝資訊。

    -   **Linux** - 使用散發套件封裝管理系統。本教學課程使用 ruby1.9.1 及 ruby1.9.1-dev 封裝，在 Ubuntu 12.10 通過驗證。

    -   **Windows** - Windows 有多個 Ruby 散發套件。本教學課程使用 [RailsInstaller][RailsInstaller] 1.9.3-p392 通過驗證。

2.  開啟新的命令列或終端機工作階段，並輸入下列命令安裝 Ruby on Rails：

        gem install rails --no-rdoc --no-ri

    <div class="dev-callout">
    <strong>注意</strong>
    <p>此命令可能需要某些作業系統的管理員或根權限。如果您執行此命令, try using 'sudo' as follows:</p>
    <pre class="prettyprint">sudo gem install rails</pre>
    </div>

    <div class="dev-callout">
    <strong>注意</strong>
    <p>本教學課程使用 Rails gem 3.2.12 版。</p>

    </div>

3. 您也必須安裝 JavaScript 直譯器，以便 Rails 用來編譯 Rails 應用程式所用的 CoffeeScript 資產。[][1]<https://github.com/sstephenson/execjs#readme></a> 提供支援的直譯器清單。

    本教學課程的驗證期間使用 [Node.js][Node.js]，它適用於 OS X、Linux 和 Windows 作業系統。

## <span id="create"></span></a>建立 Rails 應用程式

1.  從命令列或終端機工作階段中，使用下列命令建立名稱為「blog\_app」的新 Rails 應用程式：

        rails new blog_app

    此命令會建立名稱為 **blog\_app** 的新目錄，並且填入 Rails 應用程式所需的檔案和子目錄。

    <div class="dev-callout">
    <strong>注意</strong>
    <p>此命令可能需要一分鐘以上的時間才能完成。它會以無訊息的方式安裝預設應用程式所需的 gem，在這段期間，可能呈現看似無反應的狀態。</p>
    </div>

2.  將目錄變更為 **blog\_app** 目錄，然後使用下列命令建立基本部落格樣板：

        rails generate scaffold Post name:string title:string content:text

    這將建立貼文到部落格所需的控制器、檢視、模型和資料庫移轉。各篇貼文都將有作者名稱、貼文標題和文字內容。

3.  若要建立將儲存部落格貼文的資料庫，請使用下列命令：

        rake db:migrate

    這將使用 Rails 的預設資料庫提供者，也就是 [SQLite3 Database][SQLite3 Database]。雖然您可能會想要對於生產應用程式使用不同的資料庫，不過 SQLite 對於本教學課程的目的而言已經夠用。

## <span id="test"></span></a>測試應用程式

執行下列步驟在部署環境中啟動 Rails 伺服器

1.  從命令列或終端機工作階段中，使用下列命令啟動 Rails 伺服器：

        rails s

    您應該會看到如下所示的輸出。請注意網頁伺服器接聽的連接埠。在下列範例中，它接聽連接埠 3000。

        => Booting WEBrick
        => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
        => Call with -d to detach
        => Ctrl-C to shutdown server
        [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
        [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
        [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  開啟瀏覽器，導覽至 http://localhost:3000/。您應該會看到如下所示的頁面：

    ![預設 rails 頁面][預設 rails 頁面]

    此頁面是靜態的歡迎畫面。若要檢視由樣板命令產生的表單，請瀏覽到 http://localhost:3000/posts。您應該會看到如下所示的頁面：

    ![列出貼文的頁面][列出貼文的頁面]

    若要停止伺服器程序，請在命令列中輸入 CTRL+C

## <span id="createvm"></span></a>建立 Azure 虛擬機器

按照[此處][此處]提供的指示建立代管 Linux 的 Azure 虛擬機器。

<div class="dev-callout">
<strong>注意</strong>

<p>本教學課程的步驟是在裝載 Ubuntu 12.10 的 Azure 虛擬機器上執行。如果您使用不同的 Linux 散發套件，可能需要不同的步驟才能完成相同的工作。</p></div>

<div class="dev-callout">
<strong>注意</strong>
<p>您<strong>只</strong>需要建立虛擬機器。瞭解如何使用 SSH 連線至虛擬機器後停止。</p>
</div>

建立 Azure 虛擬機器後執行下列步驟，在虛擬機器上安裝 Ruby and Rails：

1.  從命令列或終端機工作階段中，使用下列命令以 SSH 連線至虛擬機器：

        ssh username@vmdns -p port

    取代建立 VM、VM 的 DNS 位址和 SSH 端點的連接埠期間建立的使用者名稱。例如：

        ssh railsdev@railsvm.cloudapp.net -p 61830

    <div class="dev-callout">
<strong>注意</strong>
<p>如果您使用 Windows 做為開發環境，您可以使用 <b>PuTTY</b> for SSH 功能之類的公用程式。PuTTY 可自 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html">PuTTY 下載頁面</a> (英文) 下載取得。</p>
</div>

2.  從 SSH 工作階段中，使用下列命令在 VM 上安裝 Ruby：

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby1.9.1 ruby1.9.1-dev build-essential libsqlite3-dev nodejs -y

    安裝完成後，使用下列命令確認已成功安裝 Ruby：

        ruby -v

    這應該傳回虛擬機器上安裝的 Ruby 版本，可能不是 1.9.1。例如，**ruby 1.9.3p194 (2012-04-20 revision 35410) [x86\_64-linux]**。

3.  使用下列命令安裝搭配程式：

        sudo gem install bundler --no-rdoc --no-ri

    將搭配程式複製到伺服器後，將用來安裝 rails 應用程式要求的 gem。

## <span id="copy"></span></a>將應用程式複製到 VM

從開發環境中，開啟新的命令列或終端機工作階段，並使用 **scp** 將 **blog\_app** 目錄複製到虛擬機器。此命令的格式如下：

    scp -r -P 54822 -C directory-to-copy user@vmdns:

例如：

    scp -r -P 54822 -C ~/blog_app railsdev@railsvm.cloudapp.net:

<div class="dev-callout">
<strong>注意</strong>
<p>如果您使用 Windows 做為開發環境，您可以使用 <b>pscp</b> for scp 功能之類的公用程式。Pscp 可自 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html">PuTTY 下載頁面</a> (英文) 下載取得。</p>
</div>

用於此命令的參數有下列效果：

-   **-r**：不斷將指定目錄和所有子目錄的內容

-   **-P**：使用指定連接埠進行 SSH 通訊

-   **-C**：啟用壓縮

-   **directory-to-copy**：要複製的本機目錄

-   <**user@vmdns**>：將檔案複製到其中及使用者登入所用的機器位址

在複製作業後，**blog\_app** 目錄將位在使用者主目錄中。在虛擬機器的 SSH 工作階段中使用下列命令，檢視已複製的檔案：

    cd ~/blog_app
    ls

傳回的檔案清單應該符合開發環境中的 **blog\_app** 目錄所包含的檔案。

## <span id="start"></span></a>安裝 gem 並啟動 Rails

1.  在虛擬機器上，將目錄變更為 **blog\_app** 目錄，並使用下列命令安裝 **Gemfile** 中指定的 gem：

        sudo bundle install

2.  若要建立資料庫，請使用下列命令：

        rake db:migrate

3.  使用下列命令啟動伺服器：

        rails s

    您應該會看到如下所示的輸出。請注意網頁伺服器接聽的連接埠。在下列範例中，它接聽連接埠 3000。

        => Booting WEBrick
        => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
        => Call with -d to detach
        => Ctrl-C to shutdown server
        [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
        [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
        [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

4.  在瀏覽器中，瀏覽至 [Azure 管理入口網站][Azure 管理入口網站]並選取虛擬機器。

    ![虛擬機器清單][虛擬機器清單]

5.  選取頁面頂端的 [端點]，並按一下頁面底端的 [+ 新增端點]。

    ![端點頁面][端點頁面]

6.  在 [新增端點] 對話方塊中，按一下右下方的箭頭繼續進入第二頁，並在表單中輸入下列資訊：

    -   **NAME**：HTTP

    -   **PROTOCOL**：TCP

    -   **PUBLIC PORT**： 80

    -   **PRIVATE PORT**：\<以上步驟 3 中的資訊\>

    這將建立公用連接埠 80，將流量傳送至 Rails 伺服器接聽的私人連接埠 3000。

    ![新節點對話方塊][新節點對話方塊]

7.  按一下核取記號來儲存端點。

8.  訊息應該會出現，顯示**更新進行中**。這個訊息消失後，端點便在作用中。您現在可以瀏覽至虛擬機器的 DNS 名稱，測試您的應用程式。網站應如下所示：

    ![預設 rails 頁面][2]

    將 **/posts** 附加到 URL 之後應該會顯示樣板命令所產生的頁面。

    ![貼文頁面][顯示列出貼文的瀏覽器]

## <span id="next"></span></a>後續步驟

在本文中，您已經了解如何建立基本表單型 Rails 應用程式並發佈至 Azure 虛擬機器。我們執行的大部分動作都是手動進行，而且，在生產環境中，應該自動進行。另外，大部分生產環境均代管 Rails 應用程式以及 Apache 或 NginX 之類的其他伺服器程序，處理傳送至多個 Rails 應用程式及執行個體並提供靜態資源的要求。

如需 Rails 應用程式自動部署以及使用 Unicorn 網頁伺服器和 NginX 的資訊，請參閱[現代化商務的雲端][現代化商務的雲端]。

如果要深入了解 Ruby on Rails，請參閱 [Ruby on Rails 指南][Ruby on Rails 指南] (英文)。

若要深入了解如何使用 Azure SDK for Ruby 從 Ruby 應用程式存取 Azure，請參閱：

-   [使用 Blob 儲存非結構化資料][使用 Blob 儲存非結構化資料]

-   [使用資料表儲存機碼值組][使用資料表儲存機碼值組]

-   [使用內容傳遞網路提供高頻寬內容][使用內容傳遞網路提供高頻寬內容]

<!-- WA.com links --> 
<!-- External Links --> 
<!-- Images -->

  [顯示列出貼文的瀏覽器]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png
  [設定開發環境]: #setup
  [建立 Rails 應用程式]: #create
  [測試應用程式]: #test
  [建立 Azure 虛擬機器]: #createvm
  [將應用程式複製到 VM]: #copy
  [安裝 gem 並啟動應用程式]: #start
  [後續步驟]: #next
  [Homebrew]: http://brew.sh/
  []: https://github.com/sstephenson/rbenv/
  [RailsInstaller]: http://railsinstaller.org/
  [1]: https://github.com/sstephenson/execjs#readme
  [Node.js]: http://nodejs.org/
  [SQLite3 Database]: http://www.sqlite.org/
  [預設 rails 頁面]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailslocal.png
  [列出貼文的頁面]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilslocal.png
  [此處]: /zh-tw/documentation/articles/virtual-machines-linux-tutorial
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [虛擬機器清單]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png
  [端點頁面]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png
  [新節點對話方塊]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png
  [2]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png
  [現代化商務的雲端]: /zh-tw/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/
  [Ruby on Rails 指南]: http://guides.rubyonrails.org/
  [使用 Blob 儲存非結構化資料]: /zh-tw/documentation/articles/storage-ruby-how-to-use-blob-storage
  [使用資料表儲存機碼值組]: /zh-tw/develop/ruby/how-to-guides/table-service/
  [使用內容傳遞網路提供高頻寬內容]: /zh-tw/develop/ruby/app-services/
