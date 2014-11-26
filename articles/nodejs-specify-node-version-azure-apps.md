<properties linkid="develop-nodejs-common-tasks-specifying-a-node-version" urlDisplayName="Specifying a Node.js Version" pageTitle="Specifying a Node.js Version" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Specifying a Node.js version in an Azure application" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# 在 Azure 應用程式中指定 Node.js 版本

裝載 Node.js 應用程式時，您可能會想要確定應用程式是使用特定版本的 Node.js。有數種方式可以為 Azure 上裝載的應用程式完成這項動作。

## 預設版本

Azure 目前提供 Node.js 0.6.17、0.6.20 和 0.8.4 版。除非另有指定，否則 0.6.20 將是使用的預設版本。

<div class="dev-callout">
<strong>注意</strong>
<p>如果您要將應用程式裝載在 Azure 雲端服務 (Web 或背景工作角色) 中，而且這是您第一次部署應用程式，只要您安裝在部署環境中的 Node.js 版本符合 Azure 上提供的其中一個預設版本，Azure 就會嘗試使用這個版本。</p>
</div>

## 以 package.json 進行版本設定

您可以在 **package.json** 檔案中新增下列內容，以指定要使用的 Node.js 版本。

    "engines":{"node":version}

其中 *version* 是要使用的特定版本號碼。您可以針對 version 指定較複雜的條件，例如：

    "engines":{"node": "0.6.22 || 0.8.x"}

由於 0.6.22 不是主控環境中已提供的版本，因此將改用 0.8 系列可用的最高版本，也就是 0.8.4 版。

## 以 PowerShell 進行雲端服務版本設定

如果您要將應用程式裝載在雲端服務中，而且要使用 Azure PowerShell 來部署應用程式，您可以使用 **Set-AzureServiceProjectRole** PowerShell Cmdlet 覆寫預設 Node.js 版本。例如：

    Set-AzureServiceProjectRole WebRole1 node 0.8.4

您也可以使用 **Get-AzureServiceProjectRoleRuntime** 擷取對於以雲端服務形式裝載的應用程式而言，可用的 Node.js 版本清單。

## 對 Azure 網站使用自訂版本

雖然 Azure 提供數個預設 Node.js 版本，不過您可能會想要使用預設未提供的版本。如果您的應用程式是以 Azure 網站形式裝載，您可以使用 **iisnode.yml** 檔案達到該目的。下列步驟將逐步引導您對 Azure 網站使用自訂版本的 Node.Js：

1.  建立新目錄，然後在目錄中建立 **server.js** 檔案。**server.js** 檔案應該包含下列內容：

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    這將顯示當您瀏覽網站時會使用的 Node.js 版本。

2.  建立新網站並記下網站的名稱。例如，以下使用 [Azure 命令列工具][Azure 命令列工具]建立新的 Azure 網站 (名稱為 **mywebsite**)，然後編輯該網站的 Git 存放庫。

        azure site create mywebsite --git

3.  建立名稱為 **bin** 的新目錄作為 **server.js** 檔案所在目錄的子目錄。

4.  下載要讓應用程式使用的特定 **node.exe** 版本 (Windows 版本)。例如，以下使用 **curl** 下載 0.8.1 版。

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    將 **node.exe** 檔案儲存到先前建立的 **bin** 資料夾中。

5.  在 **server.js** 檔案所在的同一個目錄中建立 **iisnode.yml** 檔案，然後在 **iisnode.yml** 檔案中新增下列內容：

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    您將應用程式發行到 Azure 網站後，您專案中的 **node.exe** 檔案將位在這個路徑中。

6.  發行您的應用程式。例如，由於我稍早是使用 --git 參數建立新的網站，下列命令會將應用程式檔案新增到我的本機 Git 存放庫，然後將這些檔案推播到網站存放庫：

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    發行應用程式後，使用瀏覽器開啟網站。您應該會看見 "Hello from Azure running node version: v0.8.1" 訊息。

## 後續步驟

您已了解如何指定應用程式使用的 Node.js 版本，現在請了解如何[使用模組][使用模組]、[建置並部署 Node.js 網站][建置並部署 Node.js 網站]和[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具][Azure 命令列工具]。

  [Azure 命令列工具]: /zh-TW/documentation/articles/xplat-cli/
  [使用模組]: /zh-TW/documentation/articles/nodejs-use-node-modules-azure-apps/
  [建置並部署 Node.js 網站]: /zh-TW/documentation/articles/web-sites-nodejs-develop-deploy-mac/
