<properties urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="混合式連線概觀 | Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="Learn about hybrid connections, including Security." metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="mandia" />



# 混合式連線概觀
本主題簡介混合式連線、列出支援的組態，並列出需要的 TCP 連接埠。具體而言：

- [什麼是混合式連線](#HCOverview)
- [支援的組態](#KnownIssues)
- [安全性和連接埠](#HCSecurity)

##<a name="HCOverview"></a>什麼是混合式連線

混合式連線提供簡單又方便的作法，讓您將 Azure 網站和 Azure 行動服務連接到內部部署資源。混合式連線是 Azure BizTalk 服務的一項功能：

![Hybrid Connections][HCImage]

混合式連線的優點包括：

- 網站和行動服務可以安全地存取現有的內部部署資料和服務。
- 多個網站或行動服務可以共用一個混合式連線來存取內部部署資源。 
- 只需要最少的 TCP 連接埠就能存取您的網路。
- 使用混合式連線的應用程式只存取透過混合式連線發佈的特定內部部署資源。
- 可以連接到任何使用靜態 TCP 連接埠的內部部署資源，例如 SQL Server、MySQL、HTTP Web API 和大部分的自訂 Web 服務。

> [WACOM.NOTE] 目前不支援使用動態連接埠的 TCP 服務 (例如 FTP 被動模式或延伸被動模式)。

- 可以與 Azure 網站 (.NET、PHP、Java、Python、Node.js) 和 Azure 行動服務 (Node.js、.NET) 支援的所有架構一起使用。
- 網站或行動服務可以像是位於本機網路一樣，以完全相同的方式來存取內部部署資源。例如，內部部署使用的相同連接字串也可以在 Azure 上使用。


混合式連線也可讓企業系統管理員控制和檢視混合式應用程式所存取的公司資源，包括：

- 系統管理員可以使用群組原則設定，以允許在網站上使用混合式連線，也能指定混合式應用程式可存取的資源。
- 公司網路上的事件和稽核記錄可讓您查看混合式連線所存取的資源。


##<a name="KnownIssues"></a>支援的組態

混合式連線支援下列架構和應用程式的組合：

- .NET 架構存取 SQL Server
- .NET 架構搭配 WebClient 存取 HTTP/HTTPS 服務
- PHP 存取 SQL Server、MySQL
- Java 存取 SQL Server、MySQL 和 Oracle
- Java 存取 HTTP/HTTPS 服務

使用混合式連線來存取內部部署 SQL Server 時，請注意下列事項：

- SQL Express 具名執行個體必須設定為使用靜態連接埠。依預設，SQL Express 具名執行個體是使用動態連接埠。
- SQL Express 預設執行個體使用靜態連接埠，但必須啟用 TCP。預設不會啟用 TCP。
- 使用群集或可用性群組時，目前不支援 MultiSubnetFailover=true 模式。
- 目前不支援 ApplicationIntent=ReadOnly。
- 可能需要 SQL 驗證當做 Azure 應用程式和內部部署 SQL Server 所支援的端對端授權方法。


##<a name="HCSecurity"></a>安全性和連接埠

混合式連線採用共用存取簽章 (SAS) 授權，以保護從 Azure 應用程式和內部部署混合式連線管理員至混合式連線的連線安全。針對應用程式和內部部署混合式連線管理員，系統會建立個別的連線金鑰。這些連線金鑰可以個別地更換和撤銷。

混合式連線能夠以連貫和安全的方式，將金鑰配送給應用程式和內部部署混合式連線管理員。 

請參閱 [建立和管理混合式連線](http://azure.microsoft.com/zh-tw/documentation/articles/integration-hybrid-connection-create-manage) (英文)。 

**應用程式授權與混合式連線分開**。任何適當的授權方法都可使用。授權方法視 Azure 雲端和內部部署元件之間支援的端對端授權方法而定。例如，您的 Azure 應用程式存取內部部署 SQL Server。在此情況下，SQL 授權可能是端對端支援的授權方法。

####TCP 連接埠
混合式連線只需要您私人網路的輸出 TCP 或 HTTP 連線。您不需要開啟任何防火牆連接埠，或變更您的網路周邊組態，即可允許任何輸入連線進入您的網路。

混合式連線會使用下列 TCP 通訊埠：

<table border="1">
    <tr>
       <th><strong>連接埠</strong></th>
        <th>理由</th>
    </tr>
    <tr>
        <td>80</td>
        <td>HTTP 連接埠；用於憑證驗證。</td>
    </tr>
    <tr>
        <td>443</td>
        <td>HTTPS 連接埠</td>
    </tr>
	<tr>
        <td>5671</td>
        <td>用來連接到 Azure。如果 TCP 連接埠 5671 無法使用，則使用 TCP 連接埠 443。</td>
	</tr>
	<tr>
        <td>9352</td>
        <td>用來推送和提取資料。如果 TCP 連接埠 9352 無法使用，則使用 TCP 連接埠 443。</td>
	</tr>
</table>


## 下一步

- [建立和管理混合式連線](http://azure.microsoft.com/zh-tw/documentation/articles/integration-hybrid-connection-create-manage)
- [將 Azure 網站連接到內部部署資源](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [混合式連線逐步說明：從 Azure 網站連接至內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Azure 行動服務和混合式連線](http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)


## 另請參閱

- [用於管理 Azure 上之 BizTalk 服務的 REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk 服務：儀表板、監視器和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/> (英文)

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
