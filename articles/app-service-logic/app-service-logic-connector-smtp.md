<properties
   pageTitle="在 Logic Apps 中使用 SMTP 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 SMTP 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="andalmia"/>


# 開始使用 SMTP 連接器並將它加入您的邏輯應用程式
連線至 SMTP 伺服器並傳送電子郵件，包括帶有附件的電子郵件。SMTP 連接器「傳送電子郵件」動作可讓您傳送電子郵件到指定的電子郵件位置。

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於工作流程一部分的資料。您可以將 SMTP 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。


## 觸發程序和動作
*觸發程序*是發生的事件。例如，訂單更新時或加入新客戶時。*動作*是觸發程序的結果。例如，更新訂單或加入新客戶時，傳送電子郵件給新客戶。

SMTP 連接器可以在邏輯應用程式中用作觸發程序或動作，且支援 JSON 和 XML 格式的資料。這個連接器目前沒有觸發程序。

SMTP 連接器提供下列觸發程序和動作：

觸發程序 | 動作
--- | ---
None | 傳送電子郵件


## 建立 SMTP 連接器
連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 [**Marketplace**]。
2. 選取 [API 應用程式]，然後搜尋「SMTP 連接器」。
3. **建立**連接器。
4. 輸入名稱、App Service 方案和其他屬性。
5. 輸入下列封裝設定：

	名稱 | 必要 | 說明
	--- | --- | ---
	使用者名稱 | 是 | 輸入可以連接到 SMTP 伺服器的使用者名稱。
	密碼 | 是 | 輸入使用者名稱密碼。
	伺服器位址 | 是 | 輸入 SMTP 伺服器名稱或 IP 位址。
	伺服器連接埠 | 是 | 輸入 SMTP 伺服器連接埠號碼。
	啟用 SSL | 否 | 輸入 *true* 以透過 SSL/TLS 安全通道使用 SMTP。

6. 選取 [**建立**]。

> [AZURE.IMPORTANT]某些 SMTP 伺服器在此連接器的運作方式 (SendGrid 和 Gmail) 可能會有問題。如果您想要從 SendGrid 傳送郵件，則我們的 [GitHub 儲存機制](https://github.com/logicappsio/SendGridAPI)具有自訂 API，其可直接與 SendGrid API 整合。

## 在邏輯應用程式中使用 SMTP 連接器
建立連接器之後，即可使用 SMTP 連接器做為邏輯應用程式的動作。作法：

1.	建立新的邏輯應用程式：![][2]
2.	開啟 [觸發程序和動作] 以開啟 Logic Apps 設計工具，並設定您的工作流程：![][3]
3.	SMTP 連接器會列在右側資源庫中的 [此資源群組中的 API 應用程式] 區段。請選取它：![][4]
4.	選取 [SMTP 連接器]，自動將它加入工作流程設計工具。

您現在可以設定要在工作流程中使用的 SMTP 連接器。選取 [傳送電子郵件] 動作，並設定輸入屬性：

	Property | Description
	--- | ---
	To | Enter the email address of recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Enter the email address of the carbon copy recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Enter the subject of the email.
	Body | Enter body of the email.
	Is HTML | When this property is set to true, the contents of the body are sent as HTML.
	Bcc | Enter the email address of recipient(s) for blind carbon copy. Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Enter the Importance of the email. The options are Normal, Low, and High.
	Attachments | Attachments to be sent along with the email. It contains the following fields: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

![][5] ![][6]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是 Logic Apps？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

檢視位於[連接器和 API Apps 參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)的 Swagger REST API 參考。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱〈[管理和監視內建 API Apps 和連接器](app-service-logic-monitor-your-connectors.md)〉。

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=Oct15_HO3-->