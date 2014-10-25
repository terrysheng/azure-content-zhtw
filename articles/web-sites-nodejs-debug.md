<properties linkid="dev-nodejs-how-to-debug-website" urlDisplayName="Debug Websites (Node)" pageTitle="How to Debug Azure Websites in Node.js" metaKeywords="debug website azure, debugging azure, troubleshooting azure web site, troubleshoot azure website node" description="Learn how to debug an Azure website in Node.js." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="How to debug a Node.js application in Azure Websites" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# 如何在 Azure 網站中偵錯 Node.js 應用程式

Azure 提供內建的診斷功能，可協助您針對 Azure 網站所託管的 Node.js 應用程式進行偵錯。本文中，您將了解如何啟用 stdout 和 stderr 記錄、在瀏覽器中顯示錯誤資訊，以及如何下載和檢視記錄檔。

對於 Azure 上裝載的 Node.js 應用程式，診斷程式由 [IISNode][] 提供。本文只討論最常用來收集診斷資訊的設定，不提供 IISNode 的完整使用參考。如需有關使用 IISNode 的詳細資訊，請參閱 GitHub 的 [IISNode Readme][]。

## <span id="enablelogging"></span></a>啟用記錄

依預設，Azure 網站只擷取關於部署的診斷資訊，例如使用 Git 來部署網站時。如果您在部署時發生問題，此資訊很有用，例如安裝 **package.json** 中參考的模組失敗時，或使用自訂部署指令碼時。

若要啟用 stdout 和 stderr 資料流記錄，您必須在 Node.js 應用程式的根目錄中建立 **IISNode.yml** 檔案，並加入下列指令：

    loggingEnabled: true

這會啟用記錄來自 Node.js 應用程式的 stderr 和 stdout。

**IISNode.yml** 檔案也可用來控制當失敗發生時，將易懂的錯誤還是開發人員錯誤傳回至瀏覽器。若要傳回開發人員錯誤，請將下一行加入至 **IISNode.yml** 檔案：

    devErrorsEnabled: true

啟用此選項之後，IISNode 會將傳送至 stderr 的最後 64K 資訊傳回，而不是易懂的錯誤，例如「發生內部伺服器錯誤」。

<div class="dev-callout">
<strong>注意</strong>
<p>雖然 devErrorsEnabled 在開發期間診斷問題時很有用，但在生產環境中啟用可能會導致將開發錯誤傳送給使用者。</p>
</div>

如果您的應用程式中尚無 **IISNode.yml** 檔案，則在發行已更新的應用程之後，您必須重新啟動網站。如果只是在先前發行的現有 **IISNode.yml** 檔案中變更設定，則不需要重新啟動。

<div class="dev-callout">
<strong>注意</strong>
<p>如果網站是以 Azure 命令列工具或 Azure PowerShell Cmdlet 建立，則會自動建立預設的 <strong>IISNode.yml</strong> 檔案。</p>
</div>

您可以從 [Azure 管理入口網站][]選取網站，然後選取 [重新啟動] 按鈕，即可重新啟動網站：

![restart button][]

如果開發環境中已安裝 Azure 命令列工具，您可以使用下列命令來重新啟動網站：

    azure site restart [sitename]

<div class="dev-callout">
<strong>注意</strong>
<p>雖然 loggingEnabled 和 devErrorsEnabled 是最常用來擷取診斷資訊的 IISNode.yml 組態選項，但 IISNode.yml 也能用來為主控環境設定各種選項。如需完整的組態選項清單，請參閱 <a href="https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml">iisnode_schema.xml</a> 檔案。</p>
</div>

## <span id="viewlogs"></span></a>存取記錄檔

有三種方式可存取診斷記錄檔：使用檔案傳輸通訊協定 (FTP)、下載 Zip 封存檔，或即使更新的記錄資料流 (又稱為 tail)。需要有 Azure 命令列工具，才能下載記錄檔的 Zip 封存檔或檢視即時資料流。請使用下列命令來安裝這些工具：

    npm install azure-cli -g

安裝之後，可使用 'azure' 命令來存取工具。必須先設定命令列工具來使用您的 Azure 訂閱。如需有關如何完成此工作的詳細資訊，請參閱＜[如何使用 Azure 命令列工具][]＞文章的＜**如何使用 Azure 命令列工具**＞一節。

### FTP

若要透過 FTP 來存取診斷資訊，請造訪 [Azure 入口網站]，選取您的網站，然後選取 [儀表板]。在快速連結區段中，[FTP DIAGNOSTIC LOGS] 和 [FTPS DIAGNOSTIC LOGS] 連結可讓您使用 FTP 通訊協定來存取記錄檔。

<div class="dev-callout">
<strong>注意</strong>
<p>如果您先前沒有為 FTP 或部署設定使用者名稱和密碼，您可以從 [快速入門] 管理頁面中選取 [設定部署認證] 來設定。</p>
</div>

儀表板中傳回的 FTP URL 是 **LogFiles** 目錄，內含下列子目錄：

-   [部署方法] - 如果您使用 Git 之類的部署方法，則會建立相同名稱的目錄，其中含有與部署相關的資訊。

-   nodejs - 從應用程式的所有執行個體擷取的 stdout 和 stderr 資訊 (當 loggingEnabled 為 true 時)。

### Zip 封存檔

若要下載診斷記錄的 Zip 封存檔，請從 Azure 命令列工具中使用下列命令：

    azure site log download [sitename]

這會將 **diagnostics.zip** 下載到目前目錄。此封存檔包含下列目錄結構：

-   deployments - 關於應用程式部署的資訊記錄

-   LogFiles

    -   [部署方法] - 如果您使用 Git 之類的部署方法，則會建立相同名稱的目錄，其中含有與部署相關的資訊。

    -   nodejs - 從應用程式的所有執行個體擷取的 stdout 和 stderr 資訊 (當 loggingEnabled 為 true 時)。

### 即時資料流 (tail)

若要檢視診斷記錄資訊的即時資料流，請從 Azure 命令列工具中使用下列命令：

    azure site log tail [sitename]

這會傳回伺服器上最新發生之記錄事件的資料流。此資料流會傳回部署資訊及 stdout 和 stderr 資訊 (當 loggingEnabled 為 true 時)。

## <span id="nextsteps"></span></a>後續步驟

本文中，您學到如何啟用和存取 Azure 的診斷資訊。此資訊有助於了解應用程式發生的問題，但也可能指出您使用的模組有問題，或 Azure 網站使用的 Node.js 版本和部署環境中使用的版本不同。

如需有關在 Azure 上使用模組的詳細資訊，請參閱＜[在 Azure 應用程式中使用 Node.js 模組][]＞(英文)。

如需有關為應用程式指定 Node.js 版本的詳細資訊，請參閱＜[在 Azure 應用程式中指定 Node.js 版本][]＞(英文)。

  [IISNode]: https://github.com/tjanczuk/iisnode
  [IISNode Readme]: https://github.com/tjanczuk/iisnode#readme
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [restart button]: ./media/web-sites-nodejs-debug/restartbutton.png
  [iisnode\_schema.xml]: https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml
  [如何使用 Azure 命令列工具]: /zh-TW/documentation/articles/xplat-cli/
  [在 Azure 應用程式中使用 Node.js 模組]: /zh-TW/documentation/articles/nodejs-use-node-modules-azure-apps/
  [在 Azure 應用程式中指定 Node.js 版本]: /zh-TW/documentation/articles/nodejs-specify-node-version-azure-apps/
