<properties
   pageTitle="測試 VM 供應項目的 Marketplace 表現 | Microsoft Azure"
   description="了解如何測試您的 VM 供應項目在 Azure Marketplace 的表現。"
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
   ms.date="10/09/2015"
   ms.author="hascipio" />

# 在預備環境中測試 VM 供應項目的 Marketplace 表現

預備環境代表將您的 SKU 部署在私人的「沙箱」中，您可以在部署到 Marketplace 之前在沙箱中測試與驗證其功能。SKU 會出現在預備環境中，就如同客戶已部署該項目一樣。您的 VM 映像必須經過認證才能推送至預備環境。

## 步驟 1：推送供應項目至預備環境

1. 按一下 [發佈] 索引標籤中的 [推送至準備環境]。

  ![繪圖](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. 此時發佈入口網站可能會通知您更正任何錯誤或不一致。
3.	提供您要在 [Azure Preview 入口網站](https://portal.azure.com) 的快顯方塊中用來預覽供應項目的 Azure 訂用帳戶清單，如上方螢幕擷取畫面所示。
4. 使用上一個步驟中列出的 Azure 訂用帳戶之一登入 [Azure Preview 入口網站](http://portal.azure.com)。
5. 尋找您的供應項目，並驗證您的 VM 映像點：
  1. 行銷內容正確顯示在資源庫中

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. VM 映像的端對端部署

> [AZURE.IMPORTANT]您的供應項目將留在預備環境中，直到您透過發佈入口網站 [發佈 > "要求核准以推送至生產環境"] 通知 Microsoft 您已準備好推送至生產為止。這是所有團隊成員為您即將上市的供應項目做好萬全準備並徹底檢查的最佳時機。

> 跨資料中心的複寫作業需要 24-48 小時的時間。複寫完成時，您的供應項目會列在 [Azure Marketplace](http://azure.microsoft.com/marketplace) 中。

## 後續步驟
現在您的供應項目「預備」好了，您已經測試過它的功能和行銷內容，您可以繼續進行最終的提供和 (或) SKU 發佈階段，即**步驟 4**，[將您的供應項目部署至 Marketplace](marketplace-publishing-push-to-production.md)。

## 另請參閱
- [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO3-->