<properties  linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="" editor="" />

# 如何建立和部署雲端服務

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Azure 管理入口網站提供兩種方法讓您建立和部署雲端服務：**快速建立**和**自訂建立**。

本主題說明如何使用「快速建立」方法建立新的雲端服務，然後使用 **上傳** 上傳雲端服務封裝並在 Azure 中部署。您使用這個方法時，Azure 管理入口網站會提供您進行時完成所有要求的便利連結。如果您準備在建立雲端服務時加以部署，可以同時使用**自訂建立**進行這兩項作業。

**注意** 如果您計劃從 Windows Team Foundation Services (TFS) 發佈您的雲端服務，請使用「快速建立」，然後從**快速啟動**或儀表板設定 TFS 發佈。如需詳細資訊，請參閱[使用 Team Foundation Service 預覽持續傳遞至 Azure][1] (英文) 或**快速啟動**頁面的說明。

## 目錄

* [概念](#concepts)
* [準備您的應用程式](#prepare)
* [開始之前](#begin)
* [作法：使用「快速建立」建立雲端服務](#quick)
* [作法：上傳雲端服務的憑證](#uploadcertificate)
* [作法：部署雲端服務](#deploy)

<h2><a  id="concepts" ></a> 概念</h2>


需要三個元件才能部署應用程式成為 Azure 中的雲端服務：

> * **服務定義檔** 定義服務模型的雲端服務定義檔 (.csdef)，包含角色數。

> * **服務組態檔** 雲端服務組態檔 (.cscfg) 提供雲端服務和個別角色的組態設定，包括角色執行個體數。

> * **服務封裝** 服務封裝 (.cspkg) 包含應用程式程式碼和服務定義檔。

<h2><a  id="prepare" ></a>準備您的應用程式</h2>


您部署雲端服務之前，必須先從應用程式程式碼和雲端服務組態檔 (.cscfg) 建立雲端服務封裝 (.cspkg)。各個雲端服務封裝均包含應用程式檔案和組態。服務組態檔提供組態設定。

Azure SDK 提供準備這些必要部署檔案的工具。您可以從 [Azure 下載][2] (英文) 頁面安裝 SDK，使用您偏好的語言開發應用程式程式碼。

如果您初次使用雲端服務，可以從 [Azure 程式碼範例][3] (英文) 下載要使用的範例雲端服務封裝 (.cspkg) 和服務組態檔 (.cscfg)。

三個雲端服務功能需要特別組態，您才能匯出服務封裝：

* 如果您要部署使用安全通訊端層 (SSL) 進行資料加密的雲端服務，請針對 SSL 設定應用程式。如需詳細資訊，請參閱[設定 HTTPS 端點的 SSL 憑證][4]。

* 如果您要設定角色執行個體的遠端桌面連線，請設定遠端桌面的角色。如需準備遠端存取服務定義檔的詳細資訊，請參閱[對於角色設定遠端桌面連線概觀][5]。

* 如果您要設定雲端服務的詳細資訊監視，請啟用雲端服務的 Azure 診斷。*最小監視* (預設監視層級) 使用從角色執行個體 (虛擬機器) 的主機作業系統收集的效能計數器。「詳細資訊監視」會按照角色執行個體內的效能資料收集其他度量，以便進一步分析應用程式處理期間發生的問題。若要了解如何啟用 Azure 診斷，請參閱[啟用 Azure 診斷][6] (英文)。

<h2><a  id="begin" ></a>開始之前</h2>


* 如果您尚未安裝 Azure SDK，請按一下 **Install Azure SDK** 開啟 [Azure 下載頁面][2] (英文)，然後對於開發程式碼所偏好使用的語言下載 SDK。(您稍後將有機會這麼做。)

* 如果任何角色執行個體需要憑證，請建立憑證。雲端服務需要含有私密金鑰的 .pfx 檔。您建立並部署雲端服務時，可以將憑證上傳至 Azure。如需建立憑證的資訊，請參閱[設定 HTTPS 端點的 SSL 憑證][4]。

* 如果您計劃將雲端服務部署至同質群組，請建立同質群組。您可以使用同質群組，將雲端服務和其他 Azure 服務部署到區域中的同一個位置。您可以在管理入口網站的 **網路** 區域中出現的 **同質群組** 頁面上建立同質群組。如需詳細資訊，請參閱 **同質群組** 頁面的說明。

<h2><a  id="quick" ></a>作法：使用「快速建立」建立雲端服務</h2>


1.  在**管理入口網站][7]中，按一下 [新增**，並按一下 **雲端服務**，然後按一下 **快速建立**。
    
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2.  在 **URL** 中，輸入要在公用 URI 中使用的子網域名稱，以存取生產部署中的雲端服務。生產部署的 URL 格式是：http://*myURL*.cloudapp.net。

3.  在 **Region/Affinity Group** 中，選取要將雲端服務部署到其中的地理區域或同質群組。如果您要將雲端服務部署到區域中其他 Azure 服務所在的同一個位置，請選取同質群組。
    
    > **WACOM.NOTE] 若要建立同質群組，請開啟管理入口網站的 [網路** 區域，並按一下
    > **同質群組**，然後按一下 **建立新的同質群組** 或 **建立**。您可以使用在舊版 Azure
    > 管理入口網站中建立的同質群組。而且，您可以使用 Azure 服務管理 API
    > 建立和管理同質群組。如需詳細資訊，請參閱[同質群組的相關作業][8]。

4.  按一下 **建立雲端服務**。
    
    您可以在視窗底端的訊息區域監視程序的狀態。
    
    **雲端服務** 區域隨即開啟，其中顯示新的雲端服務。狀態變更為 [已建立] 時，表示雲端服務建立成功完成。
    
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)
    
    如果雲端服務中的任何角色需要安全通訊端層 (SSL) 資料加密的憑證，而且憑證尚未上傳到 Azure，您必須先上傳憑證，才能部署雲端服務。您上傳憑證後，在角色執行個體中執行的任何 Windows 應用程式即可存取該憑證。

<h2><a  id="uploadcertificate" ></a>作法：上傳雲端服務的憑證</h2>


1.  在**管理入口網站][7]中，按一下 [雲端服務**。然後，按一下雲端服務的名稱來開啟儀表板。
    
    ![CloudServices_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)

2.  按一下 **憑證** 開啟 **憑證** 頁面，如下所示。
    
    ![CloudServices_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3.  按一下 **新增新憑證** 或 **上傳**。**Add a Certificate** 隨即開啟。
    
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4.  在 **憑證檔案** 中，使用 **瀏覽** 選取要使用的憑證 (.pfx 檔)。

5.  在 **密碼** 中，輸入憑證的私密金鑰。

6.  按一下 [確定] (核取記號)。
    
    您可以在訊息區域查看上傳的進度，如下所示。上傳完成時，憑證將新增至表格中。在訊息區域中，按一下向下箭頭關閉訊息，或按一下 X 移除訊息。
    
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)
    
    您可以從儀表板或 **快速啟動** 部署雲端服務。

<h2><a  id="deploy" ></a>作法：部署雲端服務</h2>


1.  在**管理入口網站][7]中，按一下 [雲端服務**。然後，按一下雲端服務的名稱來開啟儀表板。

2.  按一下 **快啟啟動** (**儀表板** 左邊的圖示) 開啟 **快速開啟** 頁面，如下所示。(您也可以使用儀表板上的 **上傳** 部署雲端服務。)
    
    ![CloudServices_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

3.  如果您尚未安裝 Azure SDK，請按一下 **Install Azure SDK** 開啟 [Azure 下載頁面][2] (英文)，然後對於開發程式碼所偏好使用的語言下載 SDK。
    
    在下載頁面上，您也能夠安裝以 Node.js、Java、PHP 和其他語言開發 Web 應用程式所用的用戶端程式庫和原始程式碼，這些都可以部署為可擴充的 Azure 雲端服務。
    
    > [WACOM.NOTE] 對於過去建立的雲端服務 (過去稱為*代管服務*)，您將需要確定虛擬機器 (角色執行個體)
    > 上的來賓作業系統與您安裝的 Azure SDK 版本相容。如需詳細資訊，請參閱 [Azure SDK .NET 版本資訊][9]。

4.  按一下 **新增生產部署** 或 **新增預備部署**。
    
    如果您想要先在 Azure 中測試雲端服務，再部署到生產環境，則您可以部署到預備環境。在預備環境中，雲端服務的全域唯一識別碼 (GUID) 將識別 URL (*GUID*.cloudapp.net) 中的雲端服務。在生產環境中，將使用指派給您更易記的 DNS 前置詞 (例如，*myservice*.cloudapp.net)。您準備將預備的雲端服務升級到生產環境時，請使用 **交換** 將用戶端要求重新導向到該部署。
    
    選取部署環境時，**Upload a Package** 隨即開啟。
    
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

5.  在 **部署名稱** 中，輸入新部署的名稱，例如，MyCloudServicev1。

6.  在 **封裝** 中，使用 **瀏覽** 選取要使用的服務封裝檔 (.cspkg)。

7.  在 **組態** 中，使用 **瀏覽** 選取要使用的服務組態檔 (.cscfg)。

8.  如果雲端服務將包含只有一個執行個體的任何角色，請選取 **Deploy even if one or more roles contain a single instance** 核取方塊，讓部署繼續進行。

	如果每個角色至少有兩個執行個體，Azure 只能保證在維護和服務更新期間存取雲端服務的成功率為 99.95%。若有需要，您可以在部署雲端服務後，在 **Scale** 頁面上新增其他角色執行個體。如需詳細資訊，請參閱[服務等級協定][10]。

1.  按一下 [確定] (核取記號) 開始雲端服務部署。
    
    您可以在訊息區域監視部署的狀態。按一下向下箭頭將隱藏訊息。
    
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### 確認部署是否成功完成

1.  按一下 **儀表板**。

2.  在 **quick glance** 下，按一下網站 URL，在網頁瀏覽器中開啟您的雲端服務。


    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)



[1]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
[2]: http://www.windowsazure.com/en-us/develop/downloads/
[3]: http://code.msdn.microsoft.com/windowsazure/
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/ff795779.aspx
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433010.aspx
[6]: http://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/
[7]: http://manage.windowsazure.com/
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/hh552718.aspx
[10]: http://www.windowsazure.com/en-us/support/legal/sla/