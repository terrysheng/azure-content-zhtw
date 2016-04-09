<properties
	pageTitle="建立 Azure Batch 帳戶 | Microsoft Azure"
	description="了解如何在 Azure 入口網站中建立 Azure Batch 帳戶，以在雲端中執行大規模的平行工作負載"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="marsma"/>

# 在 Azure 入口網站中建立和管理 Azure Batch 帳戶

> [AZURE.SELECTOR]
- [Azure 入口網站](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

[Azure 入口網站][azure_portal]提供了建立和管理 Azure Batch 帳戶所需的工具，您可將這些工作用於大規模的平行工作負載處理。在本文中，我們會利用入口網站逐步說明如何建立 Batch 帳戶，以及討論 Batch 帳戶最重要的幾項設定與屬性。例如，您利用 Batch 開發的應用程式和服務需要您帳戶的 URL 和存取金鑰 (在 Azure 入口網站中可找到這兩項) 才能與 Batch 服務 API 進行通訊。

>[AZURE.NOTE] Azure 入口網站目前支援 Batch 服務所提供的部分功能，包括建立帳戶及管理帳戶設定和屬性。開發人員可透過 Batch API 取得 Batch 的完整功能 (包含建立及執行作業和工作)。

## 建立批次帳戶：

1. 登入 [Azure 入口網站][azure_portal]。

2. 按一下 [**新增**] > [**計算**] > [**Batch 服務**]。

	![Marketplace 中的批次][marketplace_portal]

3. 在 [Batch 服務] 刀鋒視窗上檢閱資訊，然後按一下 [建立]。請注意，已停用部署模型選取功能。這是因為 Batch 僅使用資源群組部署模型。

	![Azure 入口網站中的 Batch 服務建立刀鋒視窗][3]

4. [新增 Batch 帳戶] 刀鋒視窗隨即出現。請參閱以下的項目 a 到 e*e*，以取得每個刀鋒視窗元素的說明。

    ![建立批次帳戶：][account_portal]

	a.帳戶名稱 -- 為 Batch 帳戶指定唯一的名稱。此名稱必須是建立帳戶的 Azure 區域內的唯一名稱 (請參閱下面的「位置」)。它只能包含小寫字元、數字，且長度必須為 3-24 個字元。

	b.訂用帳戶 -- 選取要在其中建立 Batch 帳戶的訂用帳戶。如果您只有一個訂用帳戶，則預設會選取此項目。

	c.資源群組 -- 選取新的 Batch 帳戶的資源群組，如果您的訂用帳戶中沒有資源群組，則建立一個新的。

	d.位置 -- 選取要在其中建立 Batch 帳戶的 Azure 區域。只有您的訂用帳戶和資源群組所支援的區域會顯示為選項。

    e.儲存體帳戶 (選用) -- 您可以將儲存體帳戶關聯 (連結) 至新的 Batch 帳戶。Batch 的[應用程式封裝](batch-application-packages.md)會在應用程式封裝的儲存和擷取作業中使用連結的儲存體帳戶。如需此功能的詳細資訊，請參閱[使用 Azure Batch 應用程式封裝部署應用程式](batch-application-packages.md)。

     > [AZURE.TIP] 在連結的儲存體帳戶中重新產生金鑰需要特殊的考量。如需詳細資訊，請參閱以下 [ Batch 帳戶的考量](#considerations-for-batch-accounts)。

5. 按一下 [建立] 來建立帳戶。

  入口網站會表示它正在「部署」帳戶，而 [Batch 帳戶] 刀鋒視窗會在完成時顯示。

## 檢視 Batch 帳戶屬性

Batch 帳戶刀鋒視窗會顯示帳戶的數個屬性，以及可供存取額外的設定，例如存取金鑰、配額、使用者和儲存體帳戶關聯。

* Batch 帳戶 URL -- 當您使用 [Batch REST][api_rest] API 或 [Batch .NET][api_net] 用戶端程式庫時，此 URL 可供存取您的 Batch 帳戶，並符合下列格式︰

  `https://<account_name>.<region>.batch.azure.com`

* 存取金鑰 -- 若要檢視及管理 Batch 帳戶的存取金鑰，請按一下金鑰圖示來開啟 [管理金鑰] 刀鋒視窗，或按一下 [所有設定] > [金鑰]。與 Batch 服務 API (例如 [Batch REST][api_rest] 或 [Batch .NET][api_net] 用戶端程式庫) 通訊時，需要有存取金鑰。

 ![Batch 帳戶金鑰][account_keys]

* 所有設定 -- 若要管理 Batch 帳戶的所有設定，或要檢視其屬性，請按一下 [所有設定] 以開啟 [設定] 刀鋒視窗。此刀鋒視窗可供存取帳戶的所有設定和屬性，包括檢視帳戶配額、選取要連結到 Batch 帳戶的 Azure 儲存體帳戶，以及管理使用者。

 ![Batch 帳戶設定和屬性刀鋒視窗][5]

## Batch 帳戶的考量

* 您也可以利用 [Batch PowerShell Cmdlet](batch-powershell-cmdlets-get-started.md) 和 [Batch Management .NET](batch-management-dotnet.md) 程式庫，建立和管理 Batch 帳戶。

* 您不需支付 Batch 帳戶本身的費用。您需支付您的 Batch 解決方案所用 Azure 計算資源的費用，以及您的工作負載執行時其他服務所用資源的費用。例如，您需支付您的集區中計算節點的費用，而如果使用[應用程式封裝](batch-application-packages.md)功能，您則需支付用來儲存應用程式封裝版本的 Azure 儲存體資源費用。如需詳細資訊，請參閱 [Batch 價格][batch_pricing]。

* 您可以在單一 Batch 帳戶中執行多個 Batch 工作負載，或在不同 Azure 區域的 Batch 帳戶之間散佈工作負載。

* 如果您執行數個大型的 Batch 工作負載，請注意適用於您的 Azure 訂用帳戶和每個 Batch 帳戶的特定 [Batch 服務配額和限制](batch-quota-limit.md)。目前 Batch 帳戶的配額會出現在入口網站的帳戶內容。

* 如果您將儲存體帳戶與 Batch 帳戶產生關聯，在重新產生儲存體帳戶存取金鑰時，請務必小心。您應該只重新產生單一儲存體帳戶金鑰，請按一下連結的儲存體帳戶刀鋒視窗中上的 [同步金鑰]，等候 5 分鐘，讓金鑰傳播至您的集區中的計算節點，然後重新產生並同步處理其他金鑰 (如有必要)。如果您同時重新產生這兩個金鑰，計算節點將無法同步處理任何一個金鑰，而且將無法存取儲存體帳戶。

  ![重新產生儲存體帳戶金鑰][4]

## 後續步驟

* 若要深入了解 Batch 服務概念和功能，請參閱 [Azure Batch 功能概觀](batch-api-basics.md)。本文討論主要 Batch 資源 (例如集區、計算節點、作業和工作)，並提供能夠進行大規模計算工作負載執行的服務功能概觀。

* 了解使用 [Batch .NET 用戶端程式庫](batch-dotnet-get-started.md)開發啟用 Batch 之應用程式的基本概念。[簡介文章](batch-dotnet-get-started.md)會介紹使用 Batch 服務在多個計算節點上執行工作負載的可行應用程式，並說明如何使用 Azure 儲存體進行工作負載檔案預備和擷取。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[3]: ./media/batch-account-create-portal/batch_acct_03.png "Azure 入口網站中的 Batch 服務建立刀鋒視窗"
[4]: ./media/batch-account-create-portal/batch_acct_04.png "重新產生儲存體帳戶金鑰"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Batch 帳戶設定和屬性刀鋒視窗"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0323_2016-->