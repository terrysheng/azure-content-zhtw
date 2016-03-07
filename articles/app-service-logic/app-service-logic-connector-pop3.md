<properties
   pageTitle="在 Logic Apps 中使用 POP3 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 POP3 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="sameerch"/>


# 開始使用 POP3 連接器並將它加入您的邏輯應用程式
>[AZURE.NOTE] 這一版文章適用於邏輯應用程式 2014-12-01-preview 結構描述版本。

連接到 POP3 伺服器擷取電子郵件，包括帶有附件的電子郵件。

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。您可以將 POP3 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。


## 建立邏輯應用程式的 POP3 連接器 ##
若要使用 POP3 連接器，您必須先建立 POP3 連接器 API 應用程式的執行個體。這可以直接從邏輯應用程式設計工具中進行，或在工具外進行。在設計工具外建立執行個體的作法如下：

1.	從 Azure 入口網站的首頁開啟 Azure Marketplace。
2.	在 [全部內容] 下搜尋「POP3 連接器」。
3.	設定 POP3 連接器，如下所示：

	![][1] 
	- **位置** - 選擇您要部署連接器的地理位置 
	- **訂用帳戶** - 選擇您要在其中建立此連接器的訂用帳戶 
	- **資源群組** - 選取或建立連接器所在的資源群組 
	- **Web 主控方案** - 選取或建立 Web 主控方案 
	- **定價層** - 選擇用於連接器的定價層 
	- **名稱** - 提供 POP3 連接器的名稱 
	- **封裝設定** 
		- **使用者名稱** 指定用來連線到 POP3 伺服器的使用者名稱 
		- **密碼** 指定用來連線到 POP3 伺服器的密碼 
		- **伺服器位址** 指定 POP3 伺服器名稱或 IP 位址 
		- **伺服器連接埠** 指定 POP3 伺服器連接埠號碼 
		- **啟用 SSL** 指定 true 以透過 SSL/TLS 安全通道使用 POP3
4.	按一下 [建立]。將建立新的 POP3 連接器。
5.	建立 API 應用程式執行個體後，您可以在相同的資源群組中建立邏輯應用程式，以便使用 POP3 連接器。

## 在邏輯應用程式中使用 POP3 連接器 ##
建立 API 應用程式之後，您現在可以使用 POP3 連接器做為邏輯應用程式的觸發程序。若要這樣做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 POP3 連接器的相同資源群組。

	![][2]
2.	開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的流程。

	![][3]
3.	POP3 接器就會出現在右側程式庫中的 [此資源群組中的 API 應用程式] 區段。選取該項目。

	![][4]
4.	您可以在 [POP3 連接器] 上按一下來將 POP3 連接器 API 應用程式置入編輯器。

5.	您現在便可以在流程中使用 POP3 連接器。選取 [取得電子郵件] 觸發程序，並設定頻率和時間間隔。您可以在流程的其他動作中使用從 POP3 觸發程序所擷取的電子郵件。
		 

	![][5]
	![][6]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)。


<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!---HONumber=AcomDC_0224_2016-->