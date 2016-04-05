<properties
	pageTitle="如何將邏輯應用程式移轉至結構描述版本 2015-08-01-preview | Microsoft Azure App Service"
	description="您可以輕鬆地將邏輯應用程式移轉至最新的結構描述版本。請直接遵循下列步驟。"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/25/2016"
	ms.author="deonhe"/>

# 如何將邏輯應用程式移轉至結構描述版本 2015-08-01-preview

若要將現有的邏輯應用程式移至新的結構描述，請執行下列作業︰
1. 在 Azure 入口網站中開啟邏輯應用程式  
2. 按一下 [更新結構描述]︰

 ![API 圖示][step1][更新結構描述] 頁面便會出現，並提供會提供新結構描述增強功能詳細資訊之文件的連結︰![API 圖示][step2]

>[AZURE.NOTE] 當您選取 [更新結構描述] 時，我們會自動執行移轉步驟，並為您提供程式碼輸出。您可以使用此輸出更新您的定義，不過，請確定您有遵循良好的程式碼撰寫方式，例如下面的**最佳作法**一節中所說的方式。

##將邏輯應用程式移轉至最新結構描述版本時的最佳作法︰  
也不進行覆寫，開始移動至新的 API

- 將已移轉的指令碼複製到新的邏輯應用程式 - 在完成測試並確認移轉的應用程式已如預期般運作後再覆寫舊的邏輯應用程式。
- 放入生產環境**之前**先測試邏輯應用程式
- 移轉完成後，開始更新邏輯應用程式以盡可能使用 [Managed API](./apis-list.md)。例如，您可以在使用 DropBox v1 的地方開始使用 Dropbox v2。


## 後續步驟
-  [了解如何手動移轉邏輯應用程式](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png

<!---HONumber=AcomDC_0330_2016-->