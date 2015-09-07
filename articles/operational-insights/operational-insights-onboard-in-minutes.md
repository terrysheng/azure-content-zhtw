<properties
    pageTitle="幾分鐘內即上架到 Operational Insights | Microsoft Azure"
	description="了解如何在幾分鐘之內完成 Azure Operational Insights 的設定"
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
    ms.service="operational-insights"
	ms.workload="operational-insights"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/06/2015"
	ms.author="banders"/>

# 幾分鐘內即上架到 Azure Operational Insights


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

幾分鐘內就可以啟動並執行 Azure Operational Insights。在選擇 Operational Insights 工作區的建立方式時，您有兩個選項，就像建立帳戶一樣：

- Microsoft Operations Management Suite
- Microsoft Azure 訂用帳戶

您可以使用 Operations Management Suite 網站建立 Operations Management Suite 工作區。也可以使用 Microsoft Azure 訂用帳戶建立 Operational Insights 工作區。目前，這兩個工作區擁有相同的功能。兩者之間唯一的差異是名稱。如果您使用 Azure 訂用帳戶，也可以使用該訂用帳戶來存取其他 Azure 服務。不論建立工作區的方法為何，您都會使用 Microsoft 帳戶或組織帳戶來建立工作區。

## 使用 Operations Management Suite 的 3 個註冊步驟

1. 移至 [Operations Management Suite](http://microsoft.com/oms) 網站並按一下 [免費試用]。登入 Microsoft 帳戶 (如 Outlook.com)，或登入貴公司或教育機構所提供，用來搭配 Office 365 或其他 Microsoft 服務的組織帳戶。
2. 提供唯一的工作區名稱。工作區是儲存管理資料的邏輯容器。它提供在不同組織團隊之間分割資料的方法，因為資料為其工作區專有。指定電子郵件地址和資料的儲存區域。![建立工作區及連結訂用帳戶](./media/operational-insights-onboard-in-minutes/create-workspace-link-sub.png)
3. 接下來，您可以建立新的 Azure 訂用帳戶或連結現有的 Azure 訂用帳戶。如果您想要繼續使用免費試用版，請按一下 [**不是現在**]。

您已準備好開始使用 Operations Management Suite 入口網站。

若要深入了解有關設定工作區，以及連結現有 Azure 帳戶與利用 Operations Management Suite 建立之工作區的資訊，請參閱[設定工作區和管理設定](operational-insights-setup-workspace.md)。

## 使用 Microsoft Azure 快速註冊

1. 移至 [Azure 入口網站](https://manage.windowsazure.com)並登入，然後在服務清單中選取 [Operational Insights]。![Azure 入口網站](./media/operational-insights-onboard-in-minutes/azure-portal-op-insights.png)
2. 依序按一下 [建立工作區] 和 [快速建立]，在 [帳戶] 下方輸入工作區名稱、選擇層，然後選擇工作區資料的儲存位置。如果您有多個訂用帳戶，可以選擇要使用的訂用帳戶，然後按一下 [建立工作區]。![Azure 入口網站](./media/operational-insights-onboard-in-minutes/quick-create.png)
3. 選取建立的工作區，然後按一下 [造訪您的 Operational Insights 帳戶] 以開啟 Operations Management Suite 網站。![瀏覽帳戶](./media/operational-insights-onboard-in-minutes/visit-account.png)
4. 在 Operations Management Suite 網站上輸入電子郵件地址，然後按一下 [確認並繼續]。系統會傳送確認電子郵件給您。開啟電子郵件，然後按一下內文中的 [現在確認]。
5. Operations Management Suite 網站會顯示概觀頁面。按一下 [開始使用] 以繼續作業。

您已準備好開始使用 Operations Management Suite 入口網站。

若要深入了解有關設定工作區，以及連結使用 Operations Management Suite 建立之工作區與 Azure 訂用帳戶的資訊，請參閱[設定工作區和管理設定](operational-insights-setup-workspace.md)。

## 開始使用 Operations Management Suite 入口網站
若要選擇解決方案及連接要管理的伺服器，請按一下 [開始使用] 磚，然後依照本節的步驟操作。

![建立工作區及連結訂用帳戶](./media/operational-insights-onboard-in-minutes/get-started.png)

- 選取要使用的解決方案，然後按一下 [新增選取的解決方案]。![解決方案](./media/operational-insights-onboard-in-minutes/solutions.png)
- 選擇收集資料的伺服器環境連接方式：
    - 藉由安裝代理程式來直接連接任何 Windows Server 或用戶端。
    - 使用 System Center Operations Manager 來附加管理群組或整個 Operations Manager 部署。
    - 使用採用 Windows 或 Linux Azure 診斷 VM 延伸模組設定的 Azure 儲存體帳戶。
- 至少設定一個記錄檔以填入資料。您可以選取 IIS 記錄檔及/或新增事件記錄，再選取頁面底部的 [儲存]。您可以指定要監視的事件記錄訊息類型，包括錯誤、警告和資訊。![解決方案](./media/operational-insights-onboard-in-minutes/logs.png)

## (選擇性) 藉由安裝代理程式來直接連接伺服器和 Operations Management Suite
1. 在 [開始使用] 檢視中按一下 [連接資料來源] 節點，然後按一下 [下載 Windows 代理程式]。您只能將代理程式安裝在 Windows Server 2008 SP1 或更新版本，亦或是 Windows 7 SP1 或更新版本。伺服器需要具備 x64 架構。
2. 將代理程式安裝在一或多部伺服器上。您可以逐一安裝代理程式、使用搭配[自訂指令碼](operational-insights-direct-agent.md#configure-the-microsoft-monitoring-agent-optional)的自動化方法，也可以使用現有的軟體散發解決方案。
3. 在同意授權合約及選擇安裝資料夾之後，請選取 [將代理程式連線至 Microsoft Azure Operational Insights]。![代理程式設定](./media/operational-insights-onboard-in-minutes/agent.png)
4. 在下一個頁面中，系統會詢問您的工作區識別碼和工作區金鑰。您的工作區識別碼和金鑰會顯示在下載代理程式檔案的畫面上。![附加伺服器](./media/operational-insights-onboard-in-minutes/key.png)
5. 在安裝期間，您可以按一下 [進階] 以選擇性地設定 Proxy 伺服器及提供驗證資訊。按 [下一步] 按鈕返回工作區資訊畫面。
6. 按 [下一步] 驗證工作區識別碼和金鑰。如果您發現任何錯誤，可以按一下 [上一頁] 予以修正。當工作區識別碼和金鑰通過驗證後，請按一下 [安裝] 來完成代理程式安裝。
7. 重新登入 Operations Management Suite 入口網站，然後按一下 [概觀] 頁面上的 [設定] 磚。當代理程式與 Operations Management Suite 服務通訊時，會出現綠色核取記號圖示。剛開始的時候，這需要大約 5-10 分鐘。

> [AZURE.NOTE]直接連接 Operations Management Suite 的伺服器目前不支援容量管理和組態評估解決方案。

您也可以將代理程式連接到 System Center Operations Manager 2012 SP1 和更新版本。若要這樣做，請選取 [將代理程式連接至 System Center Operations Manager]。如果您選擇該選項，可以在不需要額外硬體或對管理群組造成額外負荷的情況下將資料傳送到服務。

若要閱讀更多有關將代理程式直接連接到 Operations Management Suite 的資訊，請參閱[將電腦直接連接到 Operational Insights](operational-insights-direct-agent.md)。

## (選擇性) 使用 System Center Operations Manager 連接伺服器

1. 在 Operations Manager 主控台內選取 [管理]。
2. 展開 [Operational Insights] 節點，然後選取 [Operational Insights 連接]。
3. 按一下右上方的 [註冊至 Operational Insights] 連結，並遵循指示執行作業。
4. 完成註冊精靈之後，按一下 [加入電腦/群組] 連結。
5. 在 [電腦搜尋] 對話方塊中，您可以搜尋 Operations Manager 監視的電腦或群組。選取要上架到 Operational Insights 的電腦或群組，按一下 [加入]，然後按一下 [確定]。您可以驗證 Operational Insights 服務是否正在接收資料，只要移至 Operations Management Suite 入口網站的 [使用量] 磚即可。資料應該會在大約 5-10 分鐘之後出現。

若要閱讀更多有關將 Operations Manager 連接到 Operations Management Suite 的資訊，請參閱[從 System Center Operations Manager 連接至 Operational Insights](operational-insights-connect-scom.md)。

## (選擇性) 分析 Microsoft Azure 之雲端服務的資料

利用 Operations Management Suite，您可以啟用 Azure 雲端服務診斷來快速搜尋雲端服務和虛擬機器的事件和 IIS 記錄檔。您也可以安裝 Microsoft Monitoring Agent，取得有關 Azure 虛擬機器的額外見解。若要閱讀更多有關如何設定 Azure 環境以使用 Operations Management Suite 的資訊，請參閱[在 Microsoft Azure 中分析來自伺服器的資料](operational-insights-analyze-data-azure.md)。


## 後續步驟
- 開始使用[解決方案](operational-insights-solutions.md)。
- 熟悉[搜尋](operational-insights-search.md)。
- 使用[儀表板](operational-insights-use-dashboards.md)來儲存及顯示自訂搜尋。

<!---HONumber=August15_HO9-->