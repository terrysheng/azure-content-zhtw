<properties
	pageTitle="安裝 Azure Toolkit for Eclipse"
	description="了解如何安裝 Azure Toolkit for Eclipse。"
	services=""
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# 安裝 Azure Toolkit for Eclipse #

Azure Toolkit for Eclipse 提供範本和功能，可讓您輕鬆地使用 Eclipse 開發環境來建立、開發、測試及部署 Azure 應用程式。它是開放原始碼專案，您可以透過 Apache 授權 2.0 從 GitHub 上的專案網站取得其原始程式碼，URL 如下：

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>。

下列步驟示範如何安裝 Azure Toolkit for Eclipse。

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## 安裝 Azure Toolkit for Eclipse ##

1. 啟動 Eclipse。
2. 在 Eclipse 內，於功能表中按一下 [說明]<strong></strong>，然後按一下 [安裝新軟體]<strong></strong>，如下圖所示。![][ic590123]
3. 在 [可用軟體]<strong></strong> 對話方塊的 [使用]<strong></strong> 文字方塊中，輸入 <strong>http://dl.msopentech.com/eclipse</strong>，然後按 <strong>Enter</strong> 鍵。
4. 在 [名稱]<strong></strong> 窗格中，核取 [Azure Toolkit for Eclipse]<strong></strong>，然後取消核取 [在安裝期間連絡所有更新網站來尋找必要軟體]<strong></strong>。您的畫面看起來應該類似如下：![][ic719482]
5. 如果您展開 [Azure Toolkit for Eclipse]<strong></strong>，則會看到下列項目：
    * **Azure Access Control Services Filter**：這個元件支援使用 Azure ACS 來驗證應用程式使用者。
    * **Azure Common Plugin**：這個元件包含其他元件所依賴的共用功能。
    * **Azure Toolkit for Eclipse**：這個元件包含專案設定邏輯、發行至雲端精靈及使用者介面。
    * **Microsoft JDBC Driver 4.0 for SQL Server**：這個元件可簡化使用 SQL Database 開發應用程式的作業。
    * **Package for Apache Qpid Client Libraries for JMS**：這個元件提供來自 Apache Qpid 專案的 JMS 用戶端程式庫，讓應用程式得以在 Azure 中使用以進階訊息佇列通訊協定 (AMQP) 為基礎的傳訊
    * **Package for Azure Libraries for Java**：這個元件可讓您以 Java 建置 Azure 應用程式，以便充分利用可擴充的 Azure 雲端運算資源。
    * **Application Insights Plugin for Java**：這個元件可讓您將 Azure 的遙測記錄和分析服務用於應用程式和伺服器執行個體上。
6. 按 [下一步]。(如果您在安裝此工具組時遇到不尋常的延遲，請確認已取消核取 [在安裝期間連絡所有更新網站來尋找必要軟體]。)
7. 在 [安裝詳細資料] 對話方塊中，按一下 [下一步]。
8. 在 [檢閱授權] 對話方塊中，檢閱授權合約的條款。如果您接受授權合約的條款，請按一下 [我接受授權合約的條款]，然後按一下 [完成]。(其餘的步驟假設您接受授權合約的條款。如果您不接受授權合約的條款，請結束安裝程序。)
9. 如果系統提示您重新啟動 Eclipse 以完成安裝，請按一下 [立即重新啟動]。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中為 Azure 建立 Hello World 應用程式][]

[Azure Toolkit for Eclipse 的新功能][]

如需如何搭配使用 Azure 與 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[在 Eclipse 中為 Azure 建立 Hello World 應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Web Platform Installer (WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838
[Azure Toolkit for Eclipse 的新功能]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_0114_2016-->