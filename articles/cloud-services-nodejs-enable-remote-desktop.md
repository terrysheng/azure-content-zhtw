<properties urlDisplayName="Enable Remote Desktop" pageTitle="為雲端服務啟用遠端桌面 (Node.js)" metaKeywords="Azure Node.js 遠端存取, Azure Node.js 遠端連線, Azure Node.js VM 存取, Azure Node.js 虛擬機器存取" description="了解如何對裝載 Azure Node.js 應用程式的虛擬機器啟用遠端桌面存取。 " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# 在 Azure 中啟用遠端桌面

遠端桌面可讓您存取執行 Azure 之角色執行個體的桌面。您可以使用遠端桌面連接來設定虛擬機器，或對應用程式的問題進行疑難排解。

<div class="dev-callout">
	<b>注意</b>
	<p>本文中的步驟僅適用於以 Azure 雲端服務的形式受到代管的節點應用程式。</p>
	</div>

此工作包含下列步驟：

-   [步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取]
-   [步驟 2：連接到角色執行個體]
-   [步驟 3：將服務設定成停用遠端桌面存取
    (使用 Azure PowerShell)]

## <a name="step1"> </a>步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取

若要使用遠端桌面，您必須使用在雲端中進行角色執行個體驗證的使用者名稱、密碼和憑證，設定您的服務定義和服務組態。[Azure PowerShell] 包含 **Enable-AzureServiceProjectRemoteDesktop** Cmdlet，可為您執行這項設定。

在建立服務定義的電腦中，執行下列步驟。

1.  在 [開始]**** 功能表中，選取 [Azure PowerShell]****。

	![Azure PowerShell start menu entry][powershell-menu]

2.  切換至服務目錄，輸入 **Enable-AzureServiceProjectRemoteDesktop**，然後輸入向雲端中角色執行個體進行驗證時，所要使用的使用者名稱和密碼。

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  將服務組態變更發佈至雲端。在 **Azure PowerShell** 命令提示字元中，輸入 **Publish-AzureServiceProject**。

	![publish-azureserviceproject][publish-project]

完成前述步驟後，雲端中的服務即會設定其角色執行個體的遠端桌面存取。

## <a name="step2"> </a>步驟 2：連接到角色執行個體

您的部署已在 Azure 中啟動並執行，現在您可以連接到角色執行個體。

1.  在 [Azure 管理入口網站]中選取 [雲端服務]****，然後選取在上述步驟 1 中部署的服務。

	![azure management portal][cloud-services]

2.  按一下 [執行個體]****，然後按一下 [生產]**** 或 [預備]****，以查看服務的執行個體。選取執行個體，然後按一下頁面底部的 [連接]****。

    ![The instances page][3]

2.  按一下 [連接]**** 時，網頁瀏覽器會提示您儲存 .rdp 檔案。如果您使用的是 Internet Explorer，請按一下 [開啟]****。

    ![prompt to open or save the .rdp file][4]

3.  檔案開啟時，會出現下列安全性提示：

    ![Windows security prompt][5]

4.  按一下 [連接]****，將會出現安全性提示，要求您輸入存取執行個體所需的認證。請輸入您在[步驟 1][Step 1: Configure the service for Remote Desktop access using Azure PowerShell] 中建立的密碼，然後按一下 [確定]****。

    ![username/password prompt][6]

連接建立後，「遠端桌面連接」會在 Azure 中顯示執行個體的桌面。您已成功取得執行個體的遠端存取，並且可執行任何必要工作來管理您的應用程式。

![Remote desktop session][7]

## <a name="step3"> </a>步驟 3：使用 Azure PowerShell 將服務設定成停用遠端桌面存取

當您不再需要透過遠端桌面連線到雲端中的角色執行個體時，請使用 [Azure PowerShell] 來停用遠端桌面存取。

1.  在 [開始]**** 功能表中，選取 [Azure PowerShell]****。

2.  切換至服務目錄，然後輸入 **Disable-AzureServiceProjectRemoteDesktop**。

3.  將服務組態變更發佈至雲端。在 **Azure PowerShell** 命令提示字元中，輸入 **Publish-AzureServiceProject**。

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
  
  [從遠端存取 Azure 中的角色執行個體]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh124107.aspx
  [透過 Azure 角色使用遠端桌面]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg443832.aspx

<!--HONumber=35.2-->
