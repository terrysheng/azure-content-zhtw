<properties 
	pageTitle="對雲端服務啟用遠端桌面 (Node.js)" 
	description="了解如何對裝載 Azure Node.js 應用程式的虛擬機器啟用遠端桌面存取。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>






# 在 Azure 中啟用遠端桌面

遠端桌面可讓您存取執行 Azure 之角色執行個體的桌面。您可以使用遠端桌面連接來設定虛擬機器，或對應用程式的問題進行疑難排解。

> [AZURE.NOTE]本文適用於以 Azure 雲端服務的形式代管的節點應用程式。


## 步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取

若要使用遠端桌面，您必須使用在雲端中進行角色執行個體驗證的使用者名稱、密碼和憑證，設定您的服務定義和服務組態。[Azure PowerShell] 包含 **Enable-AzureServiceProjectRemoteDesktop** Cmdlet，可為您執行這項設定。

在建立服務定義的電腦中，執行下列步驟。

1.  在 [開始] 功能表中，選取 [Azure PowerShell]。

	![Azure PowerShell start menu entry][powershell-menu]

2.  將目錄切換至服務目錄，輸入 **Enable-AzureServiceProjectRemoteDesktop**，然後輸入您在雲端中驗證角色執行個體時所使用的使用者名稱和密碼。

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  將服務組態變更發佈至雲端。在 **Azure PowerShell** 提示下，輸入 **Publish-AzureServiceProject**。

	![publish-azureserviceproject][publish-project]

完成前述步驟後，雲端中的服務即會設定其角色執行個體的遠端桌面存取。

## 步驟 2：連接到角色執行個體

您的部署已在 Azure 中啟動並執行，現在您可以連接到角色執行個體。

1.  在 [Azure 管理入口網站]中選取 [雲端服務]，然後選取先前在步驟 1 中部署的服務

	![azure management portal][cloud-services]

2.  按一下 [執行個體]，然後按一下 [生產] 或 [執行]，以檢視服務的執行個體。選取執行個體，然後按一下頁面底部的 [連接]。

    ![The instances page][3]

2.  當您按一下 [連接] 時，網頁瀏覽器會提示您儲存 .rdp 檔案。如果您使用的是 Internet Explorer，請按一下 [開啟]。

    ![prompt to open or save the .rdp file][4]

3.  檔案開啟時，會出現下列安全性提示：

    ![Windows security prompt][5]

4.  按一下 [連接] 時會出現安全性提示，要求您輸入存取執行個體所需的認證。請輸入您在[步驟 1][Step 1: Configure the service for Remote Desktop access using Azure PowerShell] 中建立的密碼，然後按一下 [確定]。

    ![username/password prompt][6]

連接建立後，「遠端桌面連接」會在 Azure 中顯示執行個體的桌面。您已成功取得執行個體的遠端存取，並且可執行任何必要工作來管理您的應用程式。

![Remote desktop session][7]

## 步驟 3：將服務設定成停用遠端桌面存取 

當您不需要再透過遠端桌面連接到雲端中的角色執行個體時，請使用 [Azure PowerShell] 停用遠端桌面存取

1.  在 [開始] 功能表中，選取 [Azure PowerShell]。

2.  將目錄切換至服務目錄，然後輸入 **Disable-AzureServiceProjectRemoteDesktop**：

3.  將服務組態變更發佈至雲端。在 **Azure PowerShell** 提示下，輸入 **Publish-AzureServiceProject**：

## 其他資源

- [從遠端存取 Azure 中的角色執行個體] 
- [搭配使用遠端桌面與 Azure 角色]


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
[搭配使用遠端桌面與 Azure 角色]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=54-->