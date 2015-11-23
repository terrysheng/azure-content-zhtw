<properties
   pageTitle="針對 Marketplace 測試您的解決方案範本供應項目 | Microsoft Azure"
   description="了解如何針對 Azure Marketplace 測試您的解決方案範本供應項目。"
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
   ms.date="10/08/2015"
   ms.author="hascipio; v-divte" />

# 在預備環境中測試您的解決方案範本供應項目
預備環境代表將您的供應項目部署在私人的「沙箱」中，您可以在推送到生產環境之前在沙箱中測試與驗證其功能。供應項目會出現在預備環境中，就如同客戶已部署該項目一樣。您的供應項目**必須經過認證才能推送至預備環境**。

預備供應項目之後，您可以在 [Azure Preview 入口網站](https://ms.portal.azure.com/)中檢視並測試供應項目。

請遵循下列步驟，將您的供應項目推送至預備環境並在 [Azure Preview 入口網站](https://ms.portal.azure.com/)中執行測試。

1.	瀏覽至[發佈入口網站](https://publish.windowsazure.com) -> 選取 [解決方案範本] 索引標籤 -> 您的供應項目 -> [發佈] -> 按一下 [推送至預備環境]
2.	提供您將用來預覽/測試供應項目的 Azure 訂用帳戶清單。
3.	使用您在上一個步驟中用來預備供應項目的相同訂用帳戶 ID 登入 Azure Preview 入口網站。
4.	在 Azure Preview 入口網站中的下面所提的點上至少執行一回合的測試。
  -	行銷內容正確顯示在資源庫中
  -	拓撲的端對端部署
  -	執行效能測試和壓力測試
  -	確定您的拓撲遵守最佳作法。

## 後續步驟
如果您很滿意結果，則可以繼續進行最後的供應項目發佈階段，也就是**步驟 4**：[將您的供應項目部署至 Marketplace](marketplace-publishing-push-to-production.md)。否則，請在您的供應項目中進行變更並再次要求憑證。

> [AZURE.NOTE]若為行銷內容變更，則不需要憑證。

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO3-->