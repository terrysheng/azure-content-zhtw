<properties 
   pageTitle="使用 Visual Studio 建立 Azure 私人雲端 | Microsoft Azure"
   description="使用 Visual Studio 建立 Azure 專案"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="tarcher" />

# 使用 Visual Studio 建立 Azure 專案

Azure Tools for Visual Studio 提供可讓您建立 Azure 雲端服務的範本。此工具也可協助您設定、偵錯雲端服務，以及將雲端服務部署至 Azure。

Azure 雲端服務解決方案包含下列專案類型：

- **Azure 專案** 
    
    Azure 專案與方案中的角色專案有關。它還包含服務定義檔和服務組態檔。服務定義檔可定義應用程式的執行階段設定，包括需要哪些角色、端點和虛擬機器大小。服務組態檔可設定一個角色可以執行的執行個體數目，以及為角色定義的設定值。如需有關這些設定的詳細資訊，請參閱[如何：使用 Visual Studio 設定 Azure 雲端服務的角色](https://msdn.microsoft.com/library/azure/hh369931.aspx)。

- **Web 角色專案**
 
    背景工作角色會執行背景處理。背景工作角色可以與儲存體服務和其他網際網路服務進行通訊。背景工作角色可以有任意數目的 HTTP、HTTPS 或 TCP 端點。

    - **ASP.NET Web 角色**：適用於建置 ASP.NET 應用程式與 Web 前端
    - **ASP.NET MVC5 Web 角色**
    - **ASP.NET MVC4 Web 角色**
    - **ASP.NET MVC3 Web 角色**
    - **WCF 服務 Web 角色**：適用於建置 WCF 服務
    - **Silverlight 企業營運應用程式 Web 角色** (需要 Visual Studio 2012)

- **快取背景工作角色**

    可提供應用程式專用快取的角色。

- **搭配服務匯流排佇列的背景工作角色**

    服務匯流排佇列可提供與背景工作角色處理序進行通訊的訊息佇列功能。如需詳細資訊，請參閱[如何使用服務匯流排佇列](http://go.microsoft.com/fwlink/?LinkId=260560) (英文)。

## 在 Visual Studio 中建立 Azure 雲端服務專案

1. 以系統管理員身分啟動 Microsoft Visual Studio。

1. 在功能表上，依序選擇 [檔案]、[新增] 和 [專案]。

1. 在 [專案類型] 窗格中，從 Visual C# 或 Visual Basic 專案範本節點選擇 [雲端]。

1. 在 [範本] 窗格中選擇 [Azure 雲端服務]。

1. 指定您想要用來開發專案的 .NET Framework 版本。

1. 輸入專案的名稱和位置以及方案的名稱。選擇 [確定] 按鈕。

1. 在 [新增 Azure 專案] 對話方塊中，選擇您想要新增的角色，並選擇向右箭號按鈕將它們加入至您的方案。您可以根據需求加入任意多個角色。

1. 若要重新命名已加入至專案的角色，請將游標放在 [新增 Azure 專案] 對話方塊中的角色上面，然後選擇角色右側的 [重新命名]。您也可以為已加入方案的角色重新命名。

<!---HONumber=AcomDC_1223_2015-->