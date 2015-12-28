<properties 
   pageTitle="使用雲端總管管理 Azure 資源"
   description="了解如何在 Visual Studio 內使用 [雲端總管] 瀏覽和管理 Azure 資源。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="tarcher" />

# 使用雲端總管管理 Azure 資源

##概觀

[雲端總管] 是為了讓您更輕鬆快速地在 Visual Studio IDE 內瀏覽和管理 Azure 資源。比方說，它可讓您在 Azure 入口網站或瀏覽器中開啟 Web 應用程式，或將偵錯工具附加給它，或者，您可以檢視 blob 容器的屬性並在 [Blob 容器編輯器] 中開啟它。

[雲端總管] 以 Azure 資源管理員堆疊為基礎，就像 Microsoft Azure Preview 入口網站。它了解 Azure 資源群組等資源及邏輯應用程式和 API 應用程式等 Azure 服務，且支援[角色型存取控制](../role-based-access-control-configure/) (RBAC)。若要查看已加入或變更的 Azure 資源，請選擇 [雲端總管] 工具列的 [重新整理] 按鈕。

[雲端總管] 安裝為 Visual Studio Tools for Azure SDK 2.7 的一部分。

## 必要條件

- Visual Studio 2015 RTM。

- Visual Studio Tools for Azure SDK。
- 您也必須擁有 Azure 帳戶並登入，才能在 [雲端總管] 中檢視 Azure 資源。如果沒有的話，只需要幾分鐘的時間就可以建立帳戶。如果您有 MSDN 訂用帳戶，請參閱 [MSDN 訂戶的 Azure 權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。否則，請參閱[建立免費試用帳戶](http://azure.microsoft.com/pricing/free-trial/)。

- 如果看不到 [雲端總管]，您可以在功能表列上選擇 [檢視]、[其他視窗]、[雲端總管] ，就能看到。

## 管理 Azure 帳戶和訂用帳戶

若要在 [雲端總管] 中查看您的 Azure 資源，您需要登入具有一或多個作用中訂用帳戶的 Azure 帳戶。如果您有一個以上的 Azure 帳戶，您可以將它們加入 [雲端總管] 中，然後選擇您想要包含在 [雲端總管] 資源檢視中的訂用帳戶。

如果您未曾使用過 Azure，或尚未將必要的帳戶加入至 Visual Studio，將會提示您這樣做。

## 將 Azure 帳戶加入至雲端總管

1. 在 [雲端總管] 工具列上選擇 [設定] 圖示。

1. 選擇 [新增帳戶] 連結。登入您想要瀏覽資源的 Azure 帳戶。帳戶選擇器下拉式清單中應該已選取您剛才加入的帳戶。該帳戶的訂用帳戶會出現在帳戶項目下。

    ![新增 Azure 訂用帳戶](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![選擇 Azure 訂用帳戶](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. 選取您想要瀏覽之訂用帳戶的核取方塊，然後選擇 [套用] 按鈕。

    選取的訂用帳戶的 Azure 資源會出現在 [雲端總管] 中。

## 移除 Azure 帳戶

1. 在功能表列上選擇 [檔案]、[帳戶設定]。

1. 在 [帳戶設定] 對話方塊的 [所有帳戶] 區段中，選擇您想要移除之帳戶旁的 [移除] 命令。請注意，此命令只從 Visual Studio 中移除帳戶，並不會影響 Azure 帳戶本身。

## 檢視資源類型或群組

若要檢視您的 Azure 資源，您可以選擇 [資源類型] 或 [資源群組] 檢視。

![資源檢視下拉式清單](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- [資源類型] 檢視 (也是用在 Azure 入口網站上的一般檢視) 會依類型分類來顯示 Azure 資源，例如 Web 應用程式、儲存體帳戶和虛擬機器。這類似 Azure 資源在 [伺服器總管] 中出現的方式。

- [資源群組] 檢視會依相關聯的 Azure 資源群組將 Azure 資源分類。

 
	資源群組是通常由特定應用程式使用的 Azure 資源組合。若要深入了解 Azure 資源群組，請參閱 [Azure 資源管理員概觀](https://azure.microsoft.com/documentation/articles/resource-group-overview/)。

## 檢視和瀏覽資源

若要在 [雲端總管] 中瀏覽至 Azure 資源並檢視其資訊，請展開項目的類型或相關聯的資源群組，然後選擇資源。當您選擇資源時，資訊會出現在 [雲端總管] 底部的兩個索引標籤中。

![選擇資源檢視](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- [動作] 索引標籤會顯示您在 [雲端總管] 中可針對選取的資源採取的動作。您也可以在資源的捷徑功能表上看到可用的動作。

- [屬性] 索引標籤會顯示資源的屬性，例如其類型、地區設定和相關聯的資源群組。

每個資源都有 [在入口網站中開啟] 這個動作。當您選擇此動作時，[雲端總管] 會在 Azure 入口網站中顯示選取的資源。這項功能是特別方便瀏覽至深度巢狀的資源。

根據 Azure 資源而定，也可能出現其他動作和屬性值。例如，除了 [在入口網站中開啟]，Web 應用程式和邏輯應用程式也有 [在瀏覽器中開啟] 和 [附加偵錯工具] 動作。當您選擇儲存體帳戶 blob、佇列或資料表時，將會出現開啟編輯器的動作。Azure 應用程式具有 **URL** 和 **狀態**屬性，而儲存體資源具有索引鍵和連接字串屬性。

## 搜尋資源

若要在 Azure 訂用帳戶中尋找具有特定名稱的資源，請在 [雲端總管] 的 [搜尋] 方塊中輸入名稱。

![在 [雲端總管] 中尋找資源](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

當您在 [搜尋] 方塊中輸入字元時，只有符合這些字元的資源才會出現在資源樹狀目錄中。

<!---HONumber=AcomDC_1217_2015-->