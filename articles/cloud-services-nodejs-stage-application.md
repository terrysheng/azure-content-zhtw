<properties 
	pageTitle="預備雲端服務部署 (Node.js) - Azure" 
	description="了解如何將 Azure 應用程式部署至預備環境，然後使用虛擬 IP (VIP) 交換來部署至生產環境。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>







# 在 Azure 中預備應用程式

封裝的應用程式可以先部署到 Azure 的預備環境
進行測試後，再移到生產
環境，讓應用程式透過網際網路進行存取。
預備環境和生產環境完全一致，但有一點例外，
您只能使用 Azure 產生的隱藏 URL 存取預備應用程式
。驗證
應用程式可正常運作之後，就能將它部署到生產
環境，方法是執行虛擬 IP (VIP) 交換作業。

> [AZURE.NOTE] 本文中的步驟僅適用於以 Azure 雲端服務的形式受到代管的節點應用程式。

此工作包含下列步驟：

-   [步驟 1：預備應用程式]
-   [步驟 2：交換 VIP 將應用程式部署至生產環境]

<h2><a id="step1"></a>步驟 1：預備應用程式</h2>

這項工作涵蓋如何使用 **Windows 
Azure PowerShell** 來預備應用程式。

1.  在發行服務時，直接將 **-Slot** 參數傳給 
    **Publish-AzureServiceProject** Cmdlet。

    **Publish-AzureServiceProject -Slot staging**

2.  登入 [Azure 管理入口網站]，然後選取 [**雲端服務**]。在建立好雲端服務且 [**預備**] 欄狀態已更新為 [**執行中**] 後，按一下服務名稱。

	![portal displaying a running service][cloud-service]

3.  選取 [**儀表板**]，然後選取 [**預備**]。

	![cloud service dashboard][cloud-service-dashboard]

4. 記下右邊的 [**網站 URL**] 項目值。DNS 名稱是 Azure 產生的隱藏內部識別碼。

    ![site url][cloud-service-staging-url]

現在，您可以利用預備網站 URL，驗證應用程式在預備環境中可正常運作。

若為升級案例，亦即預備應用程式是
已部署至生產環境之應用程式的升級版本，您可以
[藉由交換 VIP 升級生產環境中的應用程式
][步驟 2：交換 VIP 將應用程式部署至生產環境]

<h2><a id="step2"></a>步驟 2：藉由交換 VIP 升級生產環境中的應用程式</h2>

驗證預備環境中的應用程式升級版本後，
您可以快速地使其可用於生產環境，方法是
交換預備和生產環境的虛擬 IP (VIP)
。

> [AZURE.NOTE] 此步驟假設您已將應用程式部署至生產環境，
也已預備應用程式的升級版本
。

1.  登入 [Azure 管理入口網站]，
    按一下 [**雲端服務**]，然後選取服務名稱。

2.  從 [**儀表板**] 選取 [**預備**]，然後按一下頁面底部的 [**交換**]。如此會開啟 [VIP 交換] 
    對話方塊。

    ![vip swap dialog][vip-swap-dialog]

3.  檢閱資訊，然後按一下 [**確定**]。兩個部署
    便會開始更新，預備部署會切換為生產部署，
    生產部署則會切換為預備部署。

您已順利預備部署，並透過交換 VIP 使用預備環境中的部署升級生產
部署。

## 其他資源

- [如何在 Azure 中交換 VIP 將服務升級部署至生產環境]
- [在 Azure 中管理部署的概觀]

  [步驟 1：預備應用程式]: #step1
  [步驟 2：交換 VIP 將應用程式部署至生產環境]: #step2
  [Azure 管理入口網站]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [如何在 Azure 中交換 VIP 將服務升級部署至生產環境]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [在 Azure 中管理部署的概觀]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx

<!--HONumber=45--> 
