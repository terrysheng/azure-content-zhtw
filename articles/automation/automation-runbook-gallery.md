<properties
	pageTitle="Azure 自動化的 Runbook 和模組資源庫 | Microsoft Azure"
	description="來自 Microsoft 和社群的 Runbook 和模組可供您在 Azure 自動化環境中安裝及使用。本文說明如何存取這些資源以及將您的 Runbook 貢獻至資源庫。"
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="stevenka"
	editor="tysonn" />
<tags
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="03/02/2016"
	ms.author="magoedte;bwren" />


# Azure 自動化的 Runbook 和模組資源庫

並非在 Azure 自動化中建立您自己的 Runbook 和模組，您可以存取各種已由 Microsoft 和社群建置的解決方案。您可以不加修改地使用這些解決方案，或者您可以使用它們做為起點並針對您的特定需求進行編輯。

您可以從 [Runbook 資源庫](#runbooks-in-runbook-gallery)取得 Runbook，從 [PowerShell 資源庫](#modules-in-powerShell-gallery)取得模組。您也可以藉由共用您開發的解決方案來參與社群。

## Runbook 資源庫中的 Runbook

[Runbook 資源庫](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=RootCategory&f[0].Value=WindowsAzure&f[1].Type=SubCategory&f[1].Value=WindowsAzure_automation&f[1].Text=Automation)提供各種來自 Microsoft 和社群的 Runbook，您可以匯入至 Azure 自動化。您可以從 [TechNet 指令碼中心](http://gallery.technet.microsoft.com/)託管的資源庫下載 Runbook，或者您可以直接從 Azure 傳統入口網站或 Azure 入口網站的資源庫匯入 Runbook。

若要直接從 Runbook 資源庫匯入，您只能使用 Azure 傳統入口網站或 Azure 入口網站。您無法使用 Windows PowerShell 執行此函式。

>[AZURE.NOTE] 您應該驗證您從 Runbook 資源庫取得的任何 Runbook 的內容，並且在生產環境中安裝和執行它們時小心謹慎。

### 透過 Azure 傳統入口網站從 Runbook 資源庫匯入 Runbook

1. 在 Azure 管理入口網站中，按一下 [新增] > [應用程式服務] > [自動化] > [Runbook] > [從資源庫]。
2. 選取類別以檢視相關的 Runbook，然後選取 Runbook 以檢視其詳細資料。當您選取您想要的 Runbook 時，按一下向右箭頭按鈕。

    ![Runbook 資源庫](media/automation-runbook-gallery/runbook-gallery.png)

3. 檢閱 Runbook 的內容並記下說明中的任何需求。當您完成時按一下向右箭頭按鈕。
4. 輸入 Runbook 詳細資料，然後按一下勾選記號按鈕。Runbook 名稱已填入。
5. Runbook 會出現在 [自動化帳戶] 的 [Runbook] 索引標籤。

### 使用 Azure 入口網站從 Runbook 資源庫匯入 Runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 按一下 [Runbook] 磚以開啟 Runbook 的清單。
3. 按一下 [瀏覽資源庫] 按鈕。

    ![瀏覽資源庫按鈕](media/automation-runbook-gallery/browse-gallery-button.png)

4. 找出您想要的資源庫項目，並且選取以檢視其詳細資料。

    ![瀏覽資源庫](media/automation-runbook-gallery/browse-gallery.png)

4. 按一下 [檢視來源專案] 以檢視 [TechNet 指令碼中心](http://gallery.technet.microsoft.com/)中的項目。
5. 若要匯入項目，請按一下以檢視其詳細資料，然後按一下 [匯入] 按鈕。

    ![匯入按鈕](media/automation-runbook-gallery/gallery-item-detail.png)

6. 選擇性變更 Runbook 的名稱，然後按一下 [確定] 以匯入 Runbook。
5. Runbook 會出現在 [自動化帳戶] 的 [Runbook] 索引標籤。


### 將 Runbook 新增至 Runbook 資源庫

Microsoft 鼓勵您將您認為可能有助於其他客戶的 Runbook 新增至 Runbook 資源庫。您可以藉由[將其上傳到指令碼中心](http://gallery.technet.microsoft.com/site/upload)來新增 Runbook，並且將下列詳細資料納入考量。

- 您必須針對 [類別] 指定 [Windows Azure]，針對 [子類別] 指定[自動化]，讓 Runbook 顯示在精靈中。  

- 上傳必須是單一 .ps1 或 .graphrunbook 檔案。如果 Runbook 需要任何模組、子 Runbook 或資產，則您應該在提交的說明中和 Runbook 的註解區段中列出。如果您有需要多個 Runbook 的解決方案，則分別將每個上傳並且在各自的說明中列出相關 Runbook 的名稱。請確定您使用相同的標記，這樣它們就會在相同的類別中顯示。使用者必須閱讀說明以便知道解決方案需要其他 Runbook 才能運作。

- 使用 [**插入程式碼區段**] 圖示將 PowerShell 或 PowerShell 工作流程程式碼片段插入說明中。

- 上傳的摘要會顯示在 Runbook 資源庫結果，因此您應該提供詳細資訊，幫助使用者識別 Runbook 的功能。

- 您應該對上傳項目指派 1 到 3 個下列標記。Runbook 會列在精靈中符合其標記的類別底下。精靈會略過不在此清單上的任何標記。如果您未指定任何相符的標記，Runbook 會列在 [其他] 類別底下。

 - 備份
 - 產能管理
 - 變更控制
 - 法規遵循
 - 開發/測試環境
 - 災害復原
 - 監視
 - 修補
 - 佈建
 - 補救
 - VM 生命週期管理


- 一小時更新自動化資源庫一次，因此您不會立即看到您的貢獻。

## PowerShell 資源庫中的模組

PowerShell 模組包含您可以在 Runbook 中使用的 Cmdlet，您可以安裝在 Azure 自動化中的現有模組可於 [PowerShell 資源庫](http://www.powershellgallery.com)取得。您可以從 Azure 入口網站啟動此資源庫，然後直接安裝至 Azure 自動化，也可以手動下載及安裝。您無法直接從 Azure 傳統入口網站安裝模組，但是您可以使用一般方式，下載及安裝這些模組。

### 透過 Azure 入口網站從自動化模組資源庫匯入模組

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 按一下 [**資產**] 磚以開啟資產的清單。
3. 按一下 [**模組**] 磚以開啟模組的清單。
4. 按一下 [瀏覽資源庫] 按鈕，即可啟動 [瀏覽資源庫] 刀鋒視窗。

    ![模組資源庫](media/automation-runbook-gallery/modules-blade.png) <br>
5. 啟動 [瀏覽資源庫] 刀鋒視窗後，您就可以搜尋下列欄位：

   - 模組名稱
   - 標記
   - 作者
   - Cmdlet/DSC 資源名稱

6. 尋找您感興趣的模組，並選取以檢視其詳細資料。當您向下切入到特定的模組時，您可以檢視有關該模組的詳細資訊，包括返回 PowerShell 資源庫的連結、任何必要的相依性，以及該模組包含的所有 cmdlet 和 (或) DSC 資源。

    ![PowerShell 模組的詳細資料](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>

7. 若要將該模組直接安裝至 Azure 自動化，請按一下 [部署] 按鈕。

    ![[匯入模組] 按鈕](media/automation-runbook-gallery/module-import-button.png)

8. 按一下 [匯入] 按鈕時，會看到要匯入的模組名稱。如果已安裝所有相依性，便可使用 [確定]。如果缺少相依性，需先加以匯入後才能匯入該模組。
9. 按一下 [確定] 匯入模組，即可啟動 [模組] 刀鋒視窗。當 Azure 自動化模組匯入到您的帳戶時，會擷取有關模組和 cmdlet 的中繼資料。

    ![[匯入模組] 刀鋒視窗](media/automation-runbook-gallery/module-import-blade.png)

    因為必須解壓縮每個活動，此步驟可能需要幾分鐘的時間。
10. 您會在模組已部署以及模組完成時收到通知。
11. 在匯入模組之後，會看到可用的活動。您可以將其資源用於您的 Runbook 和預期狀態設定中。

## 要求 Runbook 或模組

您可以將要求傳送至[使用者心聲](https://feedback.azure.com/forums/246290-azure-automation/)。如果您需要協助撰寫 Runbook 或有關於 PowerShell 的問題，請將問題張貼至我們的[論壇](http://social.msdn.microsoft.com/Forums/windowsazure/zh-TW/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)。

## 相關文章

- [在 Azure 自動化中建立或匯入 Runbook](automation-creating-importing-runbook.md)
- [了解 PowerShell 工作流程](automation-powershell-workflow.md)

<!---HONumber=AcomDC_0302_2016-------->