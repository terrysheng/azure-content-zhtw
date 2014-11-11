<properties linkid="dev-nodejs-enablestaging" urlDisplayName="Staging Deployment" pageTitle="Stage a cloud service deployment (Node.js) - Azure" metaKeywords="Azure staging, Azure application staging, Azure test environment, Azure staging environment, Azure Virtual IP swap, Azure VIP swap" description="Learn how to deploy your Azure application to a staging environment, then deploy to a production environment using Virtual IP (VIP) swap." metaCanonical=" " services="cloud-services" documentationCenter="nodejs" title="Staging an Application in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# 在 Azure 中預備應用程式

將封裝的應用程式移至生產環境以便在網際網路可供存取之前，可先將該應用程式部署至 Azure 中的預備環境進行測試。預備環境與生產環境完全相同，差別在於您只能以 Azure 產生的隱藏 URL來存取預備應用程式。在確認應用程式可正常運作之後，就可以執行虛擬 IP (VIP) 交換，將它部署至生產環境。


<b>注意</b>
<p>本文中的步驟僅適用於以 Azure 雲端服務的形式受到代管的節點應用程式。</p>

此工作包含下列步驟：

-   [步驟 1：預備應用程式][步驟 1：預備應用程式]
-   [步驟 2：交換 VIP 將應用程式部署至生產環境][步驟 2：交換 VIP 將應用程式部署至生產環境]

## <span id="step1"></span></a>步驟 1：預備應用程式

此工作涵蓋如何使用 **Windows Azure PowerShell** 來預備應用程式。

1.  發行服務時，只要將 **-Slot** 參數傳給 **Publish-AzureServiceProject** Cmdlet 即可。

    **Publish-AzureServiceProject -Slot staging**

2.  登入 [Azure 管理入口網站][Azure 管理入口網站]，選取 [雲端服務]。當雲端服務已建立且 [預備] 欄狀態更新為 [正在執行] 之後，按一下服務名稱。

    ![portal displaying a running service][portal displaying a running service]

3.  選取 [儀表板]，然後選取 [預備]。

    ![cloud service dashboard][cloud service dashboard]

4.  請注意右邊的 [網站 URL] 項目中的值。DNS 名稱是 Azure 產生的隱藏內部識別碼。

    ![site url][site url]

現在，您可以利用預備網站 URL，驗證應用程式在預備環境中可正常運作。

在升級案例中，預備應用程式是已部署至生產環境之應用程式的升級版本，您可以[交換 VIP 將應用程式升級至生產環境][步驟 2：交換 VIP 將應用程式部署至生產環境]。

## <span id="step2"></span></a>步驟 2：交換 VIP 將應用程式升級至生產環境

在預備環境中驗證應用程式的升級版本之後，您可以交換預備和生產環境的虛擬 IP (VIP)，快速將應用程式移至生產環境。

<div class="dev-callout">
<b>注意</b>
<p>此步驟假設您已將應用程式部署至生產環境，
也已預備應用程式的
升級版本。</p>
</div>

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]、按一下 [雲端服務]，然後選取服務名稱。

2.  從 [儀表板] 中，選取 [預備]，然後按一下頁面底部的 [交換]。這樣會開啟 [VIP 交換] 對話方塊。

    ![vip swap dialog][vip swap dialog]

3.  檢閱資訊，然後按一下 [確定]。兩個部署開始更新，預備部署會切換至生產環境，而生產部署會切換至預備環境。

您已與預備中的部署交換 IP，順利預備部署並升級生產部署。

## 其他資源

-   [如何在 Azure 中交換 VIP 將服務升級部署至生產環境][如何在 Azure 中交換 VIP 將服務升級部署至生產環境]
-   [在 Azure 中管理部署的概觀][在 Azure 中管理部署的概觀]

  [步驟 1：預備應用程式]: #step1
  [步驟 2：交換 VIP 將應用程式部署至生產環境]: #step2
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [portal displaying a running service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
  [cloud service dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [site url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip swap dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [如何在 Azure 中交換 VIP 將服務升級部署至生產環境]: http://msdn.microsoft.com/zh-TW/library/windowsazure/ee517253.aspx
  [在 Azure 中管理部署的概觀]: http://msdn.microsoft.com/zh-TW/library/windowsazure/hh386336.aspx
