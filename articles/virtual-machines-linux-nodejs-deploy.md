<properties
   pageTitle="將 Node.js 應用程式部署到 Azure 中的 Linux 虛擬機器"
   description="了解如何將 Node.js 應用程式部署到 Azure 中的 Linux 虛擬機器"
   services=""
   documentationCenter="nodejs"
   authors="stepro"
   manager="dmitryr"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="stephpr"/>

# 將 Node.js 應用程式部署到 Azure 中的 Linux 虛擬機器

本教學課程說明如何將 Node.js 應用程式部署到 Azure 中執行的 Linux 虛擬機器。本教學課程中的指示可運用在任何足以執行 Node.js 應用程式的作業系統上。

您將學習如何：

- 分叉和複製含有簡易待辦事項應用程式的 GitHub 儲存機制；
- Azure 中建立和設定兩個 Linux 虛擬機器來執行應用程式；
- 將更新發送至 Web 前端虛擬機器來反覆測試應用程式。

> [AZURE.NOTE]
若要完成本教學課程，您需要有 GitHub 帳戶和 Microsoft Azure 帳戶，並且能夠從開發電腦上使用 Git。

> 如果您沒有 GitHub 帳戶，您可以在[這裡](https://github.com/join)註冊。

> 如果您沒有 [Microsoft Azure](https://azure.microsoft.com/) 帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)註冊免費試用帳戶。如果您還沒有 Microsoft 帳戶，這也會引導您完成 [Microsoft 帳戶](http://account.microsoft.com)的註冊程序。或者，如果您是 Visual Studio 訂閱者，您可以[啟用 MSDN 權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> 如果您的開發電腦上沒有 git，當您使用 Macintosh 或 Windows 機器時，請從[這裡](http://www.git-scm.com)安裝 git。如果您使用 Linux，請使用最適合您的機制來安裝 git，例如 `sudo apt-get install git`。

## 分叉和複製待辦事項應用程式

本教學課程所使用的待辦事項應用程式會透過 MongoDB 執行個體，實作簡單的 Web 前端來追蹤待辦事項清單。登入 GitHub 之後，移至[這裡](https://github.com/stepro/node-todo)來尋找應用程式，並使用右上方的連結將它分叉。這應該會在您的帳戶中建立名為 *accountname*/node-todo 的儲存機制。

現在，在您的開發電腦上複製這個儲存機制：

    git clone https://github.com/accountname/node-todo.git

我們稍後會使用此儲存機制本機複製品來變更原始程式碼。

## 建立和設定 Linux 虛擬機器

Azure 充分支援使用 Linux 虛擬機器執行原始計算。這部分的教學課程將說明如何輕鬆地讓兩個 Linux 虛擬機器運作，並在上面部署待辦事項應用程式，其中一個執行 Web 前端，另一個執行 MongoDB 執行個體。

### 建立虛擬機器

在 Azure 中建立新的虛擬機器最簡單方式是使用 Azure 入口網站。按一下[這裡](https://portal.azure.com)登入，並在網頁瀏覽器中啟動 Azure 入口網站。載入 Azure 入口網站後，請完成下列步驟：

- 按一下 [+ 新增] 連結；
- 選取 [計算] 類別，然後選擇 [Ubuntu Server 14.04 LTS]；
- 選取 [資源管理員] 部署模型，然後按一下 [建立]；
- 依照下列指導方針填寫基本資料：
  - 指定您稍後可以輕鬆識別的名稱；
  - 針對本教學課程，請選擇 [密碼] 驗證；
  - 使用可識別的名稱建立新的資源群組。
- 在虛擬機器大小方面，[A1 標準] 是適合本教學課程的選擇。
- 關於其他設定，請確定磁碟類型為 [標準]，並接受其餘所有預設值。
- 在摘要頁面上開始建立。

執行上述程序兩次以建立兩個 Linux 虛擬機器，一個用於 Web 前端，另一個用於 MongoDB 執行個體。建立虛擬機器需要大約 5-10 分鐘。

### 指派虛擬機器的 DNS 項目

根據預設，只有透過公用 IP 位址 (例如 1.2.3.4) 才能存取 Azure 中建立的虛擬機器。我們指派 DNS 項目讓電腦更容易識別。

一旦入口網站指出已建立虛擬機器，請按一下左側導覽列中的 [虛擬機器] 連結，並找出您的機器。針對每一部機器：

- 找出 [基本資訊] 索引標籤，然後按一下 [公用 IP 位址]。
- 在公用 IP 位址設定中，指派 DNS 名稱標籤，然後儲存。

入口網站會確保您指定的名稱可用。儲存組態之後，虛擬機器會有類似 `machinename.region.cloudapp.azure.com` 的主機名稱。

### 連線至虛擬機器

當佈建虛擬機器時，它們已預先設定為允許透過 SSH 從遠端連接。這是我們將用來設定虛擬機器的機制。如果您使用 Windows 進行開發，您必須取得 SSH 用戶端 (如果還沒有的話)。常見的選擇是 PuTTy，您可以從[這裡](http://www.chiark.greenend.org.uk/~sgtatham/putty/)下載。Macintosh 與 Linux 作業系統會預先安裝一個 SSH 版本。

### 設定 Web 前端虛擬機器

使用 PuTTY、ssh 命令列或您偏好的其他 SSH 工具，透過 SSH 連線到您建立的 Web 前端機器。您應該會看到歡迎使用訊息，後面接著命令提示字元。

首先，讓我們確定 git 和節點都已安裝：

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs
    
由於應用程式的 Web 前端依賴一些原生的 Node.js 模組，我們還需要安裝一套基本的建置工具：

    sudo apt-get install -y build-essential

最後，讓我們安裝一個稱為 *forever* 的 Node.js 應用程式，以協助執行 Node.js 伺服器應用程式：

    sudo npm install -g forever
    
為了能夠執行應用程式的 Web 前端，這些是此虛擬機器上需要的所有相依項目，讓我們啟動它們。若要這樣做，我們將先從您之前分叉的 GitHub 儲存機制建立裸機複製品，讓您可以輕鬆地將更新發佈至虛擬機器 (我們稍後會說明此更新案例)，然後再複製裸機複製品來提供可實際執行的儲存機制版本。

從起始 (~) 目錄開始，執行下列命令 (將 *accountname* 換成您的 GitHub 使用者帳戶名稱)：

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

現在，進入 node-todo 目錄並執行下列命令：

    npm install
    forever start server.js
    
現在，應用程式的 Web 前端正在執行，但還需要完成一個步驟，您才能從網頁瀏覽器存取應用程式。您所建立的虛擬機器由一個稱為*網路安全性群組*的 Azure 資源所保護，這是在您佈建虛擬機器時為您建立的群組。目前，此資源只允許將到達連接埠 22 的外部要求路由傳送到虛擬機器，這樣可透過 SSH 與機器通訊，但也僅此而已。因此，若要檢視設定在連接埠 8080 上執行的待辦事項應用程式，此連接埠也必須開啟。

返回 Azure 入口網站，並完成下列步驟：

- 按一下左側導覽列中的 [資源群組]；
- 選取包含您的虛擬機器的資源群組；
- 在產生的資源清單中，選取網路安全性小組 (有保護盾圖示)；
- 在屬性中，選擇 [輸入安全性規則]；
- 在工具列中，按一下 [新增]；
- 提供名稱，例如 "default-allow-todo"；
- 將通訊協定設為 [TCP]；
- 將目的地連接埠範圍設為 "8080"；
- 按一下 [確定]，並等候安全性規則建立。

建立這個安全性規則之後，待辦事項應用程式在網際網路上就公開可見，您可以使用如下的 URL 來瀏覽它：

    http://machinename.region.cloudapp.azure.com:8080

您會發現，即使我們還沒有設定 MongoDB 虛擬機器，待辦事項應用程式也已頗具功能。這是因為來源儲存機制已硬式編碼為使用預先部署的 MongoDB 執行個體。一旦我們設定 MongoDB 虛擬機器後，我們會返回並變更原始碼，以改用我們的私用 MongoDB 執行個體。

### 設定 MongoDB 虛擬機器

使用 PuTTY、ssh 命令列或您偏好的其他 SSH 工具，透過 SSH 連線到您建立的第二部機器。看到歡迎訊息和命令提示字元之後，安裝 MongoDB (這些指示取自於[這裡](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/))：

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

根據預設，MongoDB 會設定為只供本機存取。本教學課程中，我們會將 MongoDB 設定為可從應用程式的虛擬機器存取。在虛擬環境下，開啟 /etc/mongod.conf 檔案並尋找 `# network interfaces` 一節。將 `net.bindIp` 組態值變更為 `0.0.0.0`

> [AZURE.NOTE]
此設定只用於本教學課程。**不是**建議的安全性作法，不應該用於實際執行環境。

現在，請確定 MongoDB 服務已啟動：

    sudo service mongod restart

根據預設，MongoDB 會透過連接埠 27017 運作。因此，就像我們需要在 Web 前端虛擬機器上開啟連接埠 8080 一樣，我們也需要在 MongoDB 虛擬機器上開啟連接埠 27017。

返回 Azure 入口網站，並完成下列步驟：

* 按一下左側導覽列中的 [資源群組]；
* 選取包含 MongoDB 虛擬機器的資源群組；
* 在產生的資源清單中，選取與 MongoDB 虛擬機器同名的網路安全性小組 (有保護盾圖示)；
* 在屬性中，選擇 [輸入安全性規則]；
* 在工具列中，按一下 [新增]；
* 提供名稱，例如 "default-allow-mongo"；
* 將通訊協定設為 [TCP]；
* 將目的地連接埠範圍設為 "27017"；
* 按一下 [確定]，並等候安全性規則建立。

## 反覆測試待辦事項應用程式
目前為止，我們已佈建兩個 Linux 虛擬機器：一個執行應用程式的 Web 前端，另一個執行 MongoDB 執行個體。但有問題，Web 前端實際上還未使用已佈建的 MongoDB 執行個體。讓我們更新 Web 前端程式碼來使用環境變數，而非硬式編碼的執行個體，以解決這個問題。

### 變更待辦事項應用程式

在您最初複製 node-todo 儲存機制的開發電腦上，使用您喜好的編輯器開啟 `node-todo/config/database.js` 檔案，將 url 值從硬式編碼值 (例如 `mongodb://...`) 變更為 `process.env.MONGODB`。

認可變更並推送至 GitHub master：

    git commit -am "Get MongoDB instance from env"
    git push origin master

不幸的是，這不會將變更發佈至 Web 前端虛擬機器。讓我們稍微再變更該虛擬機器，以啟用一個簡單但很有效率的機制來發佈變更，讓您可以在實際環境中快速地觀察變更的效果。

### 設定 Web 前端虛擬機器
您應該記得，我們先前在 Web 前端虛擬機器上建立 node-todo 儲存機制的裸機複製品。原來這個動作會建立新的 Git 遠端，讓變更推送到那裡。不過，只是推送至此遠端無法提供開發人員處理程式碼時所尋找的快速反覆模型。

我們希望做的是確保推送至虛擬機器上的遠端儲存機制時，執行中的待辦事項應用程式會自動更新。所幸，這使用 git 就能輕易達成。

Git 公開一些會在特定時間呼叫的勾點，可對儲存機制上採取的動作有所反應。這些是使用儲存機制的 `hooks` 資料夾中的殼層指令碼來指定。自動更新案例最適合的勾點是 `post-update` 事件。

在 Web 前端虛擬機器的 SSH 工作階段，切換至 `~/node-todo.git/hooks` 目錄，並將下列內容新增至名為 `post-update` 的檔案 (將 `machinename` 和 `region` 換成您的 MongoDB 虛擬機器資訊)：

    #!/bin/bash
    
    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info
    
請執行下列命令來確定這個檔案可執行：

    chmod 755 post-update

此指令碼可確保目前的伺服器應用程式已停止、複製的儲存機制中的程式碼已更新為最新、滿足任何更新的相依性，以及已重新啟動伺服器。它也可確保已設定環境來準備接收我們的第一個應用程式更新，從環境變數取得 MongoDB 執行個體。

### 設定您的開發電腦
現在讓我們將您的開發電腦連接到 Web 前端虛擬機器。這就像在虛擬機器上新增裸機儲存機制作為遠端那麼簡單。執行下列命令來執行這項操作 (將 *user* 換成您的 Web 前端虛擬機器登入名稱，並視情況取代 *machinename* 和 *region*)：

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

這就是將變更推送 (事實上是發佈) 至 Web 前端虛擬機器所需的全部動作。

### 發佈更新

讓我們發佈目前為止已完成的一項變更，使應用程式使用我們自己的 MongoDB 執行個體

    git push azure master

您應該會看到如下所示的輸出：

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

此命令完成後，請嘗試在網頁瀏覽器中重新整理應用程式。您應該可以看到此處顯示的待辦事項清單是空的，且不再繫結到已部署的共用 MongoDB 執行個體。

在本教學課程最後，讓我們再完成另一項更明顯的變更。在您的開發電腦上，使用您喜好的編輯器開啟 node-todo/public/index.html 檔案。找出 jumbotron 標頭，將標題 "I'm a Todo-aholic" 變更為 "I'm a Todo-aholic on Azure!"。

現在讓我們確認：

    git commit -am "Azurify the title"

這一次，在將變更推回至 GitHub 儲存機制之前，讓我們先將變更發佈至 Azure：

    git push azure master

此命令完成之後，重新整理網頁，您會看到所做的變更。因為看起來沒問題，請將變更推回至原始遠端：

    git push origin master

## 後續步驟
這篇文章說明如何將 Node.js 應用程式部署到 Azure 中執行的 Linux 虛擬機器。若要深入了解 Azure 中的 Linux 虛擬機器，請參閱 [Azure 上的 Linux 簡介](/documentation/articles/virtual-machines-linux-introduction/)。
    
如需如何在 Azure 上開發 Node.js 應用程式的詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

<!---HONumber=AcomDC_0211_2016-->