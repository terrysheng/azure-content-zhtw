<properties 
	pageTitle="Operational Insights 安全性" 
	description="Operational Insights 是一項分析服務，可讓 IT 系統管理員取得跨內部部署和雲端環境的深入見解。它可讓您與即時和歷史電腦資料互動，以快速開發自訂見解，並提供 Microsoft 和社群開發的資料分析模式。" 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>





<h1 id="">Operational Insights 安全性</h1>


Microsoft 致力於保護您的隱私權和保護資料安全，同時提供軟體和服務，幫助您管理組織的 IT 基礎結構。我們了解當您將資料委託給他人管理時，將需要嚴格的安全性。Microsoft 從撰寫程式碼到運作服務均遵守嚴格的規範與安全性指導方針。

保全和保護資料是在 Microsoft 的第一要務。如有任何問題、建議或關於下列任一項資訊的問題 (包括我們的安全性原則)，請與我們連絡： <a href="mailto:scdata@microsoft.com" target="_blank">scdata@microsoft.com</a>。

本文說明 Microsoft Azure Operational Insights 中的資料收集、處理和保全方式。您可以使用任一代理程式直接連接到 Web 服務，或可使用 System Center Operations Manager 收集 Operational Insights 服務的作業資料。收集的資料是透過網際網路傳送至裝載於 Microsoft Azure 的 Operational Insights 服務。

Operational Insights 服務會使用下列方法安全地管理您的資料：

**資料隔離：**客戶資料以邏輯方式分開儲存整個作業的見解服務每個元件上。每個組織加上標記的所有資料。這項標記作業在整個資料生命週期持續發生，它會強制執行服務的每個層級。 

每個客戶都有專用的 Azure Blob 可裝載長期資料。Blob 是以每個客戶唯一的金鑰加密，而且每 90 天會變更一次。

**資料保留：**每個智慧組件的彙總度量會儲存在 Microsoft Azure 代管的 SQL Database。這項資料會儲存 390 天。已編制索引的搜尋資料在清理前平均會儲存 10 天。如果提前達到每個資料類型 2 千萬筆記錄的上限，則 Operational Insights 不到 10 天就會先清理資料。如果 10 天還未達到資料限制，Operational Insights 會等到達到限制後再清理。

**實體安全性：**Operational Insights 服務是由 Microsoft 人員操縱，所有活動都有記錄並且可供稽核。Operational Insights 服務是完全在 Azure 中執行，並符合 Azure 通用工程準則。您可以在  <a href="http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf" target="_blank">Microsoft Azure 安全性概觀的第 18 頁</a>.

**規範與認證：**在 Operational Insights 服務正式推出前，Operational Insights 軟體開發和服務小組會積極地與 Microsoft 法律和規範小組及其他產業合作夥伴合作取得各種不同的認證 (包括 ISO)。

我們目前符合下列安全性標準：

- Windows 通用工程準則
- Microsoft 高可信度電腦運算認證



<!--HONumber=45--> 
 