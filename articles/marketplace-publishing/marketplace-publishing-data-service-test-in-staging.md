<properties
   pageTitle="測試要發行到 Marketplace 的資料服務 | Microsoft Azure"
   description="了解如何測試要發行到 Azure Marketplace 的資料服務。"
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
   ms.date="12/03/2015"
   ms.author="hascipio; avikova" />

# 在預備環境中測試您的資料服務產品
完成[建立 Microsoft 開發人員帳戶](marketplace-publishing-accounts-creation-registration.md)及[在發行入口網站中建立您的資料服務產品](marketplace-publishing-data-service-creation.md)這兩個步驟之後，您就可以開始準備在 Azure Marketplace 中銷售您的產品。本主題將逐步引導您執行第一個中繼步驟，稱為「預備環境」

預備環境代表將您的供應項目部署在私人的「沙箱」中，您可以在推送到生產環境之前在沙箱中測試與驗證其功能。供應項目會出現在預備環境中，就如同客戶已部署該項目一樣。

## 步驟 1.將您的產品發送到預備環境
將產品發送到預備環境可讓您先測試您的產品，然後再將其提供給日後的訂閱者。您可以從資料訂閱者的角度來看您的產品，並了解其功能的運作狀況。

  ![繪圖](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.	登入[發行入口網站](https://publish.windowsazure.com)。
2.	選取左導覽視窗中的 [資料服務]。
3.	選取要推送到預備環境的產品。您將會看到上列畫面。
4.	按一下 [推送至預備環境] 按鈕。  
5.	您的產品如有任何問題必須在推送到預備環境之前解決，將顯示問題清單。按一下清單中每個項目，以更正這些項目。當所有的更正作業完成之後，請再按一下 [推送至預備環境] 按鈕。

您的產品若無任何問題，將會顯示下列快顯視窗。

若您不打算在 Azure 入口網站 (目前容量有限) 中提供您的產品，或此舉未獲核准，只需關閉快顯視窗即可。

除了 DataMarket 入口網站之外，若還要在 Azure 入口網站中測試您的資料服務，必須使用測試 Azure 訂用帳戶 ID。此訂用帳戶 ID 可用於識別能夠測試您產品的帳戶。

剪下並貼上您的訂用帳戶 ID，然後按一下核取記號以繼續。

  ![繪圖](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE]只有在 [Azure 管理入口網站](https://manage.windowsazure.com)的測試及預備環境中，才需要這些 Azure 訂用帳戶 ID。在 Azure Marketplace 測試不需要這些 ID。

接下來的畫面會顯示進行中圖示，以指出發行正在進行中，如下圖中的黃色強調顯示所示。推送到預備環境約需 10 到 15 分鐘的時間完成。若時間過長，請先重新整理您的瀏覽器 (在 IE 中請按 F5 鍵)。在極少數的情況下，您的產品可以持續推送了一小時以上的時間還在推送，此時，請按一下 [與我們連絡] 連結，通知我們發生此問題。

  ![繪圖](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

當推送到預備環境作業完成時，進行中圖示會停止移動，且狀態會更新成 [已推送到預備環境]。您現在已可開始測試您的產品。

## 步驟 2.在 DataMarket 中測試已推送到預備環境中的產品

按一下在**在...中檢視您的服務產品**之後顯示的連結，可顯示您的產品在生產環境及 DataMarket 中時，訂閱者會見到的畫面。

  ![繪圖](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

測試或驗證上列標記的 12 個項目，確認所有標誌、價格/交易、文字、影像、文件及連結均正確，並能正常運作。此時也很適合確認您在建立產品時所輸入的測試值，也全由實際值所代。

1. 產品標誌
2. 產品名稱
3. 發行者名稱/您公司網站的連結
4. 您產品的搜尋類別
5. 您產品的支援連結 (用於協助訂閱者)
6. 您產品的的內容描述
7. 描述計費詳細資料的產品方案
8. 實作程式碼的連結
9. 示範如何使用產品資料的範例影像
10. 產品中每項服務的輸入/輸出中繼資料
11. 產品的使用規定
12. 產品資料預覽


最後，請按一下 [探索此資料集]，確認服務在 Datamarket 中能夠正常運作。新視窗會隨即在工具 (稱為「服務總管」) 中開啟，讓您可以預覽對服務的執行的查詢結果。您可以在此視窗中輸入所需的參數，然後檢視您對服務執行查詢所得的結果。此外，顯示的內容會是查詢的 URL。

> [AZURE.NOTE]請務必檢閱視窗最上方所顯示的服務文字描述。您的產品若由多個服務呼叫組成，請按一下視窗底部的各個索引標籤記切換到下一項服務，以進行檢閱及測試。



## 後續步驟
您如有問題需要協助解決，請連絡 [Azure 發行者支援](http://go.microsoft.com/fwlink/?LinkId=272975)。

若您對一切結果感到滿意，並準備好發行您的產品，請閱讀[申請核准發行到生產環境](marketplace-publishing-push-to-production.md)文件。

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

<!-------HONumber=AcomDC_1210_2015--->