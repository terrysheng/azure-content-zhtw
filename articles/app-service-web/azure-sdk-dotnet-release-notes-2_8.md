
<properties 
   pageTitle="Azure SDK for .NET 2.8 版本資訊" 
   description="Azure SDK for .NET 2.8 版本資訊" 
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
   ms.date="11/18/2015"
   ms.author="juliako"/>

# Azure SDK for .NET 2.8

##概觀
 
本文包含 Azure SDK for .NET 2.8 版本的版本資訊 (包含已知問題和重大變更)。

如需這一版中的新功能和更新的完整清單，請參閱 [Azure SDK 2.8 for Visual Studio 2013 and Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) 公告。

## 下載 Azure SDK for .NET 2.8

[Azure SDK for .NET 2.8 for Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285)

[Azure SDK for .NET 2.8 for Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
## .NET 4.5.2 支援 

###已知問題

Azure.NET SDK 2.8 可讓您建立 .NET 4.5.2 雲端服務套件。不過，.NET 4.5.2 Framework 要到 2016 年 1 月的客體作業系統版本，才會安裝在預設的客體作業系統映像上。在這之前，可以透過獨立的客體作業系統版本 November 2015-02 取得 .NET 4.5.2 Framework。請參閱 [Azure 客體作業系統版本與 SDK 相容性矩陣](http://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/)頁面以追蹤該映像何時發行。一旦 November 2015-02 映像發行，您便可選擇透過更新您的雲端服務組態檔 (.cscfg) 來使用該映像。在服務組態檔中，將 ServiceConfiguration 項目的 osVersion 屬性設定為字串 "WA-GUEST-OS-4.26\_201511-02"。


##Azure Data Factory

###已知問題 

如果電腦上安裝的 Azure Power Shell 版本比 0.9.8 新，則在與範例資料相關的 **Data Factory 範本**專案建立期間，Azure Power Shell 指令碼可能會失敗。

若要順利地建立這種類型的專案，您必須安裝 [Azure Power Shell 0.9.8 版](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)。


## Azure 資源管理員工具 

###重大變更

Azure 資源群組專案提供的 PowerShell 指令碼在這個版本中已更新，以搭配新的 Azure PowerShell Cmdlet 1.0 版使用。使用 2.8 以前的 SDK 版本時，將無法從 Visual Studio 使用這個新的指令碼。

使用 2.8 SDK 時，將無法從 Visual Studio 執行在舊版 SDK 建立的專案中的指令碼。使用適當的 Azure PowerShell Cmdlet 版本，所有指令碼將可繼續在 Visual Studio 以外使用。

2\.8 SDK 需要 1.0 版的 Azure PowerShell Cmdlet。所有其他版本的 SDK 則需要 0.9.8 版的 Azure PowerShell Cmdlet。 如需詳細資訊，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkID=623011)。

##Azure HDInsight 工具

新更新包括：

- 您可以透過 HiveServer2 在叢集中執行 Hive 查詢，而且幾乎不需要額外支出，並可即時查看工作記錄檔。
- 使用新的 Hive 工作執行檢視，您可以深入探究您的工作、尋找更多詳細資料和識別潛在的問題。

如需詳細資訊，請參閱 [Azure SDK 2.8 for Visual Studio 2013 and Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)。

##Web 工具擴充功能

###已知問題

下列版本將解決下列已知問題。

- 非生產環境 (例如 Azure China 或 Azure Stack 客戶) 的 App Service 相關雲端與伺服器總管筆勢無效。對於這些受影響區域中的客戶，從 Azure 入口網站下載發行設定檔就能發行。未來版本將為 Azure China 或 Azure Stack 客戶修復筆勢，例如「連結偵錯工具」和「檢視資料流記錄」。 
- 客戶在建立 App Service 時，若要部署的目標 App Insights 執行個體位於美國東部以外的區域，他們可能會看到錯誤。在這些情況下，在入口網站建立 App Service 和下載發行設定檔就能發行。 


##另請參閱

[Azure SDK 2.8 公告文章](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Azure SDK for .NET 和 API 的支援和停用資訊](https://msdn.microsoft.com/library/azure/dn479282.aspx)

<!---HONumber=Nov15_HO4-->