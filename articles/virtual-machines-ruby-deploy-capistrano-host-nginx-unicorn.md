<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Deploying a Ruby on Rails Web application to an Azure Virtual Machine using Capistrano - tutorial" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Learn how to deploy a Ruby on Rails application to an Azure Virtual Machine using Capistrano, Unicorn and Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Deploy a Ruby on Rails Web application to an Azure VM using Capistrano" authors="" />

使用 Capistrano 將 Ruby on Rails Web 應用程式部署至 Azure VM
============================================================

本教學課程說明如何使用 [Capistrano](https://github.com/capistrano/capistrano/) 將 Ruby on Rails 架構的網站部署到 Azure 虛擬機器。本教學課程也說明如何使用 [Nginx](http://nginx.org/) 和 [Unicorn](https://github.com/blog/517-unicorn) 將應用程式裝載到虛擬機器上。

本教學課程假設您先前沒有使用 Azure 的經驗。完成本教學課程時，您將有一個在雲端中啟動並執行的 Ruby on Rails 架構的應用程式。

您將了解如何：

-   設定開發環境

-   安裝 Ruby 和 Ruby on Rails

-   安裝和設定 Nginx 與 Unicorn

-   建立新的 Rails 應用程式

-   使用 Capistrano 將 Rails 應用程式部署到 Azure 虛擬機器

以下是完成之應用程式的螢幕擷取畫面：

![a browser displaying Listing Posts](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png)

**注意**

本教學課程使用的應用程式包含原生二進位元件。因此，如果開發環境不是 Linux 架構，可能會發生問題，因為開發電腦上產生的 Gemfile.lock 可能不含 Linux 相容版本的必要 Gem 項目。

使用 Windows 開發環境時需要有特定的步驟，因為這與目標部署環境之間存在極大的差別。不過，如果您在部署期間或之後遭遇到本文的步驟所未涵蓋的錯誤，您可以從 Linux 架構的開發環境來試著執行本文的步驟。

本文內容
--------

-   [設定開發環境](#setup)

-   [建立 Rails 應用程式](#create)

-   [測試應用程式](#test)

-   [建立原始碼儲存機制](#repository)

-   [建立 Azure 虛擬機器](#createvm)

-   [測試 Nginx](#nginx)

-   [準備部署](#capify)

-   [部署](#deploy)

-   [後續步驟](#next)

設定開發環境
------------

1.  將 Ruby 安裝在開發環境。視作業系統而定，步驟可能有所不同。

    -   **Apple OS X** - Ruby 有許多 OS X 版的散發套件。本教學課程已在 OS X 上利用 [Homebrew](http://brew.sh/) 來安裝 **rbenv** 和 **ruby-build** 而完成驗證。如需相關安裝資訊，請參閱 <https://github.com/sstephenson/rbenv/>。

    -   **Linux** - 使用散發套件管理系統。本教學課程已在 Ubuntu 12.10 上利用 ruby1.9.1 和 ruby1.9.1-dev 套件來完成驗證。

    -   **Windows** - Ruby 有許多 Windows 版散發套件。本教學課程已利用 [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392 而完成驗證。

2.  開啟新的命令列或終端機工作階段，輸入下列命令來安裝 Ruby on Rails：

         gem install rails --no-rdoc --no-ri

    **注意**

    在某些作業系統上，此命令可能需要系統管理員或 root 權限。如果執行此命令時發生錯誤，請試著使用 'sudo'，如下所示：

    ``` {.prettyprint}
    sudo gem install rails
    ```

    **注意**

    本教學課程使用 Rails gem 3.2.12 版。

3.  您也必須安裝 JavaScript 解譯器，Rails 會使用此解譯器來編譯 Rails 應用程式所使用的 CoffeeScript 資產。關於支援的解譯器清單，請參閱 <https://github.com/sstephenson/execjs#readme>。

    本教學課程驗證期間使用 [Node.js](http://nodejs.org/)，因為它適用於 OS X、Linux 和 Windows 作業系統。

建立 Rails 應用程式
-------------------

1.  從命令列或終端機工作階段，使用下列命令建立新的 Rails 應用程式，命名為 "blog\_app"：

         rails new blog_app

    此命令會建立名為 **blog\_app** 的新目錄，並在此目錄中加入 Rails 應用程式所需的檔案和子目錄。

    **注意**

    此命令需要一分鐘以上才能完成。它會以無訊息方式安裝預設應用程式所需的 gem，此期間可能看似停止回應。

2.  切換至 **blog\_app** 目錄，然後使用下列命令來建立基本的部落格樣板：

         rails generate scaffold Post name:string title:string content:text

    這會建立控制器、檢視、模型和資料庫移轉，用以保存部落格的文章。每篇文章都有作者名稱、文章標題和文字內容。

3.  若要建立資料庫來儲存部落格文章，請使用下列命令：

         rake db:migrate

    這會讓 Rails 使用預設的資料庫提供者，也就是 [SQLite3 資料庫](http://www.sqlite.org/)。對於實際的應用程式，您可能想要使用不同的資料庫，但 SQLite 已足供本教學課程所需。

測試應用程式
------------

在開發環境中執行下列步驟來啟動 Rails 伺服器

1.  切換至 **blog\_app** 目錄 (若還不在此目錄中)，使用下列命令來啟動 Rails 伺服器：

         rails s

    您應該會看到如下所示的輸出。請注意 Web 伺服器所接聽的通訊埠。在下列範例中，它在通訊埠 3000 上接聽。

         => Booting WEBrick
        => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
        => Call with -d to detach
        => Ctrl-C to shutdown server
        [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
        [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
        [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start:pid=9789 port=3000

2.  開啟瀏覽器，瀏覽至 http://localhost:3000/。您應該會看到如下所示的頁面：

    ![default rails page](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png)

    這是靜態的歡迎使用頁面。若要查看樣板命令所產生的表單，請瀏覽至 http://localhost:3000/posts。您應該會看到如下所示的頁面：

    ![a page listing posts](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png)

    若要停止伺服器處理程序，請在命令列按 CTRL+C

建立原始碼儲存機制
------------------

在本教學課程中，您將會使用 [Git](http://git-scm.com/) 和 [GitHub](https://github.com/) 進行版本控制並做為程式碼的集中位置。

1.  在 [GitHub](https://github.com/) 上建立新的儲存機制。如果您目前沒有 GitHub 帳戶，您可以註冊免費帳戶。本教學課程的步驟假設儲存機制名稱為 **blog\_app**。

    **注意**

    本文稍後各節建立的指令碼將包含虛擬機器的位址，以及用來透過 SSH 部署應用程式的使用者名稱。因此，建議您盡可能使用專用的 GitHub 儲存機制。

2.  從命令提示字元中，切換至 **blog\_app** 目錄並執行下列命令，將應用程式的初始版本上傳至 GitHub 儲存機制：

         git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
        git push -u origin master

建立 Azure 虛擬機器
-------------------

依照[這裡](/en-us/manage/linux/tutorials/virtual-machine-from-gallery/)提供的指示，建立一個裝載 Linux 的 Azure 虛擬機器。

**注意**

本教學課程的步驟是在裝載 Ubuntu 12.10 的 Azure 虛擬機器上執行。如果您使用不同的 Linux 散發套件，可能需要以不同的步驟來完成相同的工作。

**注意**

您**只**需要建立虛擬機器。了解如何使用 SSH 來連線至虛擬機器即可。

使用 SSH 來建立 Azure 虛擬機器之後，執行下列步驟將 Ruby 和 Rails 安裝在虛擬機器上：

1.  使用 SSH 來連線至虛擬機器，然後使用下列命令來更新現有的套件並安裝 Ruby 環境：

         sudo apt-get -y update
        sudo apt-get -y upgrade
        sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

    安裝完成之後，使用下列命令來驗證 Ruby 安裝成功：

         ruby -v

    這應該傳回虛擬機器上安裝的 Ruby 版本，可能不是 1.9.1。例如，**ruby 1.9.3p194 (2012-04-20 revision 35410) [x86\_64-linux]**。

2.  使用下列命令來安裝 Bundler：

         sudo gem install bundler

    Bundler 複製到伺服器之後，將用來安裝 Rails 應用程式所需的 Gem。

開啟通訊埠 80 和測試 Nginx
--------------------------

Nginx 提供預設網站，可用來確定虛擬機器能夠接受 Web 流量。請執行下列步驟來允許流量通過通訊埠 80 並測試預設的 Nginx 網站。

1.  從連至 VM 的 SSH 工作階段中，啟動 Nginx 服務：

        sudo service nginx start

    這會啟動 Nginx 服務在通訊埠 80 上接聽連入的流量。

2.  在瀏覽器中，瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com/)，並選取您的虛擬機器。

    ![virtual machine list](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png)

3.  在頁面頂端選取 **[端點]**，然後在頁面底部按一下 **[+ 加入端點]**。

    ![endpoints page](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png)

4.  在 **[加入端點]** 對話方塊中，按一下左下方的箭頭進入第二頁，然後在表單中輸入下列資訊：

    -   **名稱**：HTTP

    -   **通訊協定**：TCP

    -   **公用連接埠**： 80

    -   **私用連接埠**： 80

    這會建立公用連接埠 80，將流量路由傳送至 Nginx 接聽的私用連接埠 80。

    ![new endpoint dialog](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png)

5.  按一下核取記號以儲存端點。

6.  應該會出現 **[更新進行中]** 訊息。此訊息消失後，就表示端點在作用中。現在，您可以瀏覽至虛擬機器的 DNS 名稱來測試應用程式。網站的外貌如下所示：

    ![nginx welcome page](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png)

7.  使用下列命令來停止並移除預設的 Nginx 網站：

        sudo service nginx stop
        sudo rm /etc/nginx/sites-enabled/default

    本教學課程稍後執行的部署指令碼會將 blog\_app 設為由 Nginx 維護的預設網站。

準備部署
--------

在本節中，您將修改應用程式來使用 Unicorn Web 伺服器、啟用 Capistrano 進行部署、允許從虛擬機器存取 GitHub，以及建立指令碼來部署和啟動應用程式。

1.  執行＜[產生 SSH 金鑰](https://help.github.com/articles/generating-ssh-keys#platform-all)＞(英文) 頁面所述的步驟，授權虛擬機器利用憑證來驗證您的 GitHub 帳戶。這將用於從部署指令碼中存取 GitHub 儲存機制。

    **注意**

    雖然必須在虛擬機器上產生 SSH 金鑰，但在開發環境中，您可以使用瀏覽器將金鑰加入至 GitHub 帳戶。

    若要檢視 SSH 憑證的內容，以便複製並貼到 GitHub，請使用下列命令：

    ``` {}
    cat ~/.ssh/id_rsa.pub
    ```

2.  在開發電腦上，修改 **Gemfile**，移除 **Capistrano** 和 **Unicorn** 兩行開頭的 '\#' 字元以取消註解：

         # gem 'unicorn'
        # gem 'capistrano'

    **注意**

    Unicorn 無法在 Windows 上使用。如果您使用 Windows 做為開發環境，請修改 **Gemfile**，以確定只有在部署至 VM 時才會嘗試安裝 Unicorn：

    ``` {.prettyprint}
    platforms :ruby do  gem 'unicorn'end
    ```

3.  執行下列命令來為專案安裝新的 Gem 並設定 Capistrano：

        bundle
        capify .

4.  將下列檔案加入至 **blog\_app/config** 目錄，並將以下連結所示的內容填入每一個檔案中：

    -   [nginx.conf](https://gist.github.com/cff582f4f970a95991e9) - 設定 Nginx 來搭配 Unicorn 一起使用，並提供 Rails 應用程式隨附的靜態檔案
    -   [unicorn\_init.sh](https://gist.github.com/3272994) - 用來啟動 Unicorn 伺服器處理程序的 Shell 指令碼
    -   [unicorn.rb](https://gist.github.com/3273014) - Unicorn 的組態檔

    在每一個檔案中，將 **blogger** 這個字換成用來登入虛擬機器的使用者名稱。這是用來部署應用程式的使用者。

5.  在 **blog\_app/config** 目錄中，編輯 **deploy.rb** 檔案，將現有的內容改為下列內容：

        require "bundler/capistrano"

        set :application, "blog_app"
        set :user, "blogger"

        set :scm, :git
        set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
        set :branch, "master"
        set :use_sudo, true

        server "VMDNSName", :web, :app, :db, primary:true

        set :deploy_to, "/home/#{user}/apps/#{application}"
        default_run_options[:pty] = true
        ssh_options[:forward_agent] = true
        ssh_options[:port] = SSHPort

        namespace :deploy do
        desc "Fix permissions"
        task :fix_permissions, :roles => [ :app, :db, :web ] do
            run "chmod +x #{release_path}/config/unicorn_init.sh"
        end

        %w[start stop restart].each do |command|
        desc "#{command} unicorn server"
        task command, roles::app, except:{no_release:true} do
        run "service unicorn_#{application} #{command}"
        end
        end

        task :setup_config, roles::app do
        sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
        sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
        sudo "mkdir -p #{shared_path}/config"
        end
        after "deploy:setup", "deploy:setup_config"

        task :symlink_config, roles::app do
        # Add database config here
        end
        after "deploy:finalize_update", "deploy:fix_permissions"
        after "deploy:finalize_update", "deploy:symlink_config"
        end

    在以上文字中，取代下列參數：

    -   **YourGitHubAccount** 改為 GitHub 帳戶名稱
    -   **VMDNSName** 改為 Azure 虛擬機器的 DNS
    -   **blogger** 改為用來登入虛擬機器的使用者名稱
    -   **SSHPort** 改為虛擬機器的外部 SSH 通訊埠

    **注意**

    如果開發環境是 Windows 系統，您必須將下一行加入至 **deploy.rb** 檔案。這應該與其他 **set** 陳述式一起放在檔案開頭：

    ``` {}
    set :bundle_flags, "--no-deployment --quiet"
    ```

    這不是最佳做法，因為會造成部署期間從 Gemfile 載入 Gem，而不是 Gemfile.lock，但這卻有必要，因為目標系統 (Linux) 與開發系統 (Windows) 不同。

6.  在 **blog\_app** 目錄中的 **Capfile** 中，取消註解資產行。

        load 'deploy/assets'

7.  執行下列命令會將變更認可到專案，並上傳至 GitHub。

         git add .
        git commit -m "adding config files"
        git push

部署
----

1.  從本機開發電腦中，使用下列命令設定遠端 Azure VM 來進行部署。

        cap deploy:setup

    出現提示時，輸入虛擬機器使用者帳戶的密碼。Capistrano 會連線至 VM，並於使用者帳戶的主目錄下建立 **apps** 目錄。

2.  在 Azure VM 的 SSH 連線中，使用下列命令來修改 **app** 目錄的權限：

        sudo chmod -R 777 apps

    **注意**

    不建議用於生產環境，在此使用只是為了示範。

3.  在開發環境上，使用下列命令執行嚴酷部署。這會將應用程式部署至虛擬機器並啟動 Unicorn 服務。

        cap deploy:cold

4.  啟動 Nginx 服務，這時應該會開始將流量路由傳送至 Unicorn 並提供靜態內容：

        sudo service nginx start

現在，Ruby on Rails 應用程式應該已在 Azure 虛擬機器上執行。若要驗證這一點，請在網頁瀏覽器中輸入虛擬機器的 DNS 名稱。例如，http://railsvm.cloudapp.net。應該會出現 'Welcome aboard' 畫面：

![welcome aboard page](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png)

如果在 URL 後面附加 '/posts'，則應該會出現文章索引，且您應該能夠建立、編輯和刪除文章。

後續步驟
--------

在本文中，您學到如何使用 Capistrano 建立基本的表單型 Rails 應用程式，並發佈至 Azure 虛擬機器。虛擬機器使用 Unicorn 和 Nginx 來處理應用程式的 Web 要求。

如需有關建立 Rails 應用程式並只使用 SSH 來部署至 Azure VM 的另一個基本範例，請參閱＜[使用 Linux 虛擬機器來裝載 Ruby on Rails Web 應用程式](/en-us/develop/ruby/tutorials/web-app-with-linux-vm/)＞。

如需深入了解 Ruby on Rails，請參閱 [Ruby on Rails Guides](http://guides.rubyonrails.org/)。

若要了解如何從 Ruby 應用程式使用 Azure SDK for Ruby 來存取 Azure 服務，請參閱：

-   [使用 Blob 來儲存非結構化資料](/en-us/develop/ruby/how-to-guides/blob-storage/)

-   [使用資料表來儲存機碼值組](/en-us/develop/ruby/how-to-guides/table-service/)

-   [透過內容傳遞網路提供高頻寬內容](/en-us/develop/ruby/app-services/)


