<properties 
   pageTitle="Azure SDK for .NET 2.9 版本資訊" 
   description="Azure SDK for .NET 2.9 版本資訊" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/29/2016"
   ms.author="juliako"/>


# Azure SDK for .NET 2.9 版本資訊

##概觀

本文包含 Azure SDK for .NET 2.9 版的版本資訊。

如需此版本之更新內容的詳細資訊，請參閱 [Azure SDK 2.9 公告文章](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)。

## Azure SDK 2.9 for Visual Studio 2015 Update 2 和 Visual Studio "15" 預覽
 
此更新包含下列錯誤修正：

- 與 REST API 用戶端產生有關的問題，在此問題中，字串「未知類型」會出現成為 code-gen 資料夾的名稱和/或放入所產生程式碼之命名空間的名稱。
- 與排程的 WebJobs 有關的問題，在此問題中，驗證資訊無法傳遞給排程器佈建程序。

此更新包含下列新功能：

- 在 App Service 佈建對話方塊的 [服務] 索引標籤中支援第二個 App Service。 

##Azure Data Lake Tools for Visual Studio 2015 Update 2
 
此更新包含下列工具：

- **Azure Data Lake Tools** for Visual Studio 現在已合併到 Azure SDK for .NET 版本。當您安裝 Azure SDK 時，便會自動安裝此工具。 

	此工具會經常更新，請前往[這裡](http://aka.ms/datalaketool)取得更新。

- **伺服器總管**現在可讓您全部檢視，並建立一些 U-SQL 中繼資料實體。如需詳細資訊，請參閱[此部落格]()。


##HDInsight 工具 

**HDInsight Tools** for Visual Studio 現在支援 HDInsight 3.3 版，包括顯示 Tez 圖形和其他語言修正。


##Azure 資源管理員 

本版新增對於 ARM 範本的[金鑰保存庫](../resource-manager-keyvault-parameter.md)支援。

##另請參閱

[Azure SDK 2.9 公告文章](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

<!---HONumber=AcomDC_0330_2016-->