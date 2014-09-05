<properties linkid="web-sites-staged-publishing" urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Staged Deployment on Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Staged Deployment, Site Slots" description="Learn how to use staged publishing on Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Microsoft Azure 網站上的預備部署
================================

目錄
----

-   [概觀](#Overview)
-   [新增部署位置至網站](#Add)
-   [關於部署位置組態](#AboutConfiguration)
-   [交換部署位置](#Swap)
-   [將生產網站回復至預備網站](#Rollback)
-   [刪除網站位置](#Delete)
-   [用於建立網站位置的 Azure PowerShell Cmdlet](#PowerShell)
-   [用於建立網站位置的 Azure 跨平台命令列介面 (xplat-cli) 命令](#CLI)

## 概觀 ##
針對在 Microsoft Azure 網站上執行的標準模式網站建立網站位置的選項，可提供您預備部署工作流程。您可針對每個預設的生產網站 (現在已成為生產位置) 建立開發或預備網站位置，並且在不中斷營運下交換這些位置。在以下情境中，預備部署是非常寶貴的功能：

-   **部署前驗證** - 在您部署內容或組態至預備網站後，您可以先驗證變更，再交換這些變更至生產網站。

-   **建立並整合網站內容** - 您可以累進式地新增內容更新至預備部署位置，等到更新完畢後再將部署位置與生產位置交換。

-   **回復生產網站** - 如果與生產位置交換的變更不如您預期，則您可以立即將原始內容回復到生產位置。

當您部署內容時，Microsoft Azure 會先讓來源網站位置的所有執行個體暖機，再將變更交換至生產位置，免除冷開機的麻煩。交換作業期間所有的流量都能順暢地重新導向，而且不會捨棄任何要求封包。就目前而言，每個標準網站在預設的生產位置之外，僅支援一個部署位置。

## 新增部署位置至網站 ##

該網站必須在標準模式下執行以便啟用網站位置建立功能。

1.  在 [快速啟動] 頁面，或是在您的網站的 [儀表板] 頁面中的 [快速瀏覽] 區段，按一下 **[Add a new deployment slot]**。

    ![新增部署位置](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)

    > [WACOM.NOTE] 如果網站還未處於標準模式，則會顯示**You must be in the standard mode to enable staged publishing**訊息。此時，您可以選擇 **升級**，瀏覽至網站的 [Scale] **索引標籤**後再繼續。

2.  **[Add New Deployment Slot]** 對話方塊隨即顯示。

    ![Add New Deployment Slot dialog](./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png)

    為部署位置提供名稱。該名稱不得超出 60 個英數字元。不可使用特殊字元或空格。

3.  在網站清單中，展開網站名稱左側的標記以顯示部署位置。該處將顯示您的生產網站名稱，並在後面以括弧括住您先前提供的部署位置名稱。

    ![部署位置網站清單](./media/web-sites-staged-publishing/SiteListWithStagedSite.png)

4.  當您選取部署網站位置名稱時，會開啟一個頁面，內含一些索引標籤，就像是其他任何網站一樣。***您的網站名稱*(*部署位置名稱*)** 會顯示在入口網站頁面上方，提醒您正在檢視部署網站位置。

    ![Deployment Slot Title](./media/web-sites-staged-publishing/StagingTitle.png)

現在您可以更新部署網站位置的內容與組態。更新內容時，請使用與部署網站位置相關聯的發行設定檔或部署認證。

## 關於部署位置組態 ##
建立部署位置時，系統預設會從生產網站複製該部署位置的組態。所有網站位置的組態都是可編輯的。

**將改變位置交換的組態**：

-   一般設定
-   連接字串
-   處理常式對應
-   監視與診斷設定

**將不會改變位置交換的組態**：

-   發行端點
-   自訂網域名稱
-   SSL 憑證與繫結
-   擴充設定

**注意**：

-   部署位置僅適用於標準模式下的網站。

-   如果您將網站必更為「免費」、「共用」或「基本」模式，將無法交換。

-   您打算與生產位置交換的部署位置，需要具備與生產位置一模一樣的組態。

-   依預設，部署位置會指向與生產網站相同的資料庫。不過，您可以將部署位置設定為指向替代資料庫，方法是變更部署位置的資料庫連接字串。接下來，您可以在將部署位置與生產位置交換之前，先在部署位置上還原原始的資料庫連接字串。

## 交換部署位置 ##

1.  若要交換部署位置，請在網站清單中選取部署位置，然後按一下命令列中的 [交換] **按鈕**。

    ![Swap Button](./media/web-sites-staged-publishing/SwapButtonBar.png)

2.  [交換部署] 對話方塊隨即顯示。此對話方塊可讓您選擇要成為來源與目的地的網站位置。

    ![交換部署對話方塊](./media/web-sites-staged-publishing/SwapDeploymentsDialog.png)

3.  按一下核取記號完成操作。操作完成時，網站位置已經交換完畢。

## 將生產網站回復至預備網站 ##
如果在將內容或組態交換至生產網站期間發生任何錯誤，您只需將部署位置 (交換前為生產網站) 回復為生產位置，然後將處於預備模式下的新版網站進行後續修正。

> [WACOM.NOTE] 為了要成功回復，部署網站位置必須仍舊包含先前生產網站內未變更的內容與組態。

## 刪除網站位置 ##

在 Azure 網站入口網站頁面下方的命令列裡，按一下 **[刪除]**。您將可以選擇刪除網站與所有部署位置，或是僅刪除部署位置。

![Delete a Site Slot](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

若您在確認頁面中回答 **[是]**，則會依據您所選的選項刪除一個或所有位置。

**注意**：

-   非生產網站位置無法使用擴充功能。只有生產網站位置可使用此功能。

-   非生產網站位置不支援連結的資源管理。

-   如果您需要的話，仍舊可以直接發行至生產網站位置。

-   目前來說，您的部署位置 (網站) 將與您的生產位置 (網站) 共用相同的資源並在相同的 VM 上執行。如果您在預備位置執行壓力測試，生產環境將會出現相似的壓力負載。

## 用於建立網站位置的 Azure PowerShell Cmdlet

Azure PowerShell 模組可提供透過 Windows PowerShell 來管理 Azure 的 Cmdlet，包括支援管理 Azure 網站部署位置。

-   如需安裝與設定 Azure PowerShell，以及使用您的 Azure 訂閱驗證 Azure PowerShell 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](http://www.windowsazure.com/zh-tw/documentation/articles/install-configure-powershell) (英文)。

-   若要列出可供 Azure 網站使用的 PowerShell Cmdlet，請呼叫 `help AzureWebsite`。

* * * * *

### Get-AzureWebsite

**Get-AzureWebsite** Cmdlet 會針對現有訂閱呈現 Azure 網站資訊，如以下範例所示。

`Get-AzureWebsite siteslotstest`

* * * * *

### New-AzureWebsite

您可以使用 **New-AzureWebsite** Cmdlet 並同時指定網站與位置的名稱，於標準模式下為任何網站建立網站位置。您還可以指出相同的區域，作為部署位置建立作業所需的網站，如以下範例所示。

`New-AzureWebsite siteslotstest –Slot staging –Location “West US”`

* * * * *

### Publish-AzureWebsiteProject

您可以在部署內容時使用 **Publish-AzureWebsiteProject** Cmdlet，如以下範例所示。

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

* * * * *

### Show-AzureWebsite

當內容與組態更新都套用到新的位置後，您就可以使用 **Show-AzureWebsite** Cmdlet 瀏覽至該位置以驗證更新，如以下範例所示。

`Show-AzureWebsite -Name siteslotstest -Slot staging`

* * * * *

### Switch-AzureWebsiteSlot

**Switch-AzureWebsiteSlot** Cmdlet 可執行交換操作，將更新的部署位置轉變為生產網站，如以下範例所示。生產網站不會出現任何停機事件，也不會進行冷啟動。

`Switch-AzureWebsiteSlot -Name siteslotstest`

* * * * *

### Remove-AzureWebsite

如果不再需要某個部署位置，可以使用 **Remove-AzureWebsite** Cmdlet 將其刪除，如以下範例所示。

`Remove-AzureWebsite -Name siteslotstest -Slot staging`

* * * * *

## 用於建立網站位置的 Azure 跨平台命令列介面 (xplat-cli) 命令

Azure 跨平台命令列介面 (xplat-cli) 提供您處理 Azure 的跨平台命令，包括支援管理 Azure 網站上的部署位置。

-   如需安裝與設定 xplat-cli 的相關說明，包括如何將 xplat-cli 與您的 Azure 訂閱連線的詳細資訊，請參閱[安裝與設定 Azure 跨平台命令列介面](http://www.windowsazure.com/zh-tw/documentation/articles/xplat-cli) (英文)。

-   若要列出 Azure 網站可用的 xplat-cli 命令，請呼叫 `azure site -h`。

* * * * *

## azure site list
如需現有訂閱中的 Azure 網站相關資訊，請呼叫 **azure site list**，如以下範例所示。

`azure site list siteslotstest`

* * * * *

## azure site create
若要在標準模式下為任何網站建立網站位置，請呼叫 **azure site create** 並指定現有網站的名稱以及要建立的位置名稱，如以下範例所示。

`azure site create siteslotstest --slot staging`

若要對新位置啟用來源控制，請使用 **--git** 選項，如以下範例所示。

`azure site create â“-git siteslotstest --slot staging`

* * * * *

## azure site swap
若要將更新的部署位置轉變成生產網站，請使用 **azure site swap** 命令執行交換操作，如以下範例所示。生產網站不會出現任何停機事件，也不會進行冷啟動。

`azure site swap siteslotstest`

* * * * *

## azure site delete
若要刪除不再需要的部署位置，請使用 **azure site delete** 命令，如以下範例所示。

`azure site delete siteslotstest --slot staging`

* * * * *
