<properties pageTitle="使用 Capistrano 將 Ruby on Rails Web 應用程式部署至 Azure 虛擬機器 - 教學課程" description="了解如何使用 Capistrano、Unicorn 和 Nginx，將 Ruby on Rails 應用程式部署至 Azure 虛擬機器。" authors="blackmist" manager="wpickett" editor="" services="virtual-machines" documentationCenter=""/>

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="ruby" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"/>


# 使用 Capistrano 將 Ruby on Rails Web 應用程式部署至 Azure VM

本教學課程說明如何使用 [Capistrano 3](https://github.com/capistrano/capistrano/) 將 Ruby on Rails 網站部署到 Azure 虛擬機器。部署之後，將使用 [Nginx](http://nginx.org/) 和 [Unicorn](https://github.com/blog/517-unicorn) 來主控網站。[PostgreSQL](https://www.postgresql.org) 會儲存已部署之應用程式的應用程式資料。

本教學課程假設您未曾使用過 Azure，但熟悉 Ruby、Rails、Git 和 Linux。完成本教學課程後，您將在雲端啟動並執行 Ruby on Rails 型應用程式。

您將了解如何：

* 設定開發和實際執行環境

* 安裝 Ruby 和 Ruby on Rails

* 安裝 Unicorn、Nginx 和 PostgreSQL

* 建立新 Rails 應用程式

* 建立 Capistrano 部署，並用它來部署應用程式

以下是完成後應用程式的螢幕擷取畫面：

![a browser displaying Listing Posts][blog-rails-cloud]

> [AZURE.NOTE] 本教學課程使用的應用程式包含原生二進位元件。如果您的開發環境不是 Linux 架構，則在部署至 VM 時可能會發生錯誤。在部署期間使用的 Gemfile.lock 檔案會包含平台特定 gem，其中可能不含 VM 所需的原生 Linux 版本 gem 的項目。
> 
> 本文列舉了使用 Windows 開發環境的特定步驟。不過，如果您在部署期間或之後遭遇到本文所未涵蓋的錯誤，您可以從 Linux 架構的開發環境重新嘗試本文的步驟。

## 本文內容

* [設定開發環境](#setup)

* [建立 Rails 應用程式](#create)

* [測試應用程式](#test)

* [建立原始碼儲存機制](#repository)

* [建立 Azure 虛擬機器](#createvm)

* [測試 Nginx](#nginx)

* [準備部署](#capify)

* [部署](#deploy)

* [後續步驟](#next)

## <a id="setup"></a>設定開發環境

1. 在開發環境中安裝 Ruby。端視您的作業系統而定，步驟可能不同。

	* **Apple OS X** - 有多個適用於 OS X 的 Ruby 發佈。本教學課程已在 OS X 上使用 [Homebrew](http://brew.sh/) 通過驗證，以安裝 **rbenv**、**ruby-build** 和 **Ruby 2.0.0-p451**。在 [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/) 可找到安裝資訊。

	* **Linux** - 使用您的發佈套件管理系統。本教學課程已使用 **rbenv**、**ruby-build** 和 **Ruby 2.0.0-p451**，在 Ubuntu 12.10 上完成驗證。

	* **Windows** - Windows 有多個 Ruby 發佈套件。本教學課程已使用 [RubyInstaller](http://RubyInstaller.org/) 通過驗證，以安裝 **Ruby 2.0.0-p451**。命令是使用 [Git for Windows](http://git-scm.com/download/win) 隨附的 **GitBash** 命令列而發出的。

2. 開啟新的命令列或終端機工作階段，並輸入下列命令安裝 Ruby on Rails：

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] 在某些作業系統上，此命令可能需要系統管理員或根權限。如果執行此命令時接收到錯誤，請嘗試使用  'sudo'，如下所示。
	> 
	> `sudo gem install rails`

	> [AZURE.NOTE] 本教學課程使用 Rails gem 4.0.4 版。

3. 您也必須安裝 JavaScript 直譯器，以便 Rails 用來編譯 Rails 應用程式所用的 CoffeeScript 資產。[https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme) 提供支援的直譯器清單。
	
	> [AZURE.NOTE] 本教學課程使用[Node.js](http://nodejs.org/)，它適用於 OS X、Linux 和 Windows 作業系統。

## <a id="create"></a>建立 Rails 應用程式

1. 從命令列或終端機工作階段中，使用下列命令建立名稱為「blog_app」的新 Rails 應用程式：

		rails new blog_app

	此命令會建立名稱為 **blog_app** 的新目錄，並且填入 Rails 應用程式所需的檔案和子目錄。

	> [AZURE.NOTE] 此命令可能需要一分鐘以上的時間才能完成。它會以無訊息方式安裝預設應用程式所需的 gem，在此期間將沒有回應。

2. 切換至 **blog_app** 目錄，然後使用下列命令來建立基本的部落格樣板：

		rails generate scaffold Post name:string title:string content:text

	這將建立貼文到部落格所需的控制器、檢視、模型和資料庫移轉。各篇貼文都將有作者名稱、貼文標題和文字內容。

3. 若要建立將儲存部落格貼文的資料庫，請使用下列命令：

		rake db:migrate

	這會使用 Rails 的預設資料庫提供者 (也就是 [SQLite3 資料庫])，建立用以儲存貼文的資料庫結構描述[sqlite3]。

4. 若要將  貼文的索引顯示為首頁，請修改 **config/routes.rb** 檔案，並在  `resources :posts` 資料行後新增以下項目。

		root 'posts#index'

	這會在使用者瀏覽網站時顯示貼文清單。

## <a id="test"></a>測試應用程式

1. 切換至 **blog_app** 目錄 (若您還不在此目錄中)，使用下列命令來啟動 Rails 伺服器。

		rails s

	您應該會看到如下所示的輸出。請注意網頁伺服器接聽的連接埠。在下列範例中，它接聽連接埠 3000。

		=> Booting WEBrick
		=> Rails 4.0.4 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. 開啟瀏覽器，並瀏覽到 http://localhost:3000/。您應該會看到如下的頁面。

	![a page listing posts][blog-rails]

	若要停止伺服器程序，請在命令列中輸入 CTRL+C

## <a id="repository"></a>建立原始碼儲存機制

使用 Capistrano 部署應用程式時，會從儲存機制提取檔案。在本教學課程中，我們會使用 [Git](http://git-scm.com/) 進行版本控制，並使用 [GitHub](https://github.com/) 做為儲存機制。

1.	在 [GitHub](https://github.com/) 上建立新的儲存機制。如果您沒有 GitHub 帳戶，您可以註冊免費帳戶。下列步驟假設儲存機制名稱為 **blog_app**。

	> [AZURE.NOTE] 若要支援應用程式的自動部署，您應使用 SSH 金鑰向 GitHub 驗證。如需詳細資訊，請參閱[產生 SSH 金鑰](https://help.github.com/articles/generating-ssh-keys)上的 GitHub 文件。

2.	從命令提示字元中，切換至 **blog_app** 目錄並執行下列命令，將應用程式上傳至您的 GitHub 儲存機制。以您的 GitHub 帳戶名稱取代 **YourGitHubName**。

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin git@github.com:YourGitHubName/blog-azure.git
		git push -u origin master

在下一節中，我們會建立此應用程式所將部署到的虛擬機器。

## <a id="createvm"></a>建立 Azure 虛擬機器

按照[此處][vm-instructions]提供的指示建立代管 Linux 的 Azure 虛擬機器。

1. 登入 Azure [管理入口網站][management-portal]。在命令列上選取 [新增]。

2. 選取 [虛擬機器]，然後選取 [從組件庫]。

3. 在 [選擇映像] 中選取 [Ubuntu]，然後選取版本 [12.04 LTS]。選取箭頭以繼續。

	> [AZURE.NOTE] 本教學課程的步驟是在主控 Ubuntu 12.04 LTS 的 Azure 虛擬機器上執行的。如果您使用不同的 Linux 散發套件，可能需要不同的步驟才能完成相同的工作。

4. 在 [虛擬機器名稱] 中，輸入您要使用的名稱。此名稱會用來建立這個虛擬機器的網域名稱。

5. 在 [新使用者名稱] 中，輸入此機器的系統管理員帳戶名稱。

	> [AZURE.NOTE] 在本教學課程中，系統管理員帳戶也將用來部署應用程式。如需為部署建立個別帳戶的相關資訊，請參閱 [Capistrano][capistrano] 文件。

6. 在 [驗證] 下，勾選 [Upload compatible SSH key for authentication]，然後瀏覽並選取包含憑證的 **.pem** 檔案。最後，選取箭頭以繼續。

	> [AZURE.NOTE] 如果您不清楚如何產生或使用 SSH 金鑰，請參閱[如何在 Azure 上搭配使用 SSH 與 Linux][ssh-on-azure]，以取得建立 SSH 金鑰的指示。
	> 
	> 您也可以啟用密碼驗證，但必須同時提供 SSH 金鑰，因為將部署自動化時會用到此金鑰。

7. 在 [端點] 下，使用 [輸入或選取值] 下拉式清單選取 [HTTP]。此頁面上的其他欄位可保留為預設值。請記下 [雲端服務 DNS 名稱]，因為後續步驟會用到此值。最後，選取箭頭以繼續。

8. 選取最後一頁上的核取記號，以建立虛擬機器。

### 安裝 Git、Ruby 和 Nginx

虛擬機器建立後，請使用 SSH 加以連接，然後使用下列命令比較 Ruby 應用程式的主控環境。

	sudo apt-get update
	sudo apt-get -y upgrade
	sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
	git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
	echo 'export RBENV_ROOT=~/.rbenv' >> ~/.bash_profile
	echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> ~/.bash_profile
	echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
	source ~/.bash_profile
	eval "$(rbenv init -)"
	git clone git://github.com/sstephenson/ruby-build.git
	cd ruby-build
	sudo ./install.sh
	~/.rbenv/bin/rbenv install 2.0.0-p451
	~/.rbenv/bin/rbenv global 2.0.0-p451
	gem install bundler
	~/.rbenv/bin/rbenv rehash

> [AZURE.NOTE] 您可以將前述項目儲存到指令碼 (.sh 檔案) 中，以避免在執行命令時發生輸入錯誤。


> [AZURE.NOTE] **~/.rbenv/bin/rbenv install 2.0.0-p451** 命令可能需要幾分鐘才能完成。

**rbenv-install.sh** 指令碼會執行下列動作：
	
* 更新及升級目前安裝的封裝
* 安裝建置工具
* 安裝 Git
* 安裝 Node.js
* 安裝 Nginx
* 安裝建置工具和 Ruby 與 Rails 所需的其他公用程式
* 設定 [rbenv] 的本機 (使用者) 部署
* 安裝 Ruby 2.0.0-p451
* 安裝 [Bundler] gem

安裝完成後，使用下列命令確認已成功安裝 Ruby：

	ruby -v

這應該會傳回 `ruby 2.0.0p451` 做為版本。

### 安裝 PostgreSQL

Rails 用於開發的預設資料庫是 SQLite。您在實際執行環境中通常會使用其他資料庫。下列兩個步驟會在虛擬機器上安裝 PostgreSQL，然後建立使用者和資料庫。後續步驟會設定 Rails 應用程式，以在部署期間使用 PostgreSQL。

1. 使用下列命令安裝 PostgreSQL 和開發位元。

		sudo apt-get -y install postgresql postgresql-contrib libpq-dev

6. 使用下列命令，為您的登入建立使用者和資料庫。以您的登入名稱取代 **my_username** 和 **my_database**。例如，如果您 VM 的登入為 **deploy**，請以 **deploy** 取代 **my_username** 和 **my_database**。

		sudo -u postgres createuser -D -A -P my_username
		sudo -u postgres createdb -O my_username my_database

	> [AZURE.NOTE] 請同時將使用者名稱用於資料庫名稱。此應用程式所使用的 capistrano-postgresql gem 有此需求。

	出現提示時，請輸入使用者的密碼。出現允許使用者建立新角色的提示時，請選取 [y]，因為在部署期間將會使用這個使用者來建立您的 Rails 應用程式所將使用的資料庫和登入。

7. 若要驗證您可以使用該使用者帳戶連接到資料庫，請使用下列命令。請以先前使用的值取代 **my_username** 和 **my_database**。

		psql -U my_username -W my_database

	您應會看見  `database=>` 提示。若要結束 psql 公用程式，請在提示下輸入  `\q`。

### <a id="nginx"></a>測試 Nginx

在虛擬機器的建立期間已加入之 HTTP 端點，可讓它接受連接埠 80 的 HTTP 要求。若要進行確認，請使用下列步驟，確認您可以存取由 Nginx 所建立的預設網站。

2.	從連至 VM 的 SSH 工作階段中，啟動 Nginx 服務：

		sudo service nginx start

	這會啟動 Nginx 服務在通訊埠 80 上接聽連入的流量。

6. 導覽至虛擬機器的 DNS 名稱，以測試您的應用程式。網站應如下所示：

	![nginx welcome page][nginx-welcome]

	> [AZURE.NOTE] 本教學課程稍後執行的部署指令碼會將 blog_app 設為由 Nginx 維護的預設網站。

此時，您已有 Azure 虛擬機器，具有 Ruby、Nginx 和 PostgreSQL，並已可供部署之用。在下一節中，您將修改 Rails 應用程式，以新增用來執行部署的指令碼和資訊。

## <a id="capify"></a>準備部署

在您的開發環境中修改應用程式，以使用 Unicorn Web 伺服器和 PostgreSQL、啟用 Capistrano 以進行部署，以及建立用來部署和啟動應用程式的指令碼。

1. 在您的開發機器上修改 **Gemfile**，並新增以下幾行，將 Unicorn、PostgreSQL、Capistrano 的 gem 和相關的 gem 新增至您的 Rails 應用程式

		# Use Unicorn
		gem 'unicorn'
		# Use PostgreSQL
		gem 'pg', group: :production

		group :development do
		  # Use Capistrano for deployment
		  gem 'capistrano', '~> 3.1'
		  gem 'capistrano-rails', '~> 1.1.1'
		  gem 'capistrano-bundler'
		  gem 'capistrano-rbenv', '~> 2.0'
		  gem 'capistrano-unicorn-nginx', '~> 2.0'
		  gem 'capistrano-postgresql', '~> 3.0'
		end

	> [AZURE.NOTE] Unicorn 無法在 Windows 上使用。如果您使用 Windows 做為開發環境，請在指定 unicorn gem 時使用下列程式碼修改 __Gemfile__，以確定只有在部署至 VM 時才會嘗試安裝 Unicorn。
	> 
	> `platforms :ruby do`
	> `  gem 'unicorn'`
	> `end`

	大部分的 capistraon-* gem 都是協助程式，且會使用我們在實際執行伺服器 (rbenv) 或架構 (rails) 上使用的特定項目。

	capistrano-unicorn-nginx gem 會自動建立可在部署期間用於 Unicorn 和 Nginx 的指令碼，因此我們無須手動加以建立。capistrano-postgresql 會自動在您應用程式的 PostgreSQL 中產生資料庫、使用者和密碼。當您無須使用這些項目時，這兩個 gem 都可大幅簡化部署程序。
 
5.	使用下列命令安裝新的 gem。
		
		bundle

6. 使用下列命令建立 Capistrano 所使用的預設部署檔案。

		cap install

	 `cap install` 命令完成後，下列檔案和目錄即會新增至您的應用程式。

		├── Capfile
		├── config
		│   ├── deploy
		│   │   ├── production.rb
		│   │   └── staging.rb
		│   └── deploy.rb
		└── lib
		    └── capistrano
		            └── tasks

	**deploy.rb** 檔案會提供所有部署類型的通用組態和動作，而 **production.rb** 和 **staging.rb** 則包含實際執行和預備部署所需的其他組態。

	**capistrano** 資料夾包含做為部署程序之一的工作和其他檔案。

5. 在您應用程式的根目錄中編輯 **Capfile**，並移除行首的 __#__ 字元，將以下幾行取消註解。

		require 'capistrano/rbenv'
		require 'capistrano/bundler'
		require 'capistrano/rails/assets'
		require 'capistrano/rails/migrations'

	將以上幾行取消註解後，請新增以下幾行。

		require 'capistrano/unicorn_nginx'
		require 'capistrano/postgresql'

	這些行會指示 Capistrano 使用先前新增至 Gemfile 的 gem。

	完成以上變更後，請儲存檔案。

6.  編輯 **config/deploy.rb** 檔案，並以下列項目取代檔案內容。以您的應用程式名稱取代 **YourApplicationName**，並以此專案之 GitHub 儲存機制的 URL 取代 **https://github.com/YourGitHubName/YourRepoName.git**。

		lock '3.1.0'
		# application name and the github repository
		set :application, 'YourApplicationName'
		set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'
		
		# describe the rbenv environment we are deploying into
		set :rbenv_type, :user
		set :rbenv_ruby, '2.0.0-p451'
		set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
		set :rbenv_map_bins, %w{rake gem bundle ruby rails}
		
		# dirs we want symlinked to the shared folder
		# during deployment
		set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}
		
		namespace :deploy do

		  desc 'Restart application'
		  task :restart do
		    on roles(:app), in: :sequence, wait: 5 do
		      # Your restart mechanism here, for example:
		      # execute :touch, release_path.join('tmp/restart.txt')
              #
			  # The capistrano-unicorn-nginx gem handles all this
			  # for this example
		    end
		  end
		
		  after :publishing, :restart
		
		  after :restart, :clear_cache do
		    on roles(:web), in: :groups, limit: 3, wait: 10 do
		      # Here we can do anything such as:
		      # within release_path do
		      #   execute :rake, 'cache:clear'
		      # end
		    end
		  end
		
		end

	此檔案會提供所有部署類型通用的一般資訊和工作。

5.  編輯 **config/deploy/production.rb** 檔案，並以下列項目取代現有內容。以您 VM 的網域名稱取代 **YourVm.cloudapp.net**。以先前為您的 Azure VM 建立的登入帳戶取代 **YourAzureVMUserName**。

		# production deployment
		set :stage, :production
		# use the master branch of the repository
		set :branch, "master"
		# the user login on the remote server
		# used to connect and deploy
		set :deploy_user, "YourAzureVMUserName"
		# the 'full name' of the application
		set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
		# the server(s) to deploy to
		server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
		# the path to deploy to
		set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
		set :rails_env, :production

	此檔案會提供實際執行部署的特定資訊。

8.	執行下列命令以認可您在先前的步驟中修改的檔案變更，然後將變更上傳至 GitHub。

		git add .
		git commit -m "adding config files"
		git push

應用程式現在應已可供部署之用。

> [AZURE.NOTE] 如果是較複雜的應用程式，或是不同的資料庫或應用程式伺服器，您可能需要其他組態或部署指令碼。

## <a id="deploy"></a>部署

2.	從本機開發機器中，使用下列命令將應用程式所使用的組態檔部署至 VM。

		bundle exec cap production setup

	Capistrano 會使用 SSH 連接到 VM，然後建立應用程式所將部署到的目錄 (~/apps)。如果這是第一次部署，則 capistrano-postgresql gem 也會在伺服器的 PostgreSQL 中建立角色和資料庫。此外也會建立 Rails 將用來連接到資料庫的 database.yml 組態檔。

	> [AZURE.NOTE] 如果在部署時發生「從驗證通訊端讀取回應長度時發生錯誤」 的錯誤，您可能必須使用  `ssh-agent` 命令在您的開發環境啟動 SSH 代理程式。例如，將 `eval $(ssh-agent)` 加入到您的 ~/.bash\_profile 檔案。
	> 
	> 您還可能必須使用  `ssh-add` 命令將 SSH 金鑰新增到代理程式快取中。

4.	使用下列命令執行實際執行部署。這會將應用程式部署至虛擬機器、啟動 Unicorn 服務，然後把 Nginx 設定成將流量路由傳送至 Unicorn。

		bundle exec cap production deploy

	此命令會將應用程式部署至 VM、安裝必要的 gem，然後啟動/重新啟動 Unicorn 和 Nginx。

	> [AZURE.NOTE] 此程序在處理期間會暫停數分鐘。

	> [AZURE.NOTE] 部署的某些部分可能會傳回「結束狀態 1 (失敗)」。一般而言，只要部署順利完成，就可以忽略前述情況。

	> [AZURE.NOTE] 在某些系統上，對 GitHub 進行驗證時，可能會發生 SSH 代理程式無法將認證轉送至遠端 VM 的情況。如果發生此情況，您可以修改 **config/deploy.rb** 檔案以解決此錯誤，並變更  `set :repo_url` 程式碼行以便在存取 Github 時使用 HTTPS。使用 HTTPS 時，您必須將 GitHub 使用者名稱和密碼 (或驗證權杖) 指定為 URL 的一部分。例如：
	> 
	> `set :repo_url, 'https://you:yourpassword@github.com/You/yourrepository.git'
	> 
	> 雖然這樣應該可讓您迴避錯誤並完成此教學課程，但這並不是實際執行部署的建議解決方案，因為這樣會將您的驗證認證以純文字的形式儲存在應用程式中。您應在作業系統的文件中，參閱有關於使用 SSH 代理程式進行轉送的部分。

現在，Ruby on Rails 應用程式應該已在 Azure 虛擬機器上執行。若要驗證這一點，請在網頁瀏覽器中輸入虛擬機器的 DNS 名稱。例如 http://railsvm.cloudapp.net。 應該會出現貼文索引，且您應該能夠建立、編輯和刪除貼文。

## <a id="next"></a>後續步驟

在本文中，您學到如何使用 Capistrano 建立基本的 Rails 應用程式，並發佈至 Azure 虛擬機器。使用如同本文中的基本應用程式，只能稍微討論到在部署中使用 Capistrano 的功能而已。如需使用 Capistrano 的詳細資訊，請參閱：

* [Capistranorb.com](http://capistranorb.com) - Capistrano 網站。
* [Azure、Ruby on Rails、Capistrano 3，與 PostgreSQL](http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql) - 與自訂部署指令碼相關之 Azure 部署的另一個方法。
* [Capistrano 3 教學課程](http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/) - 如何使用 Capistrano 3 的教學課程。

如需有關建立 Rails 應用程式並只使用 SSH 來部署至 Azure VM 的另一個基本範例，請參閱[使用 Linux 虛擬機器來裝載 Ruby on Rails Web 應用程式][ruby-vm]。

如果要深入了解 Ruby on Rails，請造訪 [Ruby on Rails 指南][rails-guides] (英文)。

若要深入了解如何使用 Azure SDK for Ruby 從 Ruby 應用程式存取 Azure，請參閱：

* [使用 Blob 儲存非結構化資料][blobs]

* [使用資料表儲存機碼值組][tables]

* [使用內容傳遞網路提供高頻寬內容][cdn-howto]

[vm-instructions]: /zh-tw/manage/linux/tutorials/virtual-machine-from-gallery/


[rails-guides]: http://guides.rubyonrails.org/
[blobs]: /zh-tw/develop/ruby/how-to-guides/blob-storage/
[tables]: /zh-tw/develop/ruby/how-to-guides/table-service/
[cdn-howto]: /zh-tw/develop/ruby/app-services/
[ruby-vm]: /zh-tw/develop/ruby/tutorials/web-app-with-linux-vm/
 
[blog-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
[blog-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png 
[default-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png
[default-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png
[vmlist]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png
[endpoints]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png
[new-endpoint]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png
[nginx-welcome]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png

[management-portal]: https://manage.windowsazure.com/
[sqlite3]: http://www.sqlite.org/
[ssh-on-azure]: http://azure.microsoft.com/zh-tw/documentation/articles/linux-use-ssh-key/
[capistrano]: http://capistranorb.com


<!--HONumber=42-->
