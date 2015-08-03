<properties   
	pageTitle="BizTalk Server EDI 解決方案移轉至 BizTalk 服務：技術指南"
	description="將 EDI 移轉到 MABS；Microsoft Azure BizTalk 服務"
	services="biztalk-services"
	documentationCenter="na"
	authors="MandiOhlinger"
	manager="dwrede"
	editor="dwrede"/>

<tags 
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="mandia"/>


# BizTalk Server EDI 解決方案移轉至 BizTalk 服務：技術指南

作者：Tim Wieman 和 Nitin Mehrotra

審稿者：Karthik Bharthy

撰寫工具：Microsoft Azure BizTalk 服務 – 2014 年 2 月版本。

## 簡介

電子資料交換 (EDI) 是企業是以電子方式交換資料的其中一個最普遍的方法，也稱為「企業對企業」或 B2B 交易。自初始的 BizTalk Sever 版本以來，BizTalk Server 已經支援 EDI 超過十年。使用 BizTalk 服務，Microsoft 會持續在 Microsoft Azure 平台上支援 EDI 解決方案。B2B 交易大部分在組織外部進行，因此，它更容易在雲端平台上實作。Microsoft Azure 透過 BizTalk 服務提供這項功能。

雖然某些客戶將 BizTalk 服務視為新 EDI 解決方案的「綠地」平台，但目前擁有 BizTalk Server EDI 解決方案的許多客戶都可能會想要移轉至 Azure。因為 BizTalk 服務 EDI 是根據 BizTalk Server EDI 的相同關鍵實體所架構 (交易夥伴、實體、合約)，因此可將 BizTalk Server EDI 構件移轉至 BizTalk 服務。

本文件探討將 BizTalk Server EDI 構件移轉至 BizTalk 服務的一些差異。本文件假設讀者擁有 BizTalk Server EDI 處理和交易夥伴合約的實用知識。如需有關 BizTalk Server EDI 的詳細資訊，請參閱[使用 BizTalk Server 的交易夥伴管理](https://msdn.microsoft.com/library/bb259970.aspx)。

## 哪個版本的 BizTalk Server EDI 構件可移轉至 BizTalk 服務？

BizTalk Server 2010 的 BizTalk Server EDI 模組已大幅增強，因為它重塑為包含夥伴、設定檔與合約。BizTalk 服務會使用相同的模型來組織交易夥伴，和這些交易夥伴內的業務部門。如此一來，從 BizTalk Server 2010 和更新版本將 EDI 構件移轉至 BizTalk 服務，是直接簡單得多的程序。若要移轉 BizTalk Server 2010 之前的版本與相關聯的 EDI 構件，必須先升級至 BizTalk Server 2010，然後再將 EDI 構件移轉至 BizTalk 服務。

## 案例/訊息流程

做為 BizTalk Server，BizTalk 服務中的 EDI 處理是使用交易夥伴管理 (TPM) 方案所建置。TPM 方案具有下列主要元件：

- 交易夥伴，代表 B2B 交易中的組織。
- 設定檔，代表交易夥伴內的部門。
- 交易夥伴合約 (或艒稱「合約」)，代表兩個夥伴/設定檔之間的商務合約。

下圖說明相似之處，以及 BizTalk Server EDI 解決方案與 BizTalk 服務 EDI 方案之間的差異：

![][EDImessageflow]

BizTalk Server 與 BizTalk 服務之間的 EDI 方案流程之間的主要差異與相似之處如下：

- 如同 BizTalk Server 會使用 EDIReceive 管線來接收 EDI 訊息，並使用 EDISend 管線傳送 EDI 訊息，BizTalk 服務會使用 EDI 接收橋接器接收，且 EDI 傳送橋接器會傳送 EDI 訊息。在 BizTalk Server 中，會使用傳送或接收埠來建立管線與合約的關聯。在 BizTalk 服務中，合約本身指定傳送或接收橋接器。
- 在 BizTalk Server 中，在 EDIReceive 管線處理 EDI 訊息後，會將訊息傾印至 SQL Server 資料庫。EdiSend 管線接著從 SQL Server 資料庫收取訊息、處理它，然後將它傳送給交易夥伴。

	在 BizTalk 服務 中，在 EDI 接收橋接器處理 EDI 訊息後，它會將訊息傳送至外部處理序。外部處理序無法在 Microsoft Azure 或內部部署上執行。外部處理序應該將訊息路由傳送至 EDI 傳送橋接器；傳送橋接器本質上不會提取訊息。處理訊息後，EDI 傳送橋接器會將訊息傳送給交易夥伴。

BizTalk 服務提供方便使用的組態經驗來快速建立及部署交易夥伴之間的 B2B 合約，而不需設定任何 Microsoft Azure 計算執行個體 (Web 或背景工作角色)、任何 Microsoft Azure SQL 資料庫或任何 Microsoft Azure 儲存體帳戶。更複雜的案例需要繫結工作流程，或處理交易夥伴合約之「邊緣」的其他服務，也就是在交易夥伴合約 EDI 橋接器處理之前或之後。更詳細說，在 BizTalk 服務中處理 EDI 訊息期間會發生下列事件序列。

1. 從交易夥伴 Fabrikam 接收 EDI 訊息。從交易夥伴接收 EDI 訊息，BizTalk 服務可支援傳輸通訊協定，例如 FTP、SFTP、AS2 和 HTTP/S。

2. 交易夥伴合約接收端處理程序可將 EDI 訊息解譯為 XML 格式。您可以將解譯的 EDI 訊息 (XML 格式) 路由傳送至服務匯流排端點，例如服務匯流排轉送端點、服務匯流排主題、服務匯流排佇列或 BizTalk 服務橋接器。

3. 接著可能會從端點收到解譯的 XML 訊息以進一步自訂處理。例如，這些端點可由內部部署元件或 Microsoft Azure 計算執行個體處理，以進一步在 Windows Workflow (WF) 或 Windows Communication Foundation (WCF) 服務中處理訊息。

4. 交易夥伴合約的「傳送端處理」接著會將 XML 訊息組合成 EDI 格式，並傳送至交易夥伴 Contoso。若是傳送 EDI 訊息給交易夥伴，BizTalk 服務可支援與用來接收 EDI 訊息的相同通訊協定。

本文將進一步提供概念性指引，將部分不同的 BizTalk Server EDI 構件移轉至 BizTalk 服務。

## 至交易夥伴的傳送/接收埠

在 BizTalk Server 中，您設定接收位置和接收埠來接收交易夥伴的 EDI/XML 訊息，而且您已設定傳送埠將 EDI/XML 訊息傳送至交易夥伴。您接著將使用 BizTalk Server 管理主控台，將這些連接埠繫結至交易夥伴合約。在 BizTalk 服務中，會將從交易夥伴接收訊息的位置，和您傳送訊息給交易夥伴的位置，設定為交易夥伴合約本身 (做為傳輸設定的一部分) 在 BizTalk 服務入口網站的一部分。所以您本身其實沒有 BizTalk 服務中的「傳送埠」和「接收位置」概念。如需詳細資訊，請參閱[建立協議](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx)。

## 管線 (橋接器)

在 BizTalk Server EDI 中，管線是也可以包含特定處理能力的自訂邏輯之訊息處理實體 (應用程式所需)。BizTalk 服務的對等項目則是 EDI 橋接器。不過在目前的 BizTalk 服務中，EDI 橋接器會「關閉」。也就是說，您無法將您自己的自訂活動加入至 EDI 橋接器。任何自訂的處理必須在應用程式的 EDI 橋接器外部完成，且必須在訊息進入交易夥伴合約中設定的橋接器之前或之後。EAI 橋接器可選擇執行自訂處理。如果您想要自訂處理，您可以在 EDI 橋接器處理訊息之前或之後使用 EAI 橋接器。如需詳細資訊，請參閱[如何將自訂程式碼加入橋接器中](https://msdn.microsoft.com/library/azure/dn232389.aspx)。

您可以在交易夥伴合約接收訊息之前，或在合約處理訊息，並將它傳遞到服務匯流排端點後，使用自訂程式碼和/或使用服務匯流排訊息佇列和主題插入發佈/訂閱流程。

如需訊息流程模式的詳細資訊，請參閱本主題的**案例/訊息流程**。

## 合約

如果您熟悉用於 EDI 處理的 BizTalk Server 2010 交易夥伴合約，則 BizTalk 服務交易夥伴合約看起來會很熟悉。大部分的合約設定相同，而且使用相同的詞彙。在某些情況下，相較於 BizTalk Server 中的相同設定，合約設定會簡單許多。Microsoft Azure BizTalk 服務支援 X12、EDIFACT 和 AS2 傳輸。

Microsoft Azure BizTalk 服務也提供 **TPM 資料移轉**工具，可將交易夥伴和合約從 BizTalk Server 交易夥伴模組移轉至 BizTalk 服務入口網站。TPM 資料移轉工具是工具套件的一部分，它可以從 [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057) 下載。此封裝也包含一個 readme 檔案，提供如何使用工具的指示，以及工具基本的疑難排解資訊。

## 結構描述

BizTalk 服務提供可在 BizTalk 服務方案中使用的 EDI 結構描述。此外，BizTalk Server EDI 結構描述也可與 BizTalk 服務搭配使用，因為 EDI 結構描述的根節點跨 BizTalk Server 與 BizTalk 服務相同。因此，您將能夠直接取得您的 BizTalk Server EDI 結構描述，並在您使用 BizTalk 服務開發的 EDI 解決方案中使用它們。您也可以從 [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057) 下載結構描述。

## 對應 (轉換)

BizTalk Server 中的對應在 BizTalk 服務中稱為「轉換」。從 BizTalk Server 將對應移轉到 BizTalk 服務可能是其中一個更複雜的工作 (取決於對應複雜性)。BizTalk 服務所使用的對應程式與 BizTalk 對應程式不同。即使對應程式看起來幾乎相同，基本對應格式也不同。使用者的可用運算質 (稱為 BizTalk 服務中的**對應作業**) 也有不同。事實上，您無法在 BizTalk 服務中直接使用 BizTalk 對應。此外，並非所有 BizTalk Server 中提供的運算質都可做為 BizTalk 服務中的對應作業。

### 新的轉換作業

雖然可用的轉換對應作業清單似乎與 BizTalk Server 對應程式相當不同，但 BizTalk 服務轉換有新方法來完成相同工作。例如，BizTalk 服務轉換有**清單作業**可用。這不是在 BizTalk 對應程式中使用。**清單作業**可讓您建立及操作「清單」，清單是一組項目 (也稱為「資料列」)，而且每個項目可以有多個成員 (也稱為「資料行」)。您可以排序清單、根據條件選取項目等等。

BizTalk 服務轉換的其他新功能範例是**迴圈作業**。很難在 BizTalk Server 對應程式中建立巢狀迴圈。因此，會為 BizTalk 服務轉換加入迴圈對應作業。

但另一個範例是 **If-Then-Else** 運算式對應作業。可以在 BizTalk 對應程式中執行 if-then-else 作業，但是它需要多個運算質來完成一個看似簡單的工作。

### 移轉 BizTalk Server 對應

Microsoft Azure BizTalk 服務提供工具，可將 BizTalk Server 移轉至 BizTalk 服務轉換。**BTMMigrationTool** 可屬於 BizTalk 服務 SDK 提供的**工具**封裝，且可以從[這裡](http://go.microsoft.com/fwlink/p/?LinkId=235057)下載。如需有關此工具的詳細資訊，請參閱[將 BizTalk 對應轉換成 BizTalk 服務轉換](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx)。

您也可以查看 Sandro Pereira 提供的範例 (BizTalk MVP)，了解如何將 BizTalk Server 對應移轉至 BizTalk 服務轉換。該範例可從[這裡](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx)取得。

## 協調流程

如果您需要將 BizTalk Server 協調流程移轉至 Microsoft Azure，協調流程需要重寫，因為 Microsoft Azure 不支援 BizTalk Server 協調流程的執行。您可以在 Windows Workflow Foundation 4.0 (WF4) 服務中重寫協調流程功能。這會完全重寫，因為目前沒有從 BizTalk Server 協調流程至 WF4 的移轉。以下是 Windows 工作流程的一些資源：

- 由 Paolo Salvatori 撰寫的*如何整合 WCF 工作流程服務與服務匯流排佇列和主題*。請參閱[這裡](https://msdn.microsoft.com/library/azure/hh709041.aspx)。

- 從 Build 2011 會議*使用 Windows Workflow Foundation 和 Azure 建置應用程式*工作階段。請參閱[這裡](http://go.microsoft.com/fwlink/p/?LinkId=237314)。

- *MSDN 上的 Windows Workflow Foundation 開發人員中心*。請參閱[這裡](http://go.microsoft.com/fwlink/p/?LinkId=237315)。

- *MSDN 上的 Windows Workflow Foundation 4 (WF4) 文件*。請參閱 [這裡](https://msdn.microsoft.com/library/dd489441(v=vs.100).aspx)。

## 其他考量

以下是您在使用 BizTalk 服務時必須考量的一些事項。

### 後援合約

BizTalk Server EDI 處理程序有「後援合約」概念。BizTalk 服務至今為止**沒有**「後援合約」概念。如需如何在 BizTalk Server 中使用後援合約的詳細資訊，請參閱 BizTalk 文件主題 [EDI 處理程序中的合約角色](http://go.microsoft.com/fwlink/p/?LinkId=237317) (英文) 和[設定全域或後援協議屬性](https://msdn.microsoft.com/library/bb245981.aspx)。

### 路由至多個目的地

BizTalk 服務橋接器目前不支援使用發佈-訂閱模型，將訊息路由至多個目的地。您可以轉為將訊息從 BizTalk 服務橋接器路由到服務匯流排主題，如此即可有多個訂閱會接收位於多個端點的訊息。

## 結論

Microsoft Azure BizTalk 服務會定期更新以新增更多特性和功能。每次更新時，我們期望能支援更的多功能，以方便使用 BizTalk 服務與其他 Azure 技術建立端對端解決方案。

## 另請參閱

[使用 Azure 開發企業應用程式](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png

<!---HONumber=July15_HO4-->