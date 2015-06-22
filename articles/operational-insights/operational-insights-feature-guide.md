<properties 
	pageTitle="Operational Insights 功能指南" 
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

<h1>Operational Insights 功能指南</h1>

本指南可協助您了解 Operational Insights 可協助您解決什麼問題、Operational Insights 環境組成項目，以及如何建立 Operational Insights 帳戶和登入服務。

<h2 id="whatisaad">什麼是 Operational Insights？</h2>

Operational Insights 是一項分析服務，可讓 IT 系統管理員取得跨內部部署和雲端環境的深入見解。它可讓您與即時和歷史電腦資料互動，以快速開發自訂見解，並提供 Microsoft 和社群開發的資料分析模式。

使用 Operational Insights，您可以將電腦資料轉換成營運情報。具體而言，您可以：

- 管理伺服器基礎結構的容量
- 使用系統更新來更新伺服器
- 了解伺服器記錄檔中的資料之間的關聯性
- 使用反惡意程式碼保護伺服器
- 以安全保障管理安全性風險
- 跨伺服器追蹤組態變更
- 最佳化 SQL Server
- 搜尋事件和 IIS 記錄檔
- 在 Microsoft Azure IaaS 虛擬機器上安裝代理程式
- 在不使用 Operations Manager 的情況下使用 Operational Insights  

<h2 id="">Operational Insights 環境</h2>

Operational Insights 環境組成項目包含：

- Microsoft Azure 代管的工作區，其為 Azure 帳戶的容器
- 裝載於雲端的 Operational Insights Web 服務
- 直接連接到 Web 服務的獨立代理程式
- 或是 System Center Operations 的附加服務，但不是必要服務




如果您使用的是稱為 System Center Advisor 的舊版 Operational Insights，您的本機環境中可能已安裝 Advisor 軟體。但是，Advisor 軟體無法搭配 Operational Insights 使用。

以 Operations Manager 服務形式使用 Operational Insights 軟體會包含一或多個管理群組，每一管理群組至少包含一個代理程式。Operations Manager 代理程式會使用智慧組件 (類似 System Center Operations Manager 中的管理組件) 收集您伺服器中的資料並進行分析。分析的資料會透過 Proxy 伺服器定期從 Operations Manager 傳送至 Operational Insights Web 服務，而且因為會繞過其他伺服器，對它們並不會造成額外負載。

同樣地，安裝在個別電腦上的代理程式可以直接連接到 Web 服務，以傳送收集的資料進行處理。

每個智慧組件中的資料都會經過分析，然後在 Operational Insights 入口網站中提供。您可以檢視任何警示和相關的補救措施、組態評估、基礎結構容量問題、系統更新狀態、反惡意程式碼警告及記錄檔資料。您也可以執行詳細的搜尋。

<h2 id="">建立 Operational Insights 帳戶並登入</h2>

使用下列資訊設定搭配 Microsoft Azure Operational Insights 使用的帳戶，並登入 Operational Insights。



<h3>取得 Organizational 或 Microsoft 帳戶</h3>

如果您想建立 Organizational 帳戶，請至 <a href="http://go.microsoft.com/fwlink/?LinkId=396866" target="_blank">組織的 Microsoft 帳戶</a>.接著，您必須在 <a href="http://aka.ms/Mr1dtz" target="_blank">Windows Azure Active Directory</a>.


如果您想取得 Microsoft 帳戶 (先前稱為 "Windows Live ID")，請至 <a href="http://go.microsoft.com/fwlink/?LinkId=396868" target="_blank">註冊 - Microsoft 帳戶</a>.存取權會透過 Microsoft 帳戶自動授與 Operational Insights。


<h3>登入 Operational Insights</h3>

1. 移至 <a href="preview.opinsights.azure.com" target="_blank">Microsoft Azure Operational Insights</a>  ，然後選擇頁面頂端的 [登入]。
2. 選取 [Microsoft 帳戶]**** 或 [組織帳戶]****，然後使用您的認證「登入」****。
3. 出現提示時，建立一個 Operational Insights 帳戶以關聯您的登入資訊。
4. 當系統提示您選取要使用的 Operational Insights 版本時，請選取 [嘗試預覽]****。
5. 如果出現提示，請輸入您的邀請代碼，然後按一下 [套用]**** 加入預覽。如果您沒有邀請代碼，請在畫面底部輸入您的資訊來取得。

<!--HONumber=45--> 
 