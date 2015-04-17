<properties 
	pageTitle="什麼是連接器和 BizTalk API 應用程式" 
	description="了解 API 應用程式、連接器和 BizTalk API 應用程式" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="joshtwist" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="jtwist"/>

# 什麼是連接器和 BizTalk API 應用程式

Azure 應用程式服務是根據可擴充性和通用連線性的原則，透過 API 應用程式建立的。 *Connector*是一種著重於連線性的 API 應用程式。連接器 (例如任何其他 API 應用程式) 是從 Web 應用程式、行動應用程式和邏輯應用程式使用的。連接器可讓您輕鬆地連接到現有的服務並協助您管理驗證、提供監視、分析功能等等。

任何開發人員都可以建立自己的 API 應用程式並自行加以部署，且在未來可以透過 Marketplace 加以共用及銷售。 

![API Apps Marketplace](./media/app-service-learn-about-flows-preview/Marketplace.png)

為了加快開發人員透過 Azure 應用程式服務建置解決方案的速度，Microsoft Azure 團隊在 Marketplace 新增了多種連接器，以因應許多常見案例的需求。此外，為了將應用程式服務的功能觸及更複雜且進階的整合案例，我們也提供了許多高階和 BizTalk 功能。

[Microsoft Azure 應用程式服務中的連接器和 API 應用程式清單](app-service-logic-connectors-list.md)


## 通訊協定連接器
應用程式服務立基於 Web，適用於透過開放格式 (如 Swagger) 使用 HTTP 和共用中繼資料的通訊。當然，企業需要跨越各種通訊協定進行通訊。通訊協定連接器可以使用 FTP、SFTP、POP3/IMAP、SMTP 及 SOAP 服務消彌各種服務之間的差異並連繫各種服務，只要進行 HTTP 呼叫即可。  

[Microsoft Azure 應用程式服務中的通訊協定連接器](app-service-logic-protocol-connectors.md)


## SaaS 連接器
應用程式服務 SaaS 連接器提供周全的方法，可讓您將 Web、行動和邏輯應用程式連接到現今 SaaS 中最著名的某些服務，包括 Office 365、SalesForce、Sugar CRM、OneDrive、DropBox、Marketo、甚至 Facebook 等等。

[Microsoft Azure 應用程式服務中的社交連接器](app-service-logic-social-connectors.md)

[Microsoft Azure 應用程式服務中的應用程式 + 資料服務連接器](app-service-logic-data-connectors.md)


## 高階連接器 
高階連接器可透過 SAP、Siebel、Oracle、DB2 的連線，將應用程式服務進一步延伸至企業中。

[Microsoft Azure 應用程式服務中的企業連接器](app-service-logic-enterprise-connectors.md)


## BizTalk API 應用程式
建置業務關鍵應用程式時需要的不只是連線。以 Microsoft 領先業界的整合平台 BizTalk Server 做為基礎，BizTalk API 應用程式提供進階的整合功能，可以輕鬆地融入您的 Web、行動和邏輯應用程式中。包含對於 X12、EDIFACT 和 AS2 等 EDI 格式的批次處理和取消批次處理、VETR (驗證、擷取、轉換和路由傳送) 與支援。

[Microsoft Azure 應用程式服務中的企業對企業連接器和 API 應用程式](app-service-logic-b2b-connectors.md)<br/>

[Microsoft Azure 應用程式服務中的 BizTalk 整合 API 應用程式](app-service-logic-integration-connectors.md)

<!--HONumber=49-->