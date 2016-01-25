<properties
   pageTitle="建立 Marketplace 資料服務的指南 | Microsoft Azure"
   description="如何建立、認證和部署資料服務供他人在 Azure Marketplace 上購買的詳細指示。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="01/04/2016"
      ms.author="hascipio; avikova" />

# 適用於 Azure Marketplace 的資料服務發佈指南
完成步驟 1 [帳戶建立及註冊][link-acct-creation]之後，我們會透過 Azure Marketplace 上資料服務優惠的[一般非技術性](marketplace-publishing-prerequisites.md)和[技術性需求](marketplace-publishing-data-service-creation-prerequisites.md)來引導您。現在，我們將逐步引導您在 Azure Marketplace 的[發佈入口網站][link-pubportal]上建立資料服務優惠。

## 1\.登入發佈入口網站。

移至 [https://publish.windowsazure.com](https://publish.windowsazure.com.)。

**如果是第一次登入發佈入口網站，請使用與貴公司在開發人員中心上註冊的賣方設定檔相同的帳戶** (稍後您可以在發佈入口網站中，將公司的任何員工新增為共同管理員)。

如果這是第一次登入發佈入口網站，請按一下 [發佈資料服務] 磚。

## 2\.在左邊導覽功能表中，選擇 [資料服務] 。

  ![繪圖](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## 3\.建立新的資料服務

針對您的新資料服務優惠填入標題，然後按一下右邊的「+」。

  ![繪圖](media/marketplace-publishing-data-service-creation/step-3.png)

## 4\.檢閱導覽功能表中新建資料服務下方的子功能表。

按一下 [逐步解說] 索引標籤，然後檢閱在 Azure Marketplace 上正常發佈資料服務所需的所有必要步驟。

> [AZURE.TIP]您一律可按一下 [逐步解說] 頁面中的連結，或者使用左邊資料服務優惠子功能表上的索引標籤。

## 5\.建立新的方案。

### 優惠、方案、交易。

每個優惠都可以有多個方案，但至少必須有一 (1) 個方案。當使用者訂閱您的優惠時，他們會訂閱其中一個優惠方案。每個方案都會定義使用者將如何使用您的服務。

Azure Marketplace 目前僅針對資料服務支援以每月訂用帳戶交易為基礎的模型，例如，使用者將根據其訂閱之特定方案的價格來支付每月費用，而且能夠取用該方案所定義的每月交易量。

每個交易通常會定義為您的資料服務將根據傳送至服務之查詢傳回的記錄數。預設值為 100。傳回每個查詢的交易量將是除以 100 且無條件進位到最接近整數的記錄數。

Azure Marketplace 服務層必須負責監視每個查詢所取用的交易量 (計量)。

> [AZURE.IMPORTANT]在月中達到交易限制的使用者將會遭到封鎖，在他們的每月訂用帳戶週期結束之前，將無法繼續使用該服務。

> 該方案或其中一個方案可以 (但不是必須) 包含不限數目的交易。

### 建立方案。
1. 按一下 [加入新方案] 旁邊的 **"+"**。

2. 針對此方案選擇其中一個選項：[無限制] 或 [有限制] 使用量。如果有限制，則需要提供一個月內方案將允許取用的交易數。

    ![繪圖](media/marketplace-publishing-data-service-creation/step-5.1.png)

    發佈入口網站也會建議「方案識別碼」，此識別碼會用來將 UI 中的方案名稱傳達給使用者，而且 Marketplace 服務也會用來識別此方案。如有需要，您可以變更「方案識別碼」。

    > [AZURE.NOTE]「方案識別碼」在每個優惠範圍內必須是唯一的。由於發佈入口網站中會使用許多其他的識別碼，所以方案識別碼將會在第一次發佈至生產環境之後鎖定，而您將無法變更此識別碼。

3. 按一下以接受您的選擇。

4. 接著，系統將詢問您一些有關您最新建立之方案的其他問題。

    ![繪圖](media/marketplace-publishing-data-service-creation/step-5.2.png)


|問題|重要性|
|----|----|
|**這個方案是免費且可供全球使用嗎？**|您可以建立完全免費的方案。如果它是此優惠的唯一方案，這表示您正在 Marketplace 中發佈「免費優惠」。如果它是唯一 (或少數) 的方案，則會為您提供選項，利用每月相對少數的交易量來讓使用者能夠深入了解您的服務。如果答案為「是」，則不會詢問任何進一步的問題。|

> [AZURE.NOTE]使用者一律可升級為付費方案。

|問題|重要性|
|----|----|
|**有免費的試用版可用嗎？**|您可以選擇「完全不試用」，或提供選項來使用您的方案「一個月」。發佈者想要使用此選項，讓使用者能夠了解免費提供一個月優惠的優點。|

> [AZURE.IMPORTANT]如果使用者已建立付款方式 (例如信用卡、Enterprise 合約)，則他們只能購買免費試用版。

> 免費試用一個月之後，除非客戶已起始訂用帳戶取消作業，否則 Azure Marketplace 將從訂用帳戶的日期開始向客戶收取費用。系統將不會為使用者提供任何特殊的通知。

|問題|重要性|
|----|----|
|**這個方案需要促銷代碼才能購買嗎？**| 發佈者會提供選項，藉由為特定的客戶提供名為「促銷代碼」的特殊代碼來限制對其服務方案的存取。只有具備這個促銷代碼的使用者能夠訂閱該方案。如果您選擇 [否]，則表示您同意所有來自可使用該優惠之區域的人 (如需詳細資訊，請參閱[市場行銷內容指南](marketplace-publishing-push-to-staging.md)) 都能訂閱此方案。系統將不會進一步詢問任何問題。|
|**此外，也會向不具有效促銷方案的人隱藏此方案嗎？**|如果上一個問題的答案為「是」，則發佈者會提供選項來完全移除此方案，使其不會出現在 Marketplace 的 UI 中。這表示，客戶將不會在優惠的詳細資料頁面中看見此方案。若使用者收到促銷代碼來進行購買，就能使用這個促銷代碼來訂閱該方案。|

## 6\.建立 Marketplace 行銷內容
如需如何提供 [行銷、定價、支援和類別] 索引標籤中所需資訊的方法，請參閱[市場行銷內容指南](marketplace-publishing-push-to-staging.md)，此指南通用於 Azure Marketplace 中發佈的所有成品。

## 7\.將您的優惠連接到您的服務 (以 SQL Azure 為基礎或以 Web 服務為基礎)。

按一下 [資料服務] 子功能表。

系統將在頁面上半部要求您提供優惠的**命名空間**。

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.png)

下列問題將定義發佈者如何將新建立的優惠公開至 Azure Marketplace(如需詳細資訊，請參閱[資料服務技術性必要條件指南](marketplace-publishing-data-service-creation-prerequisites.md))。

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.2.png)

**發佈以資料庫為基礎的服務**

按一下 [資料庫]。隨即會出現下列頁面：

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.3.png)

若要根據 SQL Azure 資料庫建立資料集的 CSDL 對應：

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.4.png)

然後針對每個資料表

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.6.png)

如果 Web 服務

  ![繪圖](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT]如需建立 CSDL Web 服務的詳細指示和範例，請參閱[透過 CSDL 將現有的 Web 服務對應至 OData](marketplace-publishing-data-service-creation-odata-mapping.md)。

## 後續步驟
現在，您已經建立資料服務優惠，請確定您已完成[市場行銷內容指南](marketplace-publishing-push-to-staging.md)中的指示，然後再前往[在預備環境中測試您的資料服務](marketplace-publishing-data-service-test-in-staging.md)。

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
- 如果您有興趣全面了解 OData 對應程序和用途，請閱讀[資料服務 OData 對應](marketplace-publishing-data-service-creation-odata-mapping.md)一文來檢閱定義、結構和指示。
- 如果您有興趣學習和了解特定節點及其參數，請閱讀[資料服務 OData 對應節點](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)一文，來了解定義和說明、範例，以及使用案例內容。
- 如果您有興趣檢閱範例，請閱讀[資料服務 OData 對應範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)一文，來查看範例程式碼，並了解程式碼語法與內容。


[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=AcomDC_0114_2016-->