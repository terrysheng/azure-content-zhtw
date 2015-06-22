<properties 
   pageTitle="疑難排解 Operational Insights 問題"
   description="了解有關 Operational Insights 的問題疑難排解"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# 疑難排解 Operational Insights 問題
您可以使用下列各節中的資訊，幫助您疑難排解問題。如果您遇到的問題不在本文中，您可以嘗試 [Operational Insights 小組部落格](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## 診斷 Operational Insights 連線問題

由於 Microsoft Azure Operational Insights 依賴雲端來回移動的資料，因此連線問題可能有很大的影響。使用下列資訊來瞭解並解決連線問題。



<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>錯誤訊息</b></td>
		<td><b>可能的原因</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>已驗證網際網路連線，但無法建立至 Operational Insights 服務的連線。請稍後再試一次。|</td>
		<td>Operational Insights 服務正在進行維護。請等待 Operational Insights 維護完成。<p>您的網路已經封鎖 Operational Insights。請連絡網路系統管理員並要求存取 Operational Insights，或使用另一部伺服器做為您的閘道。</td>
    </tr>
    <tr align="left" valign="top">
		<td>無法建立網際網路連線。請檢查您的 Proxy 設定。</td>
		<td>此伺服器未連線至網際網路。檢查網際網路連線狀態，並將伺服器連線到網際網路。<p>Proxy 設定不正確。如需有關如何設定或變更 Proxy 設定的詳細資訊，請參閱<A HREF="operational-insights-proxy-filewall.md">設定 Proxy 和防火牆設定</A >。<p>Proxy 伺服器需要驗證。請參閱<A HREF="operational-insights-proxy-filewall.md">設定 Proxy 和防火牆設定</A >以了解如何設定 Operations Manager 來使用 Proxy 伺服器。</td>
    </tr>
    </tbody>
    </table>

## 疑難排解 SQL Server 探索

如果您執行 Microsoft SQL Server 2008 R2，且儘管部署 Operations Manager 代理程式，您仍看不到此伺服器的警示，則您可能遇到探索問題。

若要確認這是否為問題的來源，請檢查下列兩個問題：

- 在 Operations Manager 事件記錄檔中，您會看到事件識別碼 4001。這個事件表示有無效的類別。

- 在「SQL Server 組態管理員」中，當您檢視 SQL Server 服務時，您會看到錯誤訊息「遠端程序呼叫失敗。[0x0800706be]」

如果兩個問題都發生，則您需要安裝 SQL Server 2008 R2 Service Pack 2。若要下載此 Server Pack，請參閱 Microsoft 下載中心的 [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) 。

安裝 Service Pack 之後，您應該會在 24 小時內看到伺服器的 Operational Insights 資料。



<!--HONumber=52--> 