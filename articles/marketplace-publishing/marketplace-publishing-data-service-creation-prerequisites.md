<properties
   pageTitle="建立要發行到 Marketplace 之資料服務的技術必要條件 | Microsoft Azure"
   description="了解建立資料服務，以在 Azure Marketplace 中部署及銷售的的需求"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/30/2015"
   ms.author="hascipio; avikova" />

# 建立 Azure Marketplace 之資料服務產品的技術必要條件
開始之前，請先徹底閱讀程序，並且了解每個步驟執行的位置及原因。在供應項目建立程序之前，您應該盡可能準備您的公司資訊和其他資料、下載必要的工具，和/或建立技術元件。

開始程序之前，您應該先準備好下列項目：

## 決定要使用何種技術發行您的資料服務產品

在 Azure Marketplace 中發行資料服務時，發行者可以從多種技術中選擇所要使用的技術。主要技術支援如下所述。無論使用何種技術發行資料服務，使用者都會透過 Azure Marketplace 服務所公開的 **OData 摘要**使用服務。如需 OData 服務的完整資訊，請前往 [http://www.odata.org/](http://www.odata.org/)

## SQL Azure Database

發行者必須負責在 SQL Azure 中備妥資料集。您必須訂閱 Azure、佈建適當大小的資料庫，以及將您的資料上傳到 SQL Azure DB。發行者也必須負責將自己的資料保持在最新的狀態。如需訂閱各種 Azure 服務的詳細資訊，請參閱 [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


當將資料移至 SQL Azure 時，Azure Marketplace 可以公開資料表與檢視表。發行者可以要指定要對使用者公開的資料表/檢視表與資料行。內容提供者更可以指定使用者所能查詢的資料行，以及只有承載才會傳回的資料行。這為所要公開的資料庫資料提供高度的靈活性。可查詢的資料行必須由一或多個資料庫的索引所支援。

## 採用 REST 的 Web 服務

支援通訊協定：**僅 HTTPS**

現有 REST 服務可以透過 Azure Marketplace 公開。因為資料集一律會透過 OData 摘要公開給使用者，所以 Azure Marketplace 服務必須能夠將服務對應到 OData 服務。為達成此目的，REST 端點必須透過 HTTP 參數公開所有參數。

承載的格式必須能夠對應到 ATOM 回應。因此，服務的回應必須是 XML 格式，而且只可包含一個重複的元素 (內含記錄集一類的承載值)。Azure Marketplace 服務會將重複的節點對應到 ATOM 中的的項目節點，再將承載值對應用到該項目節點中的屬性節點。

授權資訊 (例如 API 金鑰、驗證權杖等等) 必須透過 HTTP 參數或在 HTTP 標頭 (機碼值組) 中提供，而基本驗證也可使用此法。必須提供有效的金鑰，而所有經由 Azure Marketplace 的要求都會由該金鑰發出。使用者監視與帳單會在 Azure Marketplace 層發生。

服務傳回的錯誤必須對應到 HTTP 狀態碼。若服務傳回的 XML 包含了錯誤，Azure Marketplace 服務會將其對應到 HTTP 狀態碼。

## 採用 SOAP 的 Web 服務

通訊協定：**僅 HTTPS**

這類服務的需求與 REST 服務一節所述相同。唯一差別是每一個透過 Azure Marketplace 所發出的要求，也可在要張貼到發行者服務的 XML 主體中提供參數。這表示使用者在前端提供的 HTTP 參數，將會在對內容提供者 Web 服務發出要求時，轉譯成所要張貼之 XML 文件的 XML 元素。

## 採用 OData 的 Web 服務

通訊協定：**僅 HTTPS**

資料可以在 Azure Marketplace 以OData 服務公開。系統會透過服務連接，並以 Azure Marketplace 服務的根目錄取代服務的根目錄，以確保後續的所有呼叫皆會透過 Azure Marketplace 服務的根目錄。

OData 服務不再只是需要仰賴後端的資料庫。OData 支援各種儲存體或商務邏輯，讓服務能夠順利運行。


## 後續步驟
至此，您已經檢閱了各項必要條件，而且也完成了各項必要工作，您可以依照[資料服務的發行指導](marketplace-publishing-data-service-creation.md)所述，繼續建立您的資料服務產品。

若您想要重新檢閱整個流程，以及每個發行階段的各篇文章，請參閱[快速入門：如何將產品發行到 Azure Marketplace](marketplace-publishing-getting-started.md) 一文。

[link-acct]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=AcomDC_1203_2015-->