<properties 
	pageTitle="為雲端服務啟用遠端桌面 (Node.js)" 
	description="了解如何對裝載 Azure Node.js 應用程式的虛擬機器啟用遠端桌面存取。" 
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






# 在 Azure 中啟用遠端桌面

遠端桌面可讓您存取 Azure 中執行的角色執行個體的桌面
。您可以使用遠端桌面連線
設定虛擬機器或疑難排解
應用程式的問題。

> [AZURE.NOTE] 本文中的步驟僅適用於以 Azure 雲端服務的形式受到代管的節點應用程式。

此工作包含下列步驟：

-   [步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取]
-   [步驟 2：連接到角色執行個體]
-   [步驟 3：使用 Azure PowerShell
    將服務設定成停用遠端桌面存取]

## <a name="step1"> </a>步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取

若要使用遠端桌面，您需要以使用者名稱、密碼和憑證設定服務定義和
服務組態，以
驗證雲端中的角色執行個體。[Azure PowerShell] 包含 **Enable-AzureServiceProjectRemoteDesktop** Cmdlet，其可
為您設定此組態。

從建立服務定義的電腦執行下列步驟
。

1.  在 [開始]**** 功能表中，選取 [Azure PowerShell]****。

	![Azure PowerShell start menu entry][powershell-menu]

2.  將目錄切換至服務目錄，然後輸入 
    **Enable-AzureServiceProjectRemoteDesktop**，接著輸入使用者名稱和
    密碼以在驗證雲端中的角色執行個體時使用
    。

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  將服務組態變更發佈至雲端。在 
    **Azure PowerShell** 提示中輸入 
    **Publish-AzureServiceProject**。

	![publish-azureserviceproject][publish-project]

當這些步驟完成後，雲端服務的角色執行個體
便已設定完成，可供存取遠端桌面。

## <a name="step2"> </a>步驟 2：連接到角色執行個體

您的部署已在 Azure 中啟動並執行，現在您可以連接到
角色執行個體。

1.  在 [Azure 管理入口網站]中選取 **[雲端服務]**，然後選取在上述步驟 1 中部署的服務。

	![azure management portal][cloud-services]

2.  按一下 **[執行個體]**，然後按一下 **[生產]** 或 **[預備]**，以查看服務的執行個體。選取執行個體，然後按一下頁面底部的 **[連接]**。

    ![The instances page][3]

2.  當您按一下 [**連接**]，網頁瀏覽器會提示您儲存 
    .rdp 檔。如果您使用的是 Internet Explorer，請按一下 **[開啟]**。

    ![prompt to open or save the .rdp file][4]

3.  檔案開啟時，會出現下列安全性提示：

    ![Windows security prompt][5]

4.  按一下 [**連接**]，便會出現安全性提示，要求您輸入
    認證以存取執行個體。輸入您
    在 [步驟 1][步驟 1：將服務設定為使用 Azure PowerShell 的遠端桌面存取] 所建立的密碼，然後按一下 [**確定**]。

    ![username/password prompt][6]

連接完成時，遠端桌面連線會顯示 
Azure 中執行個體的桌面。您已成功取得
執行個體的遠端存取權，而且可以執行任何必要工作來
管理您的應用程式。

![Remote desktop session][7]

## <a name="step3"> </a>步驟 3：使用 Azure PowerShell 將服務設定成停用遠端桌面存取

當您不再需要雲端中角色執行個體的遠端桌面連線時，
請使用 [Azure PowerShell] 停用遠端桌面存取

1.  在 [開始]**** 功能表中，選取 [Azure PowerShell]****。

2.  將目錄切換至服務目錄，然後輸入 
    **Disable-AzureServiceProjectRemoteDesktop**：

3.  將服務組態變更發佈至雲端。在
    **Azure PowerShell** 提示中輸入 
    **Publish-AzureServiceProject**：

## 其他資源

- [從遠端存取 Azure 中的角色執行個體] 
- [透過 Azure 角色使用遠端桌面]

  [步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取]: #step1
  [步驟 2：連接到角色執行個體]: #step2
  [步驟 3：使用 Azure PowerShell 將服務設定成停用遠端桌面存取]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure 管理入口網站]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [從遠端存取 Azure 中的角色執行個體]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [透過 Azure 角色使用遠端桌面]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=45--> 
