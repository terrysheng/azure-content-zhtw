<properties linkid="dev-node-remotedesktop" urlDisplayName="Enable Remote Desktop" pageTitle="Enable remote desktop for cloud services (Node.js)" metaKeywords="Azure Node.js remote access, Azure Node.js remote connection, Azure Node.js VM access, Azure Node.js virtual machine access" description="Learn how to enable remote-desktop access for the virtual machines hosting your Azure Node.js application. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# 在 Azure 中啟用遠端桌面

遠端桌面可讓您存取在 Azure 中執行之角色執行個體的桌面。您可以使用遠端桌面連線來設定虛擬機器，或對應用程式的問題進行疑難排解。

<div class="dev-callout">
<b>注意</b>
<p>本文中的步驟僅適用於以 Azure 雲端服務的形式受到代管的節點應用程式。</p>
    </div>

此工作包含下列步驟：

-   [步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取][]
-   [步驟 2：連接到角色執行個體][]
-   [步驟 3：使用 Azure PowerShell 將服務設定成停用遠端桌面存取][]

## <a name="step1"> </a>步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取

若要使用遠端桌面，您必須使用在雲端中用以驗證角色執行個體的使用者名稱、密碼和憑證，來設定您的服務定義和服務組態。[Azure PowerShell][] 包含 **Enable-AzureServiceProjectRemoteDesktop** Cmdlet，可為您執行這項設定。

在建立服務定義的電腦中，執行下列步驟。

1.  在 [開始] 功能表中，選取 [Azure PowerShell]。

    ![Azure PowerShell start menu entry][]

2.  將目錄切換至服務目錄、輸入 **Enable-AzureServiceProjectRemoteDesktop**，然後輸入您在雲端中驗證角色執行個體時所使用的使用者名稱和密碼。

    ![enable-azureserviceprojectremotedesktop][]

3.  將服務組態變更發佈至雲端。在**Azure PowerShell** 提示下，輸入**Publish-AzureServiceProject**。

    ![publish-azureserviceproject][]

完成前述步驟後，即會將雲端中服務的角色執行個體設定為可進行遠端桌面存取。

## <a name="step2"> </a>步驟 2：連接到角色執行個體

您的部署已在 Azure 中啟動並在執行中，現在您可以連接到角色執行個體。

1.  在 [Azure 管理入口網站][]中選取 [雲端服務]，然後選取先前在步驟 1 中部署的服務

    ![azure management portal][]

2.  按一下 [執行個體]，然後按一下 [生產] 或 [執行]，以檢視服務的執行個體。選取執行個體，然後按一下頁面底部的 [連接]。

    ![The instances page][]

3.  當您按一下 [連接] 時，網頁瀏覽器會提示您儲存 .rdp 檔案。 如果您使用的是 Internet Explorer，請按一下 [開啟]。

    ![prompt to open or save the .rdp file][]

4.  檔案開啟時，會出現下列安全性提示：

    ![Windows security prompt][]

5.  按一下 [連接] 時會出現安全性提示，要求您輸入存取執行個體所需的認證。輸入您在[步驟 1][步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取] 中建立的密碼，然後按一下 [確定]。

    ![username/password prompt][]

建立連線之後，[遠端桌面連線] 即會在 Azure 中顯示執行個體的桌面。您已成功取得執行個體的遠端存取，並且可執行任何必要工作來管理您的應用程式。

![Remote desktop session][]

## <a name="step3"> </a>步驟 3：使用 Azure PowerShell 將服務設定成停用遠端桌面存取

當您不再需要透過遠端桌面連線到雲端中的角色執行個體時，可使用 [Azure PowerShell][] 停用遠端桌面存取

1.  在 [開始] 功能表中，選取 [Azure PowerShell]。

2.  將目錄切換至服務目錄，然後輸入**Disable-AzureServiceProjectRemoteDesktop**：

3.  將服務組態變更發佈至雲端。在**Azure PowerShell** 提示下，輸入**Publish-AzureServiceProject**：

## 其他資源

-   [從遠端存取 Azure 中的角色執行個體][]
-   [搭配使用遠端桌面與 Azure 角色][]

  [步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取]: #step1
  [步驟 2：連接到角色執行個體]: #step2
  [步驟 3：使用 Azure PowerShell 將服務設定成停用遠端桌面存取]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  [Azure PowerShell start menu entry]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
  [enable-azureserviceprojectremotedesktop]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
  [publish-azureserviceproject]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [azure management portal]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [The instances page]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [prompt to open or save the .rdp file]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [Windows security prompt]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [username/password prompt]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [Remote desktop session]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  [從遠端存取 Azure 中的角色執行個體]: http://msdn.microsoft.com/en-us/library/windowsazure/hh124107.aspx
  [搭配使用遠端桌面與 Azure 角色]: http://msdn.microsoft.com/en-us/library/windowsazure/gg443832.aspx
