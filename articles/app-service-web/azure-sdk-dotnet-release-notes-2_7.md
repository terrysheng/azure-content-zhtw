
<properties 
   pageTitle="Azure SDK for .NET 2.7 版本資訊" 
   description="Azure SDK for .NET 2.7 版本資訊" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/04/2015"
   ms.author="juliako"/>


# Azure SDK for .NET 2.7 版本資訊

本文件包含 Azure SDK for .NET 2.7 版的版本資訊。

只有在 Visual Studio 2015 和 Visual Studio 2013 中才支援 Azure SDK 2.7。[Azure SDK 2.6](http://azure.microsoft.com/downloads/) 是 Visual Studio 2012 最後一個支援的 SDK。

如需此版本的詳細資訊，請參閱 [Azure SDK 2.7 公告文章](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)。

##Visual Studio 2015 的登入增強功能

Visual Studio 2015 的 Azure SDK 2.7 支援 Visual Studio 2015 新的身分識別管理功能。這包括支援透過角色型存取控制、雲端解決方案提供者、DreamSpark 以及其他帳戶和訂用帳戶類型存取 Azure 的帳戶。

這些登入增強功能，只有在 Visual Studio 2015 中才能使用。未來的更新將會包含對 Visual Studio 2013 的支援。


##Mobile SDK

更新了 **Mobile Apps** 範本，以反映最新的 [NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)和設定程序。

##服務匯流排 

一般錯誤修正與增強功能。如需更新和功能的詳細資訊，請參閱最新的[服務匯流排 NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/) 的版本資訊。

##HDInsight 工具 

此版本進行了下列更新。這些更新目前為預覽版。如需詳細資訊，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=619108)。

- 為 Hive on Tez 工作繪製 Hive 圖形
- 完整的 Hive DML IntelliSense 支援
- Pig 範本支援
- Azure 服務的 Storm 範本

###重大變更

- 使用這個版本的工具時，必須先升級舊的 **Storm** 專案。如需詳細資訊，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=619108)。
- 不再支援 Visual Studio Web Express。如需詳細資訊，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=619108)。

##Azure 應用程式服務工具

此版本對 Web 工具延伸模組進行了下列更新。如需詳細資訊，請參閱[此部落格](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)。

- 加入對 DreamSpark 帳戶的支援
- 對 Azure 工具進行全面的變更，以支援新的 Azure 資源管理 API
- 在[雲端總管](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)加入對 Azure App Service 的支援

###已知問題

伺服器總管的 [位置] 節點下方沒有出現 Web App 部署位置節點，而雲端總管下方沒有載入 Web App 部署位置的子節點。這個問題已經解決，下一個 SDK 版本將不再發生。


##<a id="cloud_explorer"></a>適用 Visual Studio 2015 的雲端總管

Azure SDK 2.7 包含適用 Visual Studio 2015 的雲端總管，可讓您從 Visual Studio 中檢視您的 Azure 資源、檢查其屬性，以及執行重要的開發人員動作。

雲端總管支援下列功能：

- Azure 資源的 [資源群組] 檢視與 [資源類型] 檢視 
- 依名稱搜尋資源 (適用於 [資源類型] 檢視)
- 支援套用角色型存取控制 (RBAC) 的訂用帳戶與資源 
- 整合式的 [動作] 面板會針對特定選取的資源顯示以開發人員為焦點的動作。例如：附加使用資源管理員堆疊建立之虛擬機器的遠端偵錯工具、檢視虛擬機器的診斷資料等等。
- 整合式的 [屬性] 面板會顯示以開發人員為焦點，在開發/測試期間經常需要的屬性 
- 快速切換列舉資源時要使用的帳戶 (使用工具列的 [設定] 命令) 
- 篩選列舉資源時要使用的訂用帳戶 (使用工具列的 [設定] 命令) 
- Azure Preview 入口網站中用來管理資源與資源群組的深層連結 
 
 
##Azure 資源管理員工具 

Azure 資源管理員工具已更新為使用角色型存取控制 (RBAC) 和新的訂用帳戶類型。除了傳統儲存體的功能之外，這些變更還附帶可使用新的儲存體帳戶在部署期間儲存成品。

如果您使用舊版 SDK 的 Azure 資源群組專案搭配 SDK 2.7，必須使用新的儲存體帳戶，而不是傳統儲存體帳戶部署新的部署指令碼。在變更您的專案以加入新的指令碼之前，會先提示您。舊的指令碼將會重新命名，而且您必須手動修改新的指令碼。
 
 
##儲存體總管工具 

- 支援檢視附加 Blob。如需詳細資訊，請參閱[此部落格文章](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx)。 
- 支援透過伺服器總管檢視進階儲存體帳戶。伺服器總管只會顯示進階儲存體帳戶的分頁 Blob，因為它們是進階儲存體帳戶唯一支援的類型。

##Visual Studio 的 Azure Data Factory 工具 

介紹 Visual Studio 的 **Azure Data Factory 工具**。以下是啟用的功能。如需詳細資訊，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=617530)。

- **以範本為基礎撰寫**：選取使用案例範本、資料移動範本或資料處理範本，以部署端對端資料整合解決方案，以及對 Data Factory 快速上手。 
- **整合方案總管，方便撰寫和部署 Data Factory 實體**：建立和部署管線與相關實體做為 Visual Studio 專案。 
- **整合圖表檢視，在撰寫同時也能進行視覺互動**：透過圖表檢視的協助，以視覺化方式撰寫管線與資料集。 
- **整合伺服器總管，方便瀏覽以及與已部署的實體互動**：利用伺服器總管，瀏覽已部署的資料處理站和對應的實體。將已部署的資料處理站或任何實體 (管線、連結的服務、資料集) 匯入您的專案。 
- **使用結構描述驗證與豐富的 Intellisense 進行 JSON 編輯**：使用豐富的 Intellisense 與結構描述驗證，有效率地設定和編輯 Data Factory 實體的 JSON 文件 
- **多重環境發佈**：藉由為每個環境建立不同的組態檔，將撰寫的管線發佈到開發、測試或生產環境。
- **Pig、Hive 和 .Net 資料處理支援**：支援在 Data Factory 專案中使用 Pig 和 Hive 指令碼。支援參考 C# 專案來管理 .Net 活動。

##另請參閱

[Azure SDK 2.7 公告文章](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

<!---HONumber=August15_HO6-->