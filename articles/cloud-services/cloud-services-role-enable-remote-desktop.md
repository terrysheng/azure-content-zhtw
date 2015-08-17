<properties 
pageTitle="設定 Azure 雲端服務中角色的遠端桌面連線" 
description="如何設定的 Azure 雲端服務應用程式以允許遠端桌面連線" 
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
ms.date="07/06/2015" 
ms.author="adegeo"/>

# 設定 Azure 中角色的遠端桌面連接
建立執行應用程式的雲端服務之後，您可以在應用程式中從遠端存取角色執行個體，以進行設定或疑難排解問題。遠端桌面必須設定雲端服務。

* 需要啟用遠端桌面驗證的憑證嗎？ [建立](cloud-services-certs-create.md)一個並進行以下設定。
* 雲端服務已經有遠端桌面設定了嗎？ [連接](#remote-into-role-instances)至雲端服務。

## 設定 web 角色或背景工作角色的遠端桌面連線
若要啟用 web 角色或背景工作角色的遠端桌面連線，您可以設定應用程式的服務模型中的連線，或者您可以使用 Azure 管理入口網站來設定執行個體執行之後的連線。

### 設定服務模型中的連線
**匯入**元素必須加入至將 **RemoteAccess** 模組和 **RemoteForwarder** 模組匯入到服務模型的服務定義檔。當您使用 Visual Studio 建立 Azure 專案時，會為您建立服務模型的檔案。

服務模型包含 [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) 檔案和 [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) 檔案。定義檔案是為部署準備雲端服務的應用程式時，和角色二進位檔一起封裝。ServiceConfiguration.cscfg 檔案會與應用程式封裝一起部署，並且由 Azure 用來決定應用程式的執行方式。

建立專案之後，您可以使用 Visual Studio [啟用遠端桌面連線](https://msdn.microsoft.com/library/gg443832.aspx)。

設定連線之後，服務定義檔應類似下列加入 `<Imports>` 元素的範例。

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```

服務組態檔應類似下列範例，該範例具有您在設定連線時所提供的值，請注意 `<ConfigurationSettings>` 和 `<Certificates>` 元素：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```

當您[封裝](cloud-services-model-and-package.md#cspkg)和[發佈](cloud-services-how-to-create-deploy-portal.md)應用程式時，您必須確定已選取 [**啟用所有角色的遠端桌面**] 核取方塊。[此](https://msdn.microsoft.com/library/ff683672.aspx)文章提供使用 Visual Studio 和雲端服務的一般相關工作清單。

### 在執行中執行個體上設定連線
在雲端服務的 [設定] 頁面上，您可以啟用或修改遠端桌面連線設定。如需詳細資訊，請參閱[設定角色執行個體的遠端存取](cloud-services-how-to-configure.md)。




## 角色執行個體的遠端存取
若要存取 web 角色或背景工作角色的執行個體，您必須使用遠端桌面通訊協定 (RDP) 檔案。您可以從管理入口網站下載檔案，或以程式設計方式擷取檔案。

### 透過管理入口網站下載 RDP 檔案

您可以使用下列步驟從管理入口網站擷取 RDP 檔案，然後使用遠端桌面連線連線到使用該檔案的執行個體：

1.  在 [執行個體] 頁面上，選取執行個體，然後按一下命令列上的 [**連線**]。
2.  按一下 [**儲存**] 以將遠端桌面通訊協定檔案儲存到本機電腦。
3.  開啟**遠端桌面連線**，按一下 [**顯示選項**]，然後按一下 [**開啟**]。
4.  瀏覽至儲存 RDP 檔案的位置，選取檔案，按一下 [**開啟**]，然後按一下 [**連線**]。請遵循指示以完成連線。

### 使用 PowerShell 取得 RDP 檔案
您可以使用 [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) cmdlet 擷取 RDP 檔案。

### 使用 Visual Studio 下載 RDP 檔案
在 Visual Studio 中，您可以使用 [伺服器總管] 建立遠端桌面連線。

1.  在 [伺服器總管] 中，展開 **Azure\\Cloud Services\\[cloud service name]** 節點。
2.  展開**預備**或**生產**。
3.  展開個別角色。
4.  以滑鼠右鍵按一下其中一個角色執行個體，按一下 [**使用遠端桌面連線...**]，然後輸入使用者名稱和密碼。

### 以程式設計的方式透過服務管理 REST API 下載 RDP 檔案
您可以使用[下載 RDP 檔案](https://msdn.microsoft.com/library/jj157183.aspx) REST 作業下載 RDP 檔案。然後，您可以使用 RDP 檔案及遠端桌面連線存取雲端服務。

## 後續步驟
您可能需要[封裝](cloud-services-model-and-package.md)或[上傳 (部署)](cloud-services-how-to-create-deploy-portal.md) 您的應用程式。

<!---HONumber=August15_HO6-->