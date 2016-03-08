<properties
   pageTitle="使用 Power BI 從 Azure 資訊安全中心的資料取得見解 | Microsoft Azure"
   description="Azure 資訊安全中心的 Power BI 內容套件可讓您根據為了報告功能所建立的資料集，輕鬆尋找安全性警示、建議、受到攻擊的資源和趨勢。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/25/2016"
   ms.author="yurid"/>

# 使用 Power BI 從 Azure 資訊安全中心的資料取得見解
Azure 資訊安全中心的 [Power BI 儀表板](http://aka.ms/azure-security-center-power-bi)可讓您從任何地方 (包括行動裝置) 視覺化、分析和篩選建議與安全性警示。使用 Power BI 儀表板即可顯示趨勢和攻擊模式，依資源或來源 IP 位址檢視安全性警示以及依資源或存留期檢視尚未解決的安全性風險。您也可以透過有趣的方式整合資訊安全中心的建議和安全性警示與其他資料，例如與 [Azure 稽核記錄檔](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/)和 [Azure SQL Database 稽核](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/)整合，這兩者均提供 Power BI 儀表板，或者您也可以將這項資料匯出至 Excel 以便輕鬆得到雲端資源安全性狀態的報告。

> [AZURE.NOTE] 本文件中的資訊適用於「Azure 資訊安全中心」的預覽版本。

## 使用 Power BI 服務探索 Azure 資訊安全中心的資料
在 Power BI 中連線到 [Azure 資訊安全中心內容套件](https://app.powerbi.com/groups/me/getdata/services/azure-security-center)，並按照下列步驟執行：

1\. 按一下 [Azure 資訊安全中心] 圖格中的 [連線] 以繼續。

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig1.png)

2\. [連線到 Azure 資訊安全中心] 視窗隨即開啟。在 [Azure 訂用帳戶識別碼] 欄位中輸入您的 Azure 訂用帳戶，然後按 [下一步]。

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig2.png)

3\. 在 [驗證方法] 下拉式清單中選取 [oAuth2]，然後按一下 [登入]。

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig3.png)

4\. 系統會將您重新導向至驗證頁面，請在此輸入您要用來連線到 Azure 資訊安全中心的認證。驗證程序完成後，Power BI 會開始匯入資料以建置報告。在這段期間，您可能會在瀏覽器右側角落看到下列訊息：

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig4.png)

5\. 程序完成後，Azure 資訊安全中心 Power BI 儀表板就會載入報告，如下所示：

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig5.png)

您可以一目了然地看到安全性警示和建議的數目，以及 Azure 資訊安全中心所監控的 VM、Azure SQL Database 和網路資源的數目。

Azure 資訊安全中心的連結會將您重新導向至 Azure 入口網站。圖表則可讓您輕鬆視覺化關於安全性建議和警示的資訊，包括：

- 資源安全性健康情況
- 整體擱置建議
- VM 建議
- 不同時間的警示
- 受到攻擊的資源
- 受到攻擊的 IP

每個圖表背後都有額外的見解。選取圖格即可查看詳細資訊，例如 [資源安全性健康情況] 圖格會顯示依資源分類之擱置建議的其他詳細資料，如下所示：

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig6.png)

如果您按一下此圖形中的任一行，其他行就會變為灰色，而您只會聚焦在所選的那一行。若要返回儀表板，請按一下此頁左窗格之 [儀表板] 選項底下的 [Azure 資訊安全中心]。

> [AZURE.NOTE] 如果您想要自訂報告，只要透過新增其他欄位或變更現有視覺效果即可編輯報告。如需詳細資訊，請閱讀[在 Power BI 中與編輯檢視的報表互動](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/)。

當您按一下 [不同時間的警示]、[受到攻擊的資源] 和 [攻擊者 IP] 圖格時，其各自都會有相似的輸出。會有這個情況是因為報告會彙總這三個變數的所有相關資訊，並將它稱為**遭受攻擊的資源**，如下所示：

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig7.png)

此時您也可以使用 [檔案] 功能表中提供的選項儲存一份這個報表、予以列印下來或發佈在網站上。

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig8.png)


##使用 Azure 資訊安全中心儀表板存取 Power BI
您也可以使用 Azure 資訊安全中心儀表板存取 Power BI 報告。請按照下列步驟來執行這項工作：

1\. 在 [Azure 資訊安全中心] 儀表板按一下 [在 Power BI 中瀏覽] 按鈕。

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig9.png)

2\. [在 Power BI 中瀏覽] 刀鋒視窗隨即會在右側開啟，如下所示：

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig10-1.png)

3\. 在 [選擇用來在 Power BI 中瀏覽的訂用帳戶] 下拉式清單底下，選取您想要使用的訂用帳戶。

4\. 在 [複製訂用帳戶識別碼] 欄位中按一下 [複製] 按鈕。5. 按一下 [移至 Power BI] 按鈕。6. [連線到 Azure 資訊安全中心] 視窗隨即開啟。在 [Azure 訂用帳戶識別碼] 欄位中輸入您的 Azure 訂用帳戶，然後按 [下一步]。

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig2.png)

7\. 在 [驗證方法] 下拉式清單中選取 [oAuth2]，然後按一下 [登入]。

![使用 Power BI 連線到 Azure 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig3.png)

8\. 系統會將您重新導向至驗證頁面，請在此輸入您要用來連線到 Azure 資訊安全中心的認證。驗證程序完成後，Power BI 會開始匯入資料以建置報告。

## 後續步驟
在本文件中，您已了解如何在 Azure 資訊安全中心內使用 Power BI。若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

- [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) - 了解如何在 Azure 資訊安全中心設定安全性設定
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) - 了解如何管理與回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) - 尋找使用服務的常見問題
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 尋找有關 Azure 安全性與相容性的部落格文章

<!---HONumber=AcomDC_0302_2016-->