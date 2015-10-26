<properties
   pageTitle="建立 Marketplace 解決方案範本的指南 | Microsoft Azure"
   description="如何建立、認證和部署一個多 VM 映像解決方案範本供他人在 Azure Marketplace 上購買的詳細指示。"
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
      ms.date="10/09/2015"
      ms.author="hascipio; v-divte" />

# 建立 Azure Marketplace 解決方案範本的指南
完成步驟 1 [帳戶建立和註冊][link-acct-creation]之後，我們引導您依照[建立解決方案範本的技術性必要條件](marketplace-publishing-solution-template-creation-prerequisites.md)建立與 Azure 相容的解決方案範本。現在，我們將逐步引導您在 Azure Marketplace 的[發佈入口網站][link-pubportal]上建立一個多 VM 的解決方案範本。

## 在發佈入口網站中建立您的解決方案範本供應項目
移至 [https://publish.windowsazure.com](http://publish.windowsazure.com)。**如果是第一次登入[發佈入口網站](https://publish.windowsazure.com/)，請使用與您公司的賣方設定檔註冊時相同的帳戶。** 稍後您可以在發佈入口網站中將您公司的任何員工新增為共同管理員。

### 1\.選取 [解決方案範本]

  ![繪圖][img-pubportal-menu-sol-templ]

### 2\.建立新的解決方案範本

  ![繪圖][img-pubportal-sol-templ-new]

### 3\.使用拓撲開始
解決方案範本是所有其拓撲的「父項」。您可以在一個供應項目/解決方案範本中定義多個拓撲。當供應項目進入預備環境時，它的所有拓撲也會一起進入。以下是定義您的供應項目的步驟。- 建立拓撲 – **拓樸識別項**通常是解決方案範本的拓撲名稱。拓撲識別項將用於 URL，如下所示：

  Azure Marketplace：http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure Preview 入口網站：https://ms.portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}

- 加入新的版本  

### 4\.讓您的拓撲版本取得認證
上傳一個 zip 檔案，包含佈建該特定拓撲版本所需的所有檔案。此 zip 檔案必須包含下列項目：*mainTemplate.json* 及其根目錄的 *createUiDefinition.json* 檔案，任何連結的範本和所有必要的指令碼。

上傳 zip 檔案之後，按一下 [要求認證]。Microsoft 認證團隊會檢閱檔案並認證拓撲。

您也可以使用下列步驟，為使用者驗證沒有實際部署的建立體驗。

1. 儲存 *createUiDefinition.json*，產生絕對的 URL。此 URL 必須可公開存取。
2. 將 URL [[http://www.url-encode-decode.com/](http://www.url-encode-decode.com/)] 編碼。
3. 將反白顯示的文字取代為需要驗證的 *createUiDefinition.json* 的位置 (編碼的 URL)。

  https://portal.azure.com/?clientOptimizations=false#blade/Microsoft_Azure_Compute/CreateMultiVmWizardBlade/internal_bladeCallId/anything/internal_bladeCallerParams/ **{"initialData":{},"providerConfig":{"createUiDefinition":"http://yoururltocreateuidefinition.jsonURLencoded"}}**
  
4. 複製 URL 並在任何瀏覽器中貼上 URL，檢視 createUiDefinition.json 檔案的使用者體驗。

> [AZURE.TIP]在您的開發人員建立解決方案範本拓撲和取得認證的同時，貴公司的業務/行銷/法律部門就可以處理行銷和法律內容。

## 後續步驟
現在，您建立了解決方案範本並提交了認證所需的 zip 檔案和必要檔案，在準備供應項目在預備環境中進行測試之前，您可以繼續並依照 [Marketplace 行銷內容指南](marketplace-publishing-push-to-staging.md)的指示進行。

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

**VM 映像** - [關於 Azure 中的虛擬機器映像](https://msdn.microsoft.com/library/azure/dn790290.aspx)

**VM 延伸模組** - [VM 代理程式和 VM 延伸模組概觀](https://msdn.microsoft.com/library/azure/dn832621.aspx) - [Azure VM 延伸模組與功能](https://msdn.microsoft.com/library/azure/dn606311.aspx)

**ARM** - [撰寫 Azure ARM 範本](../resource-group-authoring-templates/) - [簡單 ARM 範本範例](https://github.com/rjmax/ArmExamples)

**儲存體帳戶節流** - [如何監視儲存體帳戶節流](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) - [進階儲存體](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-whzh-TWing-premium-storage)

[img-pubportal-menu-sol-templ]: media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]: media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=Oct15_HO3-->