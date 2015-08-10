<properties
   pageTitle="SMTP 連接器 API 應用程式"
   description="如何使用 SMTP 連接器"
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
   ms.date="07/01/2015"
   ms.author="andalmia"/>


# SMTP 連接器

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於工作流程一部分的資料。

POP3 連接器可讓您連接到 POP3 伺服器，並執行傳送有附件之電子郵件的動作。SMTP 連接器「傳送電子郵件」動作可讓您傳送電子郵件到指定的電子郵件位置。

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

## 在邏輯應用程式中使用 SMTP 連接器
建立連接器之後，即可使用 SMTP 連接器做為邏輯應用程式的動作。作法：

1.	建立新的邏輯應用程式：

	![][2]
2.	開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的工作流程：

	![][3]
3.	SMTP 連接器會列在右側資源庫中的 [此資源群組中的 API 應用程式] 區段。請選取它：

	![][4]
4.	選取 [SMTP 連接器]，自動將它加入工作流程設計工具。

您現在可以設定要在工作流程中使用的 SMTP 連接器。選取 [傳送電子郵件] 動作，並設定輸入屬性：

	屬性 | 說明
	--- | ---
	To | 輸入收件人的電子郵件地址。使用分號 (;) 分隔多個電子郵件地址。例如輸入： recipient1@domain.com;recipient2@domain.com。
	Cc | 輸入副本收件人的電子郵件地址。使用分號 (;) 分隔多個電子郵件地址。例如輸入：recipient1@domain.com;recipient2@domain.com。
	Subject | 輸入電子郵件主旨。
	Body | 輸入電子郵件內文。
	Is HTML | 當此屬性設為 true 時，內文內容會以 HTML 傳送。
	Bcc | 輸入密件副本收件人的電子郵件地址。使用分號 (;) 分隔多個電子郵件地址。例如輸入：recipient1@domain.com;recipient2@domain.com。
	Importance | 輸入電子郵件的重要性。選項為一般、低重要性和高重要性。
	Attachments | 附件會連同電子郵件一併寄出。包含的欄位如下： <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

	![][5]
	![][6]

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。請參閱[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

使用 REST API 建立 API 應用程式。請參閱[連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。請參閱[管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=July15_HO5-->