<properties
   pageTitle="Azure 資源總管 | Microsoft Azure"
   description="說明 Azure 資源總管，以及說明如何透過 Azure Resource Manager 使用資源總管來檢視及更新部署。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="stuartle;tomfitz"/>

# 使用 Azure 資源總管來檢視及修改資源
[Azure 資源總管](https://resources.azure.com)是很棒的工具，可用來查看您已經在訂用帳戶中建立的資源。您可以使用此工具來了解資源結構，以及查看指派給每項資源的屬性。您可以了解可供資源類型使用的 REST API 操作和 PowerShell Cmdlet，而且您可以透過介面發出命令。在您建立資源管理員範本時，資源總管會特別好用，因為它可以讓您檢視現有資源的屬性。

[github](https://github.com/projectkudu/ARMExplorer) 上有提供資源總管工具的來源，如果您需要在您的應用程式中實作類似的行為，其中提供了很有價值的參考。

## 檢視資源
瀏覽至 [https://resources.azure.com](https://resources.azure.com)，並使用和您登入 [Azure 入口網站](https://portal.azure.com)時相同的認證登入。

載入之後，左邊的樹狀檢視可讓您深入了解您的訂用帳戶和資源群組：

![樹狀檢視](./media/resource-manager-resource-explorer/are-01-treeview.png)

在您深入了解資源群組時，將會看到該群組中有資源可用的提供者：

![提供者](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

您可以在那裡開始深入了解資源執行個體。在下面的螢幕擷取畫面中，您可以在樹狀檢視中看到 `sltest` SQL Server 執行個體。您可以在右邊看到可和該資源搭配使用之 REST API 要求的相關資訊。透過瀏覽至資源的節點，資源總管會自動發出 GET 要求來接收資源的相關資訊。在 URL 下方的大型文字區域中，您將會看到來自 API 的回應。

在您熟悉 ARM 範本之後，就會開始覺得文本內容看起來很熟悉！ 回應的 [屬性] 區段與您可在範本的[屬性] 區段中提供的值相符。

![sql 伺服器](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

資源總管可讓您持續深入探索子資源，在使用 SQL Database 伺服器的情況下，像是資料庫與防火牆規則這樣的項目會有子資源。

探索資料庫可讓我們知道該資料庫的屬性。在下面的螢幕擷取畫面中，我們可以看到資料庫 `edition` 為 `Standard`，而 `serviceLevelObjective` (或資料庫層) 為 `S1`。

![sql 資料庫](./media/resource-manager-resource-explorer/are-04-database-get.png)

## 變更資源

在您可以瀏覽至資源時，可以選取 [編輯] 按鈕來讓 JSON 內容可供編輯。然後您可以使用資源總管來編輯 JSON，並傳送 PUT 要求來變更資源。例如，下面的影像顯示資料庫層已變更為 `S0`：

![資料庫 - PUT 要求](./media/resource-manager-resource-explorer/are-05-database-put.png)

透過選取 **PUT**，您就可以提交要求。

提交要求之後，資源總管就會重新發出 GET 要求來重新整理狀態。此時我們可以看到 `requestedServiceObjectiveId` 已經更新，且和指示正在進行調整作業的 `currentServiceObjectiveId` 不同。您可以按一下 GET 按鈕來手動重新整理狀態。

![資料庫 - GET 要求 2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## 在資源上執行動作

[動作] 索引標籤可讓您查看及執行其他 REST 操作。例如，當您已經選取網站資源時，[動作] 索引標籤會顯示含有一長串可用操作的清單，下面顯示了其中一部份。

![Web - POST 要求](./media/resource-manager-resource-explorer/are-web-post.png)

## 透過 PowerShell 叫用 API
資源總管中的 [PowerShell] 索引標籤會顯示用來和您目前探索之資源互動的 Cmdlet。根據您已經選取的資源類型而定，顯示的 PowerShell 指令碼範圍可涵蓋簡單的 Cmdlet (例如 `Get-AzureRmResource` 和 `Set-AzureRmResource`) 到較複雜的 Cmdlet (例如網站上的交換位置)。

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

如需 Azure PowerShell Cmdlet 的詳細資訊，請參閱[將 Azure PowerShell 與 Azure Resource Manager 搭配使用](./powershell-azure-resource-manager.md)。

## 摘要
與 ARM 搭配使用時，資源總管可以成為非常有用的工具。這是用來探索如何使用 PowerShell 來查詢及變更的好方法。如果您正在使用 REST API，這是在您開始撰寫程式碼之前開始使用及快速測試 API 的好方法。以及如果您正在撰寫 ARM 範本，這可以是了解資源階層及尋找組態放置位置的好方法 - 您可以在入門網站中變更，然後在資源總管中尋找相對應的項目。

如需詳細資訊，請參閱 [Channel 9 video with Scott Hanselman and David Ebbo (Channel 9 中 Scott Hanselman 和 David Ebbo 的影片)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)

<!---HONumber=AcomDC_0309_2016-->