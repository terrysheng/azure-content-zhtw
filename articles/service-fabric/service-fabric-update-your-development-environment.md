<properties
   pageTitle="更新 Service Fabric 開發環境 | Microsoft Azure"
   description="更新 Service Fabric 開發環境，以使用最新的執行階段、SDK 和工具。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2015"
   ms.author="seanmck"/>

# 更新 Service Fabric 開發環境

 Service Fabric 定期提供新版本的執行階段、SDK 和工具，以在本機開發中使用。使用這些版本保持您的本機開發環境更新，可確保您在本機建置和測試您的應用程式時，永遠可以存取最新功能、錯誤修正和效能改進。

## 清除您的本機叢集

 Service Fabric 目前不支援在本機叢集執行時升級 Service Fabric 執行階段。為了確保全新升級，請務必先清除本機叢集。

 >[AZURE.NOTE]清除您的本機叢集將會移除所有已部署的應用程式和其資料。

 您可以清理您的本機叢集，如下所示：


 1. 關閉所有其他的 PowerShell 視窗，並以系統管理員身分啟動一個新視窗。

 2. 瀏覽至包含 `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"` 的叢集安裝目錄

 3. 執行 `.\CleanCluster.ps1`


## 更新執行階段、SDK 和工具

 一旦成功清除現有的叢集，您可以繼續如下所示進行升級：


 1. 啟動 Web Platform Installer 以[更新為新版本][1]。

 2. 完成時，以系統管理員身分啟動新的 PowerShell 視窗，並瀏覽至 `cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"` 叢集安裝目錄。

 3. 執行 `.\DevClusterSetup.ps1` 以設定您的本機叢集。

就這麼簡單！ 您現在可以啟動 Visual Studio 並繼續建置Service Fabric 應用程式。

>[AZURE.NOTE]預設專案結構已經在這個版本中變更。您應該能夠在 Visual Studio 中開啟並執行現有的專案。不過，如果對於建置、部署或偵錯您的應用程式遇到任何問題，請考慮建立新的專案，並將您的程式碼移轉過來。

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI 連結"

<!---HONumber=Oct15_HO3-->