<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="將 Azure SQL DB Elastic Scale 參考加入至 Visual Studio 專案" description="如何使用 Nuget 將 Elastic Scale API 的 .NET 參照新增到 Visual Studio 專案。" metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#作法：將 Azure SQL DB Elastic Scale 參考加入至 Visual Studio 專案

###必要條件： 

- 安裝適用於 Visual Studio 的 [NuGet Visual Studio Extension Gallery](http://docs.nuget.org/docs/start-here/installing-nuget)

###若要在 Visual Studio 中加入 Elastic Scale 參考 

1. 透過位於 [**檔案**] --> [**新增**] --> [**專案**] 的 [新增專案] 對話方塊建立新專案
2. 在 [方案總管] 中以滑鼠右鍵按一下 [**參考**]，然後選取 [**Manage NuGet Packages**]
3. 在 [管理 NuGet 封裝] 視窗左側的功能表中，依序選取 [**線上**] 和 [**nuget.org**] 或 [全部]
4. 在 [**線上搜尋**] 對話方塊中，輸入 **Elastic Scale**，選取 [**Elastic Scale 用戶端程式庫**]，然後按一下 [**安裝**]。
4. 檢閱授權，並按一下 [**我接受**]。 

Azure SQL Database Elastic Scale 參考現在已加入至您的專案。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
