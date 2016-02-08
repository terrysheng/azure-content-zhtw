<properties 
	pageTitle="如何設定雲端服務 | Microsoft Azure" 
	description="了解如何在 Azure 中設定雲端服務。了解更新雲端服務組態和設定角色執行個體的遠端存取。這些範例使用 Azure 入口網站。" 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2016"
	ms.author="adegeo"/>




# 如何設定雲端服務

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-configure-portal.md)
- [Azure classic portal](cloud-services-how-to-configure.md)

您可以在 Azure 入口網站中設定雲端服務的最常用設定。或者，如果您想要直接更新組態檔，可以下載要更新的服務組態檔、上傳更新過的檔案，然後將雲端服務更新為使用這些組態變更。使用上述任一種方式，都會將組態更新推送到所有角色執行個體。

您也可以對雲端服務中執行的一個或所有角色啟用遠端桌面連線。遠端桌面可讓您存取執行中應用程式的桌面，然後疑難排解和診斷問題。即使您未在應用程式開發期間對服務定義檔 (.csdef) 設定遠端桌面，還是可以對您的角色啟用遠端桌面連線。您不需要重新部署應用程式，就可以啟用遠端桌面連線。

每個角色至少必須有兩個角色執行個體，Azure 才能確保服務在組態更新期間有 99.95% 的可用性。如此才能讓一個虛擬機器在受到更新時，還有另一個虛擬機器可以處理用戶端要求。如需詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)。

## 變更雲端服務

1. 在 [Azure 入口網站](https://portal.azure.com/)，瀏覽至您的雲端服務。

2. 按一下**設定**圖示或 [**基本功能/所有設定**] 連結，開啟 [**設定**] 刀鋒視窗。

    ![設定頁面](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    您可以從這裡檢視**屬性**、變更**組態**、管理**憑證**，以及管理能夠存取此雲端服務的**使用者**。

2. 在 [**監視**] 區段下，您可以按一下任何磚來設定警示。

    ![雲端服務監視](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)
    
3. 在 [**角色和執行個體**] 區段下，您可以按一下任何雲端服務角色來管理執行個體。

    ![雲端服務執行個體](./media/cloud-services-how-to-configure-portal/cs-instance.png)
    
    您可以從這裡遠端連接、重新啟動或重新安裝雲端服務映像。
    
    ![雲端服務執行個體按鈕](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

>[AZURE.NOTE]
您無法使用 **Azure 入口網站**來變更用於雲端服務的作業系統，只能透過 [Azure 傳統入口網站](http://manage.windowsazure.com/)變更此設定。[這裡](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file)有詳細說明。

## 更新雲端服務組態檔

1. 首先，下載現有的雲端服務組態檔 (.cscfg)。

    1. 在 [Azure 入口網站](https://portal.azure.com/)，瀏覽至您的雲端服務。

    2. 按一下**設定**圖示或 [**基本功能/所有設定**] 連結，開啟 [**設定**] 刀鋒視窗。

        ![設定頁面](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    3. 按一下 [**組態**] 項目。

        ![設定刀鋒視窗](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
    
    4. 按一下 [**下載**] 按鈕。

        ![下載](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

2. 在您更新服務組態檔之後，請上傳和套用組態更新：

    1. 遵循上述的前 3 個步驟，開啟雲端服務的 [**組態**] 刀鋒視窗。
    
    2. 按一下 [**上傳**] 按鈕。

        ![上傳](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
    3. 選取 .cscfg 檔案，然後按一下 [**確定**]。

## 設定對角色執行個體的遠端存取

您無法使用 **Azure 入口網站**設定遠端存取，只能透過 [Azure 傳統入口網站](http://manage.windowsazure.com/)變更此設定。[這裡](cloud-services-role-enable-remote-desktop.md)有相關描述。
			
## 後續步驟

* 了解如何[部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。
* 設定[自訂網域名稱](cloud-services-custom-domain-name-portal.md)。
* [管理您的雲端服務](cloud-services-how-to-manage-portal.md)。
* 設定 [SSL 憑證](cloud-services-configure-ssl-certificate-portal.md)。

<!---HONumber=AcomDC_0128_2016-->